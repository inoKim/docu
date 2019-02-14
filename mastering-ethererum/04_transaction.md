# Transaction
----

* Ethereum 네트워크의 모든 시작은 Transaction이다.
* 그 어떤 요소라도 심지어 Contract 일지라도 transaction으로 부터 시작 되어 동작한다. 

#### Structure of Transaction
* 모든 Transaction은 serialize된 데이터로 움직인다.
* serialize된 데이터는 아래 항목들을 포함한다.
    * Nonce 
      * 메시지 재사용을 막기 위한 순서 값 
    * Gas Price
      * Transaction을 confirm하는데 사용하는 수수료
    * Gas Limit
      * 사용할 gas의 최대 값
    * Recipient
      * 목적지 이더리움 주소 
    * Value
      * 목적지로 보내는 값
    * Data
      * transaction에 포함 할 가변 데이터 
    * v,r,s
      * EOA의 ECDSA 전자 서명의 세 가지의 컴포넌트 

* Transaction의 메시지 구조는 ETH의 단순/완벽한 데이터 직렬화를 위해 만들어진 RLP인코딩 체계를 사용하여 직렬화한다. https://ihpark92.tistory.com/47

## The Transaction Nonce
1. Scalar 값이며, 현재 주소에서 보내진 횟수를 나타낸다. 
2. Nonce값은 주소로 부터 나온다. 하지만, Ethereum 네트워크에는 명식적으로 저장되지 않는다.
* 예시 케이스가 있다. 트렌젝션 두개 발생 했지만 계좌에 돈이 모자른 경우에는 순서에 따라 처리 되지 않으면 두 트렌젝션 모두가 reject 된다. 또한 nonce값이 없으면 모든 transaction을 복사해서 당신의 계좌를 공격할 수 있기 때문에 nonce값을 넣어 모든 transaction을 unique하게 만든다.

* Nonce값은 zero-base로 구성되어 있다. 
* web3.eth.getTransactionCount를 이용하여 Nonce값을 구할 때는 pending 상태인 transaction이 반영되지 않는다, 때문에 여러개의 transaction을 연속으로 보낼 때는 주의해야 한다. 
* 위와 같은 이유들 때문에 Application을 구성할 때에는 getTransactionCount()에 의존해서는 안된다. 

#### Nonce의 차이, 복제, 확인 
- 이더리움 네트워크의 transaction은 nonce값을 기반으로 순서대로 진행된다. 
- If nonce1의 transaction이 전송되고 이후에 nonce3의 값이 전송된다면 네트워크에서는 잃어버린 nonce2를 찾을 때 까지 기다리게 된다.
> 이렇게 되면 불필요한 network의 상의 gap이 생긴다. 
- 기다리던 nonce값을 가진 transaction이 node에 도착하면 다시 시작하고 nonce2번이 도착한 다음에 3번의 nonce값을 가진 transaction이 유효해진다. 
> 한번 전송된 것에 대해서 유효한 재전송을 할 수 없음을 유의하자!

##### 같은 Nonce값을 가진 transaction
- 같은 값을 가진 transaction이 동시에 출발할 경우 어떤 것이 confirm block에 포함될지는 랜덤하다. 
- 동시성(concurrency)을 갖는 구조로 netwrok의 요소들(nodes, Dapp)이 구성되어 있지만 합의체의 구조는 singleton으로 동작하기 때문에 일관성을 유지할 수 있다. 

## Transaction Gas
- Ethereum 네트워크에서 사용하는 연료, ether와 다른 것이다(?) 
- DOS공격이나, 의도되지 않은 많은 리소스를 잡아먹는 현상들에 대한 방책으로 제시되었다.
> gas fee를 내야 transaction을 일으킬 수 있다고하면 공격하려는 사람들이 지속적인 공격을 할때 마다 gas를 내야 하기 때문에 쉽지 않다. 
- gas 값은 변동성이 큰 ethereum값과 분리되어 운영되고 있으며, 메모리, 계산량에 따라 결정된다. 
- Gwei 단위
- ETH gas station이라는 사이트에서 현재 가장 적절한 가스량을 보여준다. 
> 물론 가스 값이라는게 그 시점에 정해지는게 아니고, miner들이 선택을 하기 때문에 더 적게, 더 많이 넣어도 block에 적용은 된다. 다만 적용에 걸리는 시간이 다르거나 너무 적을경우 block에 영영 못담기는 경우도 발생한다. 

