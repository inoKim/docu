# wallet
### Contents
1. 지갑이란?
2. 관점에서 생각해보기
3. 지갑의 종류 - 비결정적 지갑
4. 지갑의 종류 - 결정적 지갑
5. 그렇다면 실제로는 ?
6. 토론 


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

Hexadecimal string (PrivateKey)
> FCCF1AB3329FD5DA3DA9577511F8F137

Mnemonic code (12개의 단어로 이루어진 코드 시드)
> wolf juice proud gown wool unfair wall cliff insect more detail hub

## Mnemonic code 

![므네모시네](http://www.rapportian.com/news/photo/201608/28370_25897_1657.jpg)
> 기억의 연못 in 하데스 

## Generating mnemonic words.
1. 암호화된 랜덤 sequence S를 생성(128~256bits)
2. SHA-256 해시의 첫 번째 길이 S/32bit를 취하여 S의 checksum을 생성한다. 
3. Random sequnce S에 checksum을 추가하여 S + Checksum = SC를 만든다.
4. SC를 11개의 비트로 나눈다(?)
5. 사전에 정의된 2048 단어의 각각 11비트를 mapping한다(?)
6. 생성된 단어들의 순서를 기억하고 사용하여아 한다. 
![architecture_mnenocincode](https://lh3.googleusercontent.com/ZHlWXublZuhPnf9CzTYuuu4-Q_PP43mV6js4FDBJ9pNkfxjWKxMHxTfPmKfHkIBK3U4Qm73p1DckPh_HEJHb8VhhnHthHvncoZx3bpwp9Jgduy2lRnKkCTAws19kIImjPwjnGH6F)
7. 이제 실제 Keypair생성에 실제로 사용 될 seed를 만드는데, 필요한 첫 번쨰 요소는 생성된 mnemonic code.
8. 두 번째 필요한 요소는 각각 unit단위마다 사용하는 salt값
![](https://github.com/ethereumbook/ethereumbook/raw/develop/images/bip39-part2.png)

#### PBKDF2 
- Key-streching 함수의 일종 
- 128 ~ 256bit의 entropy(암호화된 랜덤 sequence S)를 사용하여 512bits의 seed를 얻게 해주는 역할 
- 2048번은 hashing을 진행하기 때문에 보안상 비교적 안전하다. 

#### Entropy input에 따른 결과물의 차이는?
> Entropy input에 따라 mnmonic words의 갯수 차이가 있지만 결과 시드 값은 똑같이 512bits로 사용한다. 


#### 그렇다면 step8에서 사용한 salt(passphrase)값의 역할은?
> User가 사용하지 않고 application측에서 관리하는 2-factor 보안장치 
> salt값을 모르면 mnemonic code를 알고 있다고 하더라도 결과 seed를 찾을 수 없다. 
> But, 실제로 사용하는 곳은 없을 것 같다( 호환성을 위해서?? ) 


#### Implemented libraries.
- BIP-39
- python-mnemonic
- ...

### Extended public and private keys. 
> Seed로 생성된 최초의 keypair == root keypair(RKP)
> RKP에서 자식 keypair를 생성 할 수 있다. 

* RKP로 자손 KP를 생성 할 수 있고 privKey와 관계없이 다음 depth의 publickey를 생성 할 수 있기 때문에 다양한 방법으로 사용 할 수 있다. 
* But, xpub, xpriv가 노출 될 경우 모든 지갑이 위험
* And, 자식 PrivKeyt와 부모 체인코드로 Root private key를 추론할 수 있다. 
> 이렇기 때문에 나온것이 *Hardened child key derivation* 

### Navigating the HD wallet tree structure
- HD wallet은 20억개 씩의 일반 자식키와 확장된 자식키를 가지고있기 때문에 무한한 복잡성을 가능케한다. 

----

## 그렇다면 지갑 서비스를 위해서 고려해야 할 점은?
> 사업적인 측면과, 개발(자) 입장에서 생각하고 고려하는 것이 다르다. [discussion.jpg](./img/discussion.jpg)

* 사업적 측면 
> 대부분의 대표들과 이야기 해보면 사용자 입장과 서비스측면에서 봤을 때 일부 보안 이슈들은 trade-off라고 이야기한다.(현실)

* 개발자 측면
> Blockchain 기술이 Decentralization이라는 컨셉에 기반하고 있는데 해당 기술에 반하는 중앙집중화 컨셉이 되어서는 안된다. 

### 개인적인 의견
> "AWS를 사용한다!!"라는 이야기로는 완벽한 보안이 될수 없다(*당연히*)
> 대기업 수준의 보안일 경우 유저가 우리를 신뢰할 수 있다면 유저의 편의성을 위해서 최소 수준의 중앙집중화는 허용할 수 있지만, 그렇지 않다면 유저를 위해서도 개발자를 위해서도 그렇게 해서는 안된다. 

*자 그럼 여기에 대해 다른분들의 생각은 어떤지 궁금합니다.*
- 여러분들의 생각을 적어보겠습니다.






