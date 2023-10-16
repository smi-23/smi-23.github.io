---
title: "[Pintos] project3 - Memory Management 구현"
excerpt: ""
categories:
    - Pintos
tags:
    - [Blog, CS, Github, Git, Github.io, Virtual Memory, Memory]
toc : true
toc_sticky: true
date: 2023-10-17
last_modified_at: 2023-10-17
---
# Memory Management 구현
## SPT(Supplemental Page Table) 구현
앞서 spt에 대해서 알아보았는데 다시 간략하게 정리해보자면 아래와 같다.
- 보조 페이지 테이블은 각 페이지에 대한 추가 데이터를 이용해서 페이지 테이블을 보조
- page fault가 발생했을 때 그 곳에 어떤 데이터가 있었어야 했는지 알아내기 위해 커널은 보조 페이지 테이블에서 폴트가 발생한 가상 페이지를 탐색
- 커널이 프로세스가 종료될 때 어떤 자원을 free할지 고르기 위해서 보조 페이지 테이블을 조사

spt 구현을 위한 다양한 자료구조
1. array - 간단한 구현 / 메모리 낭비
2. linked list - 긴 탐색 시간
3. bitmap - 구분해야 할 자료가 많을수록 복잡해짐
4. hash table - 빠른 검색 및 삽입, 동적 확장 등등

해시테이블 정리 링크 만들어서 올리기 

Pintos는 hash table data structure를 포함하고 있어 이를 선택!
```c
/* vm.h */
struct supplemental_page_table {
    struct hash hash_pt;
};
```
해당 페이지가 속해 있는 해시 테이블에 연결해주는 해시 테이블 요소로서 hash_elem을 추가
```c
/* vm.h */
struct page {
	const struct page_operations *operations;
	void *va;              /* Address in terms of user space */
	struct frame *frame;   /* Back reference for frame */

	/* 해시테이블에 기록하기 위한 elem */
	struct hash_elem hash_elem;
	bool writable;

	/* Per-type data are binded into the union.
	 * Each function automatically detects the current union */
	union {
		struct uninit_page uninit;
		struct anon_page anon;
		struct file_page file;
#ifdef EFILESYS
		struct page_cache page_cache;
#endif
	};
};
```
## Helper Function 구현
### page_hash()
```c
/* vm.c */
/* 이 함수는 주어진 페이지 p_의 해시 값을 계산합니다. 이 값은 주어진 페이지를
 * 해시 테이블의 버킷 중 하나에 매핑하는 데 사용됩니다. */
unsigned page_hash(const struct hash_elem *p_, void *aux UNUSED) {
  const struct page *p = hash_entry(p_, struct page, hash_elem);

  return hash_bytes(&p->va, sizeof(p->va));
}
```
### page_less()
```c
/* vm.c */
/* 두 페이지를 비교하여 첫 번째 페이지 a가 두 번째 페이지 b보다 앞에 있으면
 * true를 반환하고, 그렇지 않으면 false를 반환합니다. */
bool page_less(const struct hash_elem *a_, const struct hash_elem *b_,
               void *aux UNUSED) {
  const struct page *a = hash_entry(a_, struct page, hash_elem);
  const struct page *b = hash_entry(b_, struct page, hash_elem);

  return a->va < b->va;
}
```
## 주요 함수 구현
spt 설계는 위에서 말한 것과 같이 다양한 방법이 있지만 hash table을 사용
페이지 테이블은 처음 process가 생성될 때, fork로 만들어질 때
- initd()
- __do_fork

