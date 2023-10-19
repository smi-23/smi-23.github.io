---
title: "[Pintos] project3 - Anonymous Page 구현"
excerpt: ""
categories:
    - Pintos
tags:
    - [Blog, CS, Github, Git, Github.io, Virtual Memory, Memory]
toc : true
toc_sticky: true
date: 2023-10-19
last_modified_at: 2023-10-19
---
# Anonymous Page 구현
> anonymous page는 file-backed page와 달리 contents를 가져올 filedlsk device가 없는 page를 말함 

- 이들은 프로세스 자체에서 런타임 때 만들어지고 사용됨
- 주로 프로세스의 동적 메모리 할당 및 스택 및 힙을 나타내는 데 사용

## load segment()
1. 이 함수는 프로세스가 실행될 때 실팽 파일을 현재 스레드로 로드하는 함수인 load함수에서 호출됨
2. 파일의 내용을 upage에 로드하는 함수로 upage를 할당하기 위한 page가 필요함
3. 위에서 수정한 vm_alloc_page_with_initializer함수를 호출해서 페이지를 생성
4. lazy_loading을 사용해야 하므로 여기서 바로 파일의 내용을 로드하지 않음
5. 따라서 page에 내용을 로드할 때 사용할 함수와 필요한 인자들을 넣어줘야 함
6. vm_alloc_page_with_initializer함수의 네 번째, 다섯 번째 인자가 각각 로드할 때 사용할 함수, 그때 필요한 인자
내용을 로드할 때 사용할 함수는 lazy_load_segment를 만들어 사용하고, 인자는 aux에 직접만들어서 넘겨줌
- file 내용이 담긴 파일 객체
- ofs 이 페이지에서 읽기 시작할 위치
- read_bytes 이 페이지에서 읽어야 하는 바이트 수
- zero_bytes 이 페이지에서 read_bytes만큼 읽고 공간이 남아 0으로 초기화해야 하는 바이트 수
- lazy_aux 라는 구조체를 새로 정의해서 선언하고 안에 위 정보들을 담아서 vm_alloc_page_with_initializer함수의 마지막 인자로 전달

7. page_fualt가 처음 발생하면 lazy_load_segment가 실행되고 lazy_aux가 인자로 사용되어 내용이 로딩됨

```c
/* 이 함수는 파일에서 데이터를 읽어와 가상 메모리에 로드하며, 해당 데이터를
 * 페이지 크기에 맞게 나누어 초기화합니다. 파일 데이터와 0으로 초기화된 부분을
 * 올바르게 가상 주소에 매핑하고, 해당 페이지가 쓰기 가능한지 여부에 따라 적절한
 * 플래그를 설정합니다. 마지막으로 모든 페이지를 초기화할 때까지 반복합니다.*/
static bool load_segment(struct file *file, off_t ofs, uint8_t *upage,
                         uint32_t read_bytes, uint32_t zero_bytes,
                         bool writable) {
  // 페이지 크기(PGSIZE)로 정확하게 초기화되었는지 확인
  ASSERT((read_bytes + zero_bytes) % PGSIZE == 0);
  // upage의 오프셋이 0인지 확인
  ASSERT(pg_ofs(upage) == 0);
  // 파일 오프셋(ofs)이 페이지 크기(PGSIZE)로 정확하게 정렬되었는지 확인
  ASSERT(ofs % PGSIZE == 0);
  off_t read_start = ofs;

  while (read_bytes > 0 || zero_bytes > 0) {
    /* 이 페이지를 어떻게 채울지 계산합니다.
     * FILE에서 PAGE_READ_BYTES 바이트를 읽고
     * 나머지 PAGE_ZERO_BYTES 바이트는 0으로 채웁니다. */
    size_t page_read_bytes = read_bytes < PGSIZE ? read_bytes : PGSIZE;
    size_t page_zero_bytes = PGSIZE - page_read_bytes;

    // lazy_loading을 위해 필요한 lazy_aux란 구조체를 만듬
    struct lazy_aux *aux = (struct lazy_aux *)calloc(1, sizeof(struct lazy_aux));
    if (aux == NULL) 
      return false;

    aux->file = file; // 내용이 담긴 파일 객체
    aux->ofs = read_start; // 이 페이지에서 읽기 시작할 위치
    aux->read_bytes = page_read_bytes; // 이 페이지에서 읽어야 하는 바이트 수
    aux->zero_bytes = page_zero_bytes; // 이페이지에서 read_bytes만큼 읽고 0으로 채워야 하는 남은 공간의 바이트 수
    aux->writable = writable; // 이 페이지가 쓰기 가능인지 아닌지 여부

    // 가상 메모리에 페이지를 할당, 페이지 초기화 함수(lazy_load_segment)전달
    if (!vm_alloc_page_with_initializer(VM_ANON, upage, writable,
                                        lazy_load_segment, aux))
      return false;

    /* 다음 반복문을 위해 읽은 만큼 값을 갱신 */
    read_bytes -= page_read_bytes;
    zero_bytes -= page_zero_bytes;
    read_start += page_read_bytes;
    upage += PGSIZE;
  }
  return true;
}
```
## lazy_load_segment()
1. 이 함수는 실행 파일의 내용을 페이지로 로딩하는 함수이며 첫번째 page fault가 발생할 때 호출
2. 이 함수가 호출되기 이전에 물리 프레임 매핑이 진행되므로, 여기서는 물리 프레임에 내용을 로딩하는 작업만 하면 됨

