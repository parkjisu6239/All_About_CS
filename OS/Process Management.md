# Process Management



## Process Creation

Copy-on-write (COW)

- 부모 프로세스가 자식 프로세스를 생성
- 프로세스의 트리(계층 구조) 형성
- 프로세스는 자원을 필요로 함
  - 운영체제로부터 받는다
  - 부모와 공유한다
- 자원의 공유
  - 부모와 자식이 모든 자원을 공유하는 모델
  - 일부를 공유하지 않는 모델
  - 전혀 공유하지 않는 모델
- 수행(Excuition)
  - 부모와 자식이 공존하며 수행되는 모델
  - 자식이 종료(terminate) 될 때까지 부모가 기다리는 모델(wait)
- 주소공간(Address space)
  - 자식은 부모의 공간을 복사함(binary and OS data)
  - 자식은 그 공간에 새로운 프로그램을 올림
- 유닉스의 예
  - `fork()` : 시스템 콜이 새로운 프로세스를 생성
    - 부모를 그대로 복사(OS data except PID + binary)
    - 주소 공간 할당
  - fork 다음에 이어지는 `exec()` (덮어씌우기) 시스템 콜을 통해 새로운 프로그램을 메모리에 올림



## Process Termination

- 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌(`exit`)

  - 자식이 부모에게 output data를 보냄(via `wait`)

  - 프로세스의 각종 자원들이 운영체제에게 반납됨

- 부모 프로세스가 자식의 수행을 종료시킴(`abort`)

  - 자식이 할당 자원의 한계치를 넘어섬
  - 자식에게 할당된 태스크가 더 이상 필요하지 않음
  - 부모가 종료(exit)하는 경우
    - 운영체제는 부모프로세스가 종료하는 경우 자식이 더 이상 수행되도록 두지 않는다
    - 단계적 종료



## fork() 시스템 콜

A process is created by the fork() system call.

- Creates a new address space that is a duplicated of the caller
- `fork()` 를 만나면, 복제생성을 통해 자식 프로세스를 생성한다.
- 이때 자식 프로세서의 PC는 메인 함수의 가장 위가 아니라, 부모의 직전 실행 부분(fork() 바로 아래 부분) 
- 부모, 자식이 서로 다른 PID를 갖고, 그에 따라 서로 다른 return value를 가진다.

![image-20220104222001569](Process%20Management.assets/image-20220104222001569.png)



## exec() 시스템콜

A process can execute a differnet program by the `exec()` system call.

- Replace the memory image of the caller with a new program

- Fork()는 복제 후 부모 프로세스와 동일한 코드로 수행된지만, exec() 하면 완전히 새로운 코드가 덮어씌워진다.
- 메인 함수의 가장 윗부분부터 새롭게 시작된다.
- `exec`은 되돌릴 수 없다. 이미 새로운 프로그램므로 덮어씌워졌기 때문에 exec 이후의 코드도 실행되지 않는다.

![image-20220104222402960](Process%20Management.assets/image-20220104222402960.png)



## wait() 시스템 콜

프로세스 A가 `wait()` 시스템 콜을 호출하면

- 커널은 child가 종료될 때까지 프로세스 A를 sleep시킨다(block 상태)
- Child process가 종료되면 커널은 프로세스 A를 깨운다(ready 상태)
- 자식이 종료될 때까지 기다리는 프로세스
- Ex) 쉘도 하나의 프로그램이고, input을 받아서 특정 프로그램을 실행시키면 그동안 쉘은 block 상태가 된다.

![image-20220104223109767](Process%20Management.assets/image-20220104223109767.png)



## exit() 시스템 콜

프로세스의 종료를 의미한다.

**자발적 종료**

- 마지막 statement 수행 후 `exit()` 시스템 콜 호출
- 프로그램에 명시적으로 적어주지 않아도 main 함수가 리턴되는 위치에 컴파일러가 넣어줌(괄호 끝날 때)



**비자발적 종료**

- 부모 프로세스가 자식 프로세스를 강제 종료시킴
  - 자식 프로세스가 한계치를 넘어서 자원 요청
  - 자식에게 할당된 태스크가 더 이상 필요하지 않음
- 키보드로 `kill`, `break`등을 친 경우
- 부모가 종료하는 경우
  - 부모 프로세스가 종료하기 전에 자식들이 먼저 종료됨











____

**Reference**

- https://core.ewha.ac.kr/publicview/C0101020140321144554159683?vmode=f