> 이더리움에서 최소 가스 값은 0이다. 최소 값을 지정해 놓지 않았기 때문에 0이라도 transcation이 성공 할 수도, 실패 할 수도있다.

*Ether를 EOA에서 EOA로 전송하는 단순 작업은 21000으로 gaslimit이 정해져있다.*

- Smart contract에 transaction을 일으키는 경에는 가스 값을 측정할 수 있지만, 코드의 복잡성(실행 시점에 contract에 적용되는 내용들이 gas estimate시점과 다를 수 있다)과 실행경로에 따라 다른 가스 소모량을 가지기 때문에 측정된 값은 정확하지 않을 수 있다. 


## Transaction Recipient
* 트렌젝션 특정되어있고, 20bytes의 ethereum address이다.
* 해당 Recipient는 EOA나 contract주소가 될 수도 있다.

* 이더리움에서는 20bytes의 address라는 것 만으로 더이상의 유효성 검증은 하지 않는다. 20bytes라면 유효한 주소로 간주되어 실제 전송이 이루어진다. 

* 때문에 잘못된 전송으로 발생한 dummy ether에 관련되서는 영원히 엑세스 할 수 없게된다. 

## Transaction Value and Data 
* Transaction에는 Value 나 Data 두가지 모두 혹은 아무것도, 둘 중 하나만도 파라미터로 설정 할 수 있다.

#### Transmittin value to EOAs and Contracts
* Ethereum에서 발생한 transaction은 받는 주소가 EOA인지 Contract에 따라 다르게 동작한다.
> EOA : contract라고 flagging되어 있지 않은 모든 주소로 보냈을 경우 해당 계좌의 balance등이 변화하고 합의를 통해 confirm된다. 
> Contract : EVM에서 컨트렉을 실행하고 Data block에 있는 함수를 실행시킨다. 
  * Data file가 비어있을 경우 fallback함수를 실행 시키고 그 이후에 logic을 실행한다. 
  * Payable 함수일 경우에는 Contract의 balance를 증감시키는 역할을 한다(기본적인 pay와 같다)

#### Transmmiting a Data Payload to an EOA or Contract
Transaction의 데이터 필드에는 값이 들어갈 수 있지만, 이더리움 네트워크에서는 그 부분은 완전히 무시된다. Application(wallet)에 따라 해석해서 데이터로 사용될 수 있지만 각 client마다 다르다. 향후에 

Contract의 경우 Keccak-256(SHA-3)를 사용하여 serialized된 데이터를 사용한다.
Data field : ABI 데이터를 serialize하여 뒤에 + vlaue를 더한다. 
* ABI Function serialize 
> web3.sha3("withdraw(uint256)"); 
  ->'0x2e1a7d4d13322e7b96f9a57413e1525c250fb7a9021cf91d1540d5b69f16a49f'

> withdraw_amount = web3.toWei(0.01, "ether");
--> '10000000000000000'

> withdraw_amount_hex = web3.toHex(withdraw_amount);
--> '0x2386f26fc10000'

ABI의 첫 4bytes padding 32bytes + value(serialized)

"2e1a7d4d" + "00000000000000000000000000000000000000000000000000" + "2386f26fc10000"

>2e1a7d4d000000000000000000000000000000000000000000000000002386f26fc10000

*위 데이터는 withdraw()함수에  0.01이더를 넣었을 때 나오는 serialize된 값.*


## Special Transaction: 
#### Contract creation
* Transaction에 특수한 경우 중 하나가 컨트렉을 배포할 때이다.
* 해당 Trasaction은 "zero address"라고 불리는 특별한 주소로 전송된다.
* "0x0" 주소는 create contract로 쓰이고 어떤 다른 작업도 할 수 없다.
> 해당 주소로 ether를 전송하는 경우에는 그대로 보내지는데 당연히 다시 찾을 수 없기 때문에 burn의 효과가 있다.
> BUT, burn하려고 할 때 사용하기 위해 assign해둔 주소가 있으니 사용해라 
 "0x000000000000000000000000000000000000dEaD"
* Contract creation은 payload data가 없을 경우(ABI에 정의된 함수를 쓰지 않을경우) 위에 말한 burn account에 보내는 것과 같은 효과가 난다(신뢰할 수 있는 계약을 타지 않았기 때문에)

## Digital Signatures.





----
##### fallback : 예외가 발생할 때 호출할 수 있도록 대비해놓은 함수 
