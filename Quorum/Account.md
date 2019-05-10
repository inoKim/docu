#abi

### abi/abi.go

> 왜 account package에 들어있는지 ?

### ABI/Struct

1. Constructor - abi method
2. Methods map[string]Method
3. Events map[string]Event

- func JSON() : 입력된 abi를 JSON으로 리턴
- func Pack() : 주어진 메소드를 abi에 매핑한다(?)
- ...
- func MethodByID() : 메소드의 signiture가지고 메소드 내용을 가져온다.

### abi/method.go

> ABI codec에 들어가는 부분으로 method부분 담당

#### Method/Struct

1. Name string
2. Const (bool)
3. Inputs (Arg)
4. OUtputs (Arg)

- func Sig() : 해당 Method의 Signature 리턴
- func String() : ToString()
- func Id() : Keccak256(SHA-256) hash return.
-

### abi/Event.go

#### Event/Struct

1. Name string
2. Anonymous bool
3. Inputs Arguments

## abi/argument.go

#### Argument/Struct

> 파라미터 관련 구조체

### abi/numbers.go

> 파라미터 타입 중 int 관련 내용

### abi/pack.go , abi/unpack.go

> 입력 받은 abi를 Wrapper class(C++)등으로 패킹, 언패킹 하는 부분

###abi/bind/bind.go

> Java, C++ , python 등에서 abi를 사용 할 수 있게 binding 해준다.

---

# keystore

### keysotre/keystore.go

#### KeyStore/Struct

1. storage keyStore : 백엔드가 저장 되어있음(clearText/enc data)
2. cache \*accountCache : in-mem 캐시,
3. changes chan struct {}
4. unlocked map[common.Address]]\*unlocked
5. wallets []accounts.Wallet
6. updateFeed event.Feed
7. updateScope event.SubscriptionScope
8. updating bool
9. mu sync.RWMutex

- func NewKeyStore() : keystore 생성 입력된 디렉토리 바탕
- func NewPlaintextKeyStore() : 암호화 되지 않은 키스토어 생성
- func init() : Initialize , keystore dir 세팅
- func Wallets() : keystore에서 wallet을 복호화해서 accounts.Backend에 포함된 wallet리스트에 추가 하여 리턴한다
- func refreshWallet() : Wallet 리스트를 업데이트하고, 업데이트된 이벤트를 keysotre의 feed로 이벤트 처리한다
- func Subscribe() : 구독하기 ㅋㅋ
- func updater() : 주기적으로 해당 키스토어에 저장된 내용을 업데이트한다.
- func HasAddress() : key가 주소를 가지는지 확인한다
- func Accounts() : 디렉토리에 있는 Account가 가진 모든키를 반환한다
- func Delete() : Passphase에 해당하는 account를 삭제한다
- func SignHash() : 입력받은 hash에 ECC(sign)을 진행한다
- func SignTx() : 트렌젝션에 사인한다
- func SignHashWithPassphrase() : Passphase를 활용하여 입력받은 해시에 ECC 사인한다
- func Unlock() : 사용할 Account를 unlock한다(passphase활용)
- func Lock() : 메모리에서 해당 account의 privatekey를 삭제한다
- func TimedUnlock() timeout기간 동안만 unlock한다
- func Find() : account cache에서 입력받은 account를 찾는다
- func getDecryptedKey() : account에서 key를 가져온다(Pirvate/Public 어떤건지 구분안됨)
- func expire() : 키를 소멸시킨다 (더 봐야함)
- func NewAccount() : passphase를 입력받아서 새로운 account를 만든다
- func Export () : 새로운 키를 이용해서 JSON형태로 export 한다
- func Import() : 입력받은 passphase를 활용해서 JSON에서 in-mem에 로드한다 -> importKey()
- func ImportECDSA() : passphase를 활용해서 ECC 암호화를 풀어서 in-mem에 로드한다 -> importKey()
- func importKey() : Key와 passphase를 이용해서 in-mem에 account를 불러온다
- func Update() : 현재 account의 passphase를 update
- func ImportPreSaleKey() : 이더리움 presale wallet을 로드하여 저장한다-> 이런게 있나봄
- func zeroKey() : 키가 저장된 비트들을 0으로 세팅

