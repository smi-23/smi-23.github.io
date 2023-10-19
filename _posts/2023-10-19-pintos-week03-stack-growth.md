---
title: "[Pintos] project3 - Stack Growth 구현"
excerpt: ""
categories:
    - Pintos
tags:
    - [Blog, CS, Github, Git, Github.io, Virtual Memory, Memory]
toc : true
toc_sticky: true
date: 2023-10-20
last_modified_at: 2023-10-20
---
# Stack Growth 구현

- project2에선 USER_STACK이 시작인 단일 페이지로 제한
- project3에선 접근한 가상 주소에 매핑된 frame이 없어서 page fault가 발생한 경우 중에서 접근한 가상 주소가 stack 영역 내에 존재할 경우 추가페이지를 할당

## vm_try_handle_fault()

### 구현 흐름
- 현재 스레드의 보조 페이지 테이블을 얻어옵니다.
- 주어진 주소를 페이지 경계로 반올림하여 정렬합니다.
- 주어진 주소가 유저 영역 내에 있는지 확인합니다.
- 유저 스택의 시작과 끝을 계산합니다.
- 페이지 테이블에서 해당 페이지를 찾습니다. 페이지가 없다면 스택 확장이 필요한지 확인합니다.
- 스택 확장이 필요하다면 스택 확장 함수를 호출하여 스택을 확장합니다.
- 페이지가 있으면 해당 페이지의 쓰기 가능 여부를 확인하고, 요청한 작업이 가능한지 검사합니다.
- 스택 확장 후, 페이지를 다시 검색하여 페이지를 클레임합니다.

```c
bool vm_try_handle_fault(struct intr_frame *f UNUSED, void *addr UNUSED,
                         bool user UNUSED, bool write UNUSED,
                         bool not_present UNUSED) {
  // 현재 스레드의 보조 페이지 테이블을 가져옵니다.
  struct supplemental_page_table *spt = &thread_current()->spt;
  
  // 주어진 가상 주소를 페이지 경계로 반올림하여 정렬합니다.
  void *round_va = pg_round_down(addr);
  struct page *page = NULL;

  // 주어진 주소가 유저 영역 내에 있는지 확인합니다.
  // KERN_BASE는 커널 영역의 끝을 나타내며, addr이 이보다 위에 있어야 합니다.
  if (addr < USER_STACK && addr >= KERN_BASE)
    return false;

  // 유저 스택의 시작과 끝을 계산합니다. (1MB 크기의 스택)
  void *stack_start = USER_STACK;
  void *stack_end = stack_start - (1 << 20);

  // 주어진 주소를 페이지 테이블에서 찾습니다.
  page = spt_find_page(spt, round_va);

  if (page == NULL) {
    // 페이지가 페이지 테이블에 없는 경우, 스택 확장이 필요한지 확인합니다.
    if (round_va >= stack_end && round_va < stack_start &&
        f->rsp == (uint64_t)addr) {
      // 스택 확장 함수를 호출하여 스택을 확장합니다.
      vm_stack_growth(round_va);
    } else {
      // 페이지가 없고 스택 확장이 필요하지 않은 경우 또는
      // 주어진 조건을 충족하지 않는 경우 페이지 폴트를 처리할 수 없습니다.
      return false;
    }
  } else {
    if (page->writable == false && write == true) {
      // 페이지는 쓰기가 불가능하면서 쓰기 작업(write)을 시도한 경우,
      // 페이지 폴트를 처리할 수 없습니다.
      return false;
    }
  }

  // 스택 확장 후, 페이지를 다시 검색하여 페이지를 클레임합니다.
  page = spt_find_page(spt, round_va);

  // 페이지 클레임을 시도하고 성공 여부를 반환합니다.
  return vm_do_claim_page(page);
}
```

## vm_stack_growth()
```c
static void vm_stack_growth(void *addr UNUSED) {
  // VM_ANON 타입의 페이지를 할당하여 스택을 확장합니다.
  vm_alloc_page(VM_ANON, addr, true);
}
```

 **Reference**<br>
<a href="https://e-juhee.tistory.com/entry/Pintos-KAIST-Project-3-Memory-Management">https://e-juhee.tistory.com/</a>