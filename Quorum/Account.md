# account.go 
### Accounts/Struct
1. Address  - 20bytes의 이더리움으로 account의 기준이된다. 
2. URL - URL필드에 정의된 특정위치에 존재하는 이더리움 계정을 나타낸다.

### Wallet/Interface
> 동일한 시드에서 유도된 1개 이상의 어카운트를 포함하는 지갑을 나타낸다.( 결정적 지갑으로 보임)
* func URL() URL : 이 지갑이 접근 가능한 패스를 반환 , 지갑의 소팅 순서를 결정하는 기준 
* func Status() (string, error) : 지갑의 현재 상태 반환, 실패상태를 알려주는 역할도 한다. 
* func Open(...) error : 지갑 인스턴스를 오픈한다. 
* func Close() error : 리소스를 해제한다. 
* func Accounts() []Account : Signing account리스트를 반환한다. 
* func Contains(...) bool : 포함여부 확인 
* func Derive(...) (Account, error): 결정적 지갑을 특정 패스에 올린다.
* func SelfDerive(...) : ????
* func SignHash(...) ([]byte, error) : 입력으로 들어가는 해시를 이용해 지갑에 사인하도록 요청한다
* func SignTx(...) (*types.Transaction, error) :  주어진 트렌젝션에 사인하도록 지갑에 요청한다. 
* func SignHashWithPassphrase(...) ([]bytes, error) : 추가 인증을 위해서 passphase를 이용하여 서명한다 
* func SignTxWithPassphrase(...)  (*types.Transaction, error) : passphase를 활용하여 Transaction에 사인한다

### Backend/Interface
> 지갑을 제공해주며, Sign이 가능한 많은 어카운트를 소유한다, 지갑과 지갑이벤트 구독기능을 구현하였다. 
* func Wallet() []Wallet : 가지고 있는 지갑 리스트를 반환한다. 
* func Subscribe( ...chnnel ) event.Subscription : 지갑의 도착/떠남을 감지하기 위한 비동기 이벤트를 연결한다. 

### WalletEventType/const(like ENUM)
* WalletArrived
* WalletOpend
* WalletDropped

### WalletEvent/Struct 
* Wallet
* WalletEventType

# manager.go
### Manager/Struct
> 고수준의 계정 관리자로 트랜젝션 서명을 위해 다양한 백엔드와 통신한다. 
* backends :  등록되어 있는 backend instance를 갖는 map 
* updaters (?):  모든 backend instance의 업데이트를 감지하는??
* updates(channel): backend의 지갑의 상태가 바뀌었을 경우 알려주는 채널 인스턴스 
* wallets : 등록된 백엔드의 모든 지갑을 캐싱. 
* feed :  지갑이 새로 등록되거나 해제 될때 알려주는? 
* quit(?) : ????
* lock : RW Mutex