### fileCache.go

#### fileCach/Struct

1.all \*set.SetNonTS : 키 저장소 파일 세트

2. lastMod time.Time : 마지막 파일 업데이트 시각
3. mu sync.RWMutex

- func scan() : 입력받은 keyDir에 새로운 키 스캔을 수행하고 이미 존재하는 디렉토리와 비교한다
- func skipKeyFile() : ...?

### key.go

#### Key/Struct

1. ID uuid.UUID
2. Address common.Addres
3. PrivateKey \*ecdsa.PrivateKey

#### keyStore/interface

- func GetKey() : 디스크로부터 불러서 복호화 한다
- func SetKey() : 암호화해서 디스크에 저장한다
- func JoinPath() : ...?

---

# account.go

### Accounts/Struct

1. Address - 20bytes의 이더리움으로 account의 기준이된다.
2. URL - URL필드에 정의된 특정위치에 존재하는 이더리움 계정을 나타낸다.

### Wallet/Interface

> 동일한 시드에서 유도된 1개 이상의 어카운트를 포함하는 지갑을 나타낸다.( 결정적 지갑으로 보임)

- func URL() URL : 이 지갑이 접근 가능한 패스를 반환 , 지갑의 소팅 순서를 결정하는 기준
- func Status() (string, error) : 지갑의 현재 상태 반환, 실패상태를 알려주는 역할도 한다.
- func Open(...) error : 지갑 인스턴스를 오픈한다.
- func Close() error : 리소스를 해제한다.
- func Accounts() []Account : Signing account리스트를 반환한다.
- func Contains(...) bool : 포함여부 확인
- func Derive(...) (**Account**, error): 결정적 지갑을 특정 패스에 올린다.
- func SelfDerive(...) : ????
- func SignHash(...) ([]byte, error) : 입력으로 들어가는 해시를 이용해 지갑에 사인하도록 요청한다
- func SignTx(...) (\*types.Transaction, error) : 주어진 트렌젝션에 사인하도록 지갑에 요청한다.
- func SignHashWithPassphrase(...) ([]bytes, error) : 추가 인증을 위해서 passphase를 이용하여 서명한다
- func SignTxWithPassphrase(...) (\*types.Transaction, error) : passphase를 활용하여 Transaction에 사인한다

### Backend/Interface

> 지갑을 제공해주며, Sign이 가능한 많은 어카운트를 소유한다, 지갑과 지갑이벤트 구독기능을 구현하였다.

- func Wallet() []Wallet : 가지고 있는 지갑 리스트를 반환한다.
- func Subscribe( ...chnnel ) event.Subscription : 지갑의 도착/떠남을 감지하기 위한 비동기 이벤트를 연결한다.

### WalletEventType/const(like ENUM)

- WalletArrived
- WalletOpend
- WalletDropped

### WalletEvent/Struct

- Wallet
- WalletEventType

# manager.go

### Manager/Struct

> 고수준의 계정 관리자로 트랜젝션 서명을 위해 다양한 백엔드와 통신한다.

- backends : 등록되어 있는 backend instance를 갖는 map
- updaters (?): 모든 backend instance의 업데이트를 감지하는??
- updates(channel): backend의 지갑의 상태가 바뀌었을 경우 알려주는 채널 인스턴스
- wallets : 등록된 백엔드의 모든 지갑을 캐싱.
- feed : 지갑이 새로 등록되거나 해제 될때 알려주는?
- quit(?) : ????
- lock : RW Mutex

- func Close() : 계정 관리자의 내부 알람 프로세스를 종료
- func update() : 백엔드로부터 받아오는 데이터 캐시들을 업데이트
- func Backends() : 백엔드를 반환한다
- func Wallets() : Manager에 등록된 지갑들을 반환
- func Wallet() / func Find()
- func merge() 이미 정렬된 지갑 리스트에 새로운 지갑을 추가 한다.(하나로 만드는게 아니고 추가한다)
- func drop() : merge <-> drop

#url.go

### URL/Struct

> 계정의 백엔드를 구분하기 위한 프로토콜

- Scheme/string
- Path/string
  > 많은 함수들이 있지만 통상적으로 파싱/비교 등등 ...