### supplemental_page_table_init()
```c
/* vm.c */
/* 새로운 보조 페이지 테이블 초기화 */
void supplemental_page_table_init(struct supplemental_page_table *spt UNUSED) {
  hash_init(&spt->hash_pt, page_hash, page_less, NULL);
}
```
### spt_find_page()
```c
/* vm.c */
/* 인자로 넘겨진 보조 페이지 테이블에서로부터 가상 주소(va)와 대응되는 페이지
 * 구조체를 찾아서 반환합니다. 실패했을 경우 NULL를 반환합니다. */
struct page *spt_find_page(struct supplemental_page_table *spt UNUSED,
                           void *va UNUSED) {
  if (spt == NULL || va == NULL) // 인자가 제대로 주어졌는지 예외 처리
    return NULL;

  // 더미 페이지를 하나 만듬
  struct page *page = (struct page *)malloc(sizeof(struct page));
  // 더미 페이지에 va를 정렬해서 넣어줌
  page->va = pg_round_down(va);

  // hash_find함수로 찾고 싶은 hash_elem을 검색해서 반환
  struct hash_elem *e = hash_find(&spt->hash_pt, &page->hash_elem);
  // 필요 없어진 페이지 할당 해제
  free(page);
  
  // 위 hash_elem을 이용해서 원하는 page 구조체를 찾고 반환
  return e != NULL ? hash_entry(e, struct page, hash_elem): NULL;
}
```
### spt_insert_page()
```c
/* vm.c */
/* 인자로 주어진 보조 페이지 테이블에 페이지 구조체를 삽입합니다. 이 함수에서
 * 주어진 보충 테이블에서 가상 주소가 존재하지 않는지 검사해야 합니다. */
bool spt_insert_page(struct supplemental_page_table *spt UNUSED,
                     struct page *page UNUSED) {
  int succ = false;
  // va와 대응되는 page structure가 없다면 spt에 page를 삽입
  if (spt_find_page(spt, page->va) == NULL) {
    hash_insert(&spt->hash_pt, &page->hash_elem);
    succ = true;
  }
  return succ;
}
```
## Frame Management
### vm_get_frame
```c
/* vm.c */
/* palloc()와 프레임을 가져옵니다. 사용 가능한 페이지가 없으면 페이지를
 * 대체하고 반환합니다. 이 함수는 항상 유효한 주소를 반환합니다. 즉, 사용자 풀
 * 메모리가 가득 찬 경우, 이 함수는 사용 가능한 메모리 공간을 얻기 위해 프레임을
 * 대체합니다. */
static struct frame *vm_get_frame(void) {
  struct frame *frame = (struct frame *)malloc(sizeof(struct frame));
  // user pool에서 새로운 물리 주소를 가져온다
  frame->kva = palloc_get_page(PAL_USER | PAL_ZERO);
  if (frame == NULL || frame->kva == NULL) {
    free(frame);
    PANIC("todo");
  }
  // 할당된 프레임을 초기화
  frame->page = NULL; // 페이지 연결 초기화


  ASSERT(frame != NULL); // 프레임이 할당되지 않은 경우 예외 처리
  ASSERT(frame->page == NULL); // 프레임에 이미 페이지가 연결된 경우 예외 처리
  return frame;
}
```
### vm_claim_page()
```c
/* vm.c */
/* 주어진 가상 주소(va)에 해당하는 페이지를 할당하고, 해당 페이지를 사용 가능한
 * 물리 메모리 프레임에 매핑하는 역할을 합니다. */
bool vm_claim_page(void *va UNUSED) {
 
  struct thread *curr = thread_current();
  struct page *page = spt_find_page(&curr->spt, va);

  if (page == NULL) // 해당하는 페이지를 찾지 못했을 경우 예외 처리
    return false;

  // 페이지를 실제로 할당하고 MMU 설정
  return vm_do_claim_page(page);
}
```
### vm_do_claim_page()
```c
/* vm.c */
/* 페이지를 할당하고 MMU(Memory Management Unit)를 설정합니다.

 * 페이지를 물리 프레임에 할당한 후, 페이지 테이블 엔트리를 업데이트하여 해당
 * 페이지의 가상 주소와 물리 주소 간의 매핑을 설정합니다. 이렇게 하면 페이지가 실제
 * 물리 메모리에 매핑되고, 프로세스가 해당 페이지에 접근할 수 있게 됩니다. */
static bool vm_do_claim_page(struct page *page) {
  /* 프레임을 할당합니다. */
  struct frame *frame = vm_get_frame();
  struct thread *curr = thread_current();

  /* 프레임과 페이지를 서로 연결합니다. */
  frame->page = page;
  page->frame = frame;

  uint64_t *my_pml4 = curr->pml4;

  void *va = page->va;
  void *pa = frame->kva;

  if (!pml4_set_page(my_pml4, page->va, frame->kva, true)) {
    frame->page = NULL; // 페이지 연결 해제
    page->frame = NULL; // 프레임 연결 해제
    return false;
  }

  /* 페이지를 스왑인(swap in)하고 페이지를 프레임의 KVA로 복사합니다. */
  return swap_in(page, frame->kva);
}
```
### What I should do
현재 gitbook을 보면서 순서대로 구현 => 위 함수에서 모자란 부분을 채워 넣어야함

 **Reference**<br>
<a href="https://e-juhee.tistory.com/entry/Pintos-KAIST-Project-3-Memory-Management">https://e-juhee.tistory.com/</a>