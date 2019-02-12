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

### The Transaction Nonce
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

> 






