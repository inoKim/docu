# wallet
### 이더리움에서 기본적인 인터페이스역할을 수행 
> 잔액 확인, 기본 transaction 등.. 

### 개발 관점에서의 지갑 
1. 사용자의 키를 저장하고 관리하는 시스템 
2. 일부 지갑은 키를 저장하거나 관리하는 역할을 수행하는 반면, Dapp에 대한 인터페이스로만 사용 하기도 함. 

## 본격적으로, 지갑이란? 
- 목적 자체로는 다시 사용가능한 키쌍을 관리하는 것.
BEST : 전송 시 마다 키쌍을 다시 만들어 사용.

*But, 관리가 어렵기 때문에 아래와 같은 두 가지 방법 중 하나를 선택하여 사용한다.* 

## 1. Nondeterministic (Random) Wallets
- 1회 생성에 한쌍의 keypair가 생성된다. 

다시 생성 할 수 없고, 유저가 주기적인 키 백업과 교체를 진행해야 함. 
* 잃어버리는 경우 ether,smart-contract에 접근이 불가능
키 생성시 PrivateKey와 address로 구성된 한쌍이 나오는데 텍스트 기반이기 때문에 잃어버리기 쉽고 가독성이 떨어지기때문에 대부분의 클라이언트에서는 keystore형태로 암호화 하여 JSON 포맷으로 저장함

> Keystore file(JSON)
<pre><code>
{
    "address": "001d3f1ef827552ae1114027bd3ecf1f086ba0f9", // address
    "crypto": {
        "cipher": "aes-128-ctr",
        "ciphertext": "233a9f4d236ed0c13394b504b6da5df02587c8bf1ad8946f6f2b58f055507ece",
        "cipherparams": {
            "iv": "d10c6ec5bae81b6cb9144de81037fa15" //initialize vector
        },
        "kdf": "scrypt",
        "kdfparams": {
            "dklen": 32,
            "n": 262144,
            "p": 1,
            "r": 8,
            "salt": "99d37a47c7c9429c66976f643f386a61b78b97f3246adca89abe4245d2788407"
        },
        "mac": "594c8df1c8ee0ded8255a50caf07e8c12061fd859f4b7c76ab704b17c957e842"
    },
    "id": "4fcb2ba4-ccdb-424f-89d5-26cce304bf9c",
    "version": 3
}
</code></pre>

## 2. Deterministic (Seeded) Wallets
- 시드에 의해 키쌍이 미리 결정되어져 있는 지갑 
- 대표적인 예) HD wallet(BIP-32), Mnemonic code(BIP-39)
![architecture_HD](https://github.com/ethereumbook/ethereumbook/raw/develop/images/hd_wallet.png)
1. 트리 구조로 이루어져 있기 때문에 조직적인 사용이 가능하며, 사용자가 개인키에 접근하지 않고 공개키를 만들 수 있다.(?)
2. 시드를 기반으로 만들어진 지갑이기 때문에 시드만 있으면 모든 키 쌍을 복구해 낼 수 있다. 

* 기본적으로 Hexadecimal로 구성된 코드를 외우기 어렵고, 유효성 체크에 문제가있지만, text로 구성된 코드는 유효성 체크도 가능하기 때문에 관리 측면에서도 장점을 갖는다 

Hexadecimal string 
> FCCF1AB3329FD5DA3DA9577511F8F137

Mnemonic code 
> wolf juice proud gown wool unfair wall cliff insect more detail hub