```c
static bool lazy_load_segment(struct page *page, void *aux) {

  struct thread *curr = thread_current();
  // aux 구조체를 형변환하여 사용합니다.
  struct lazy_aux *my_if = (struct lazy_aux *)aux;

  // 파일과 오프셋 정보 가져오기
  struct file *file = my_if->file;
  off_t ofs = my_if->ofs;

  // 읽어와야 할 바이트 수, 초기화할 바이트 수, 페이지 쓰기 기능 여부 가져오기
  uint32_t read_bytes = my_if->read_bytes;
  uint32_t zero_bytes = my_if->zero_bytes;
  bool writable = my_if->writable;

  if (page == NULL)
    return false;

  // 페이지의 프레임 정보를 가져오기
  uint8_t *kpage = page->frame->kva;
  if (kpage == NULL)
    return false;

  // 파일에서 오프셋 위치로 이동
  file_seek(file, ofs);

  /* 이 페이지를 파일로부터 읽어옵니다. */
  if (file_read(file, kpage, read_bytes) != (int)read_bytes) {
    // 파일에서 읽은 바이트 수와 읽어와야 할 바이트 수를 비교하여
    // 파일 읽기에 실패한 경우 페이지를 해제하고 false 반환
    vm_dealloc_page(page);
    return false;
  }

  /* 남은 바이트가 있다면 0으로 초기화합니다. */
  if (zero_bytes > 0) 
    memset(kpage + read_bytes, 0, zero_bytes);

  // aux 구조체를 해제하여 메모리 누수 방지
  free(aux);
  return true;
}
```

## setup_stack()

setup_stack 함수의 역할은 프로세스가 실행될 때 사용할 스택 페이지를 미리 생성하는 것입니다. 이 스택 페이지는 프로세스가 시작될 때 필요한 공간을 가지며, 프로세스가 실행되기 전에 준비됩니다. 따라서 "느긋한 로딩"이 필요하지 않습니다. 이 함수는 초기 스택 페이지를 만들고, 프로세스가 실행되면 이 스택 페이지를 사용할 수 있도록 설정합니다.

원래 setup_stack 함수는 물리 메모리에 스택 페이지를 할당하고 초기화합니다. 이것은 프로세스가 시작할 때 필요한 준비를 마칩니다.

그러나 "느긋한 로딩"을 고려할 때, 스택 페이지를 생성할 때 스택 데이터를 미리 파일에서 읽어와야 할 때도 있습니다. 이 경우 "느긋한 로딩"을 사용하여 필요한 데이터를 필요한 때에만 읽어옵니다. 그러나 초기 스택 페이지는 이러한 파일 데이터가 필요하지 않고, 프로세스가 시작될 때 바로 사용되기 때문에 "느긋한 로딩"이 필요하지 않습니다. 따라서 setup_stack 함수는 물리 메모리에 스택 페이지를 할당하고 초기화하여 프로세스가 잘 시작할 수 있도록 준비합니다.

```c
/* USER_STACK의 시작 부분에 스택 페이지를 생성합니다. 성공하면 true를
 * 반환합니다. */
static bool setup_stack(struct intr_frame *if_) {
  bool success = false;
  void *stack_bottom = (void *)(((uint8_t *)USER_STACK) - PGSIZE);

  // stack_bottom에 페이지를 하나 할당 받음
  if (vm_alloc_page(VM_ANON, stack_bottom, true)) {
    // 할당 받은 페이지에 바로 물리 프레임을 매핑
    if (vm_claim_page(stack_bottom)) {
      // rsp를 변경 => argument_stack에서 이 위치부터 인자를 push하기 때문
      if_->rsp = (uint64_t)USER_STACK;
      success = true;
    }
  }
  return success;
}
```

 **Reference**<br>
<a href="https://e-juhee.tistory.com/entry/Pintos-KAIST-Project-3-Memory-Management">https://e-juhee.tistory.com/</a>