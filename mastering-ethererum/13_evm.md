### What is the EVM ?

- Smart contract을 네트워크에 적용하고, 실행 시키는 역할을 담당
- 단순히 vlaue만을 전송 할 경우 필요 없다.
- 이외의 모든 것은 EVM을 통해 진행되며 이 분산 컴퓨팅 시스템은 각각의 데이터 저장소도 갖는다.

- EVM 은 Stack기반 구조이다.
- 256bit 주소 크기기반으로 동작

### Comparison with Exsting Tech

- EVM 기존 VM(Virtualbox, KVM...)보다 제한적인 도메인에서 사용 된다.
- 큰 관점에서 봤을 때 EVM은O OS나 platform의 제한을 최소화하고 공통된 환경을 제공하는 것을 목표로 한다.
- 기존의 JVM이나 .NET F/W처럼 해당 Framework에서 작동 할 수 있게 해주는 bytecode를 추가하여 컴파일하게 되고, 실행시 해당 bytecode를 작동 시킨다.

- 특이한 점은 별도의 "Scheduler"가 내장되어 있지 않다.
  > 외부의 검증이 완료된 transaction을 기반으로 조작되기 때문에 필요없다.

### EVM Instruction Set (bytescode operations)

- 수학 연산과 로직 연산
- 실행된 컨텍스트 조회
- 스택, 메모리 및 스토리지 엑세스
- 흐름 조작 제어
- Logging, calling과 기타 동작

**해당 사항 외에도 EVM은 account, block 정보들에도 엑세스 할수 있다.**

- Arithmetic opcodes
  > ADD, MUL, SUB, DIV, SDIV, MOD, SMOD, ADDNOD, MULMOD, EXP, SIGNEXTEND, SHA3
- Stack opcodes
  > POP, MLOAD, MSTORE, MSTORE8, SLOAD, SSTORE, MSIZE, PUSHx, DUPx, SWAPx
- Process flwo opcodes
  > STOP, JUMP, JUMPI, PC, JUMPDEST
- System opcodes
  > LOGx, CREATE, CALL, CALLCODE, RETURN, DELEGATECALL, INVALIDE, SELFDESTURCT
- Logic operation
  > LT, GT, SLT, SGT, EQ, ISZERO, AND, OR, XOR, NOT, BYTE
- Env codes
  > GAS, ADDRESS, BALANCE, ORIGIN, CALLER, CALLVALUE, CALLDATALOAD, ...
