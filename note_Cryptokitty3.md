# Cryptokittyコードを読む第３章
ソースコードを読んだメモ


## 参照リンク
 - https://blockgeeks.com/guides/cryptokitties/
 - https://github.com/cryptocopycats/awesome-cryptokitties/blob/master/contracts
 - https://medium.com/loom-network/how-to-code-your-own-cryptokitties-style-game-on-ethereum-7c8ac86a4eb3

# コントラクトの構成詳解
ここで用いているコードは　こちらのGithub https://github.com/cryptocopycats/awesome-cryptokitties/blob/master/contracts 
からの抜粋にコメントをつけたもの

#### 実際にデプロイされているコントラクト
 - KittyCore (Open Source)
 - SaleClockAuction (Open Source)
 - SiringClockAuction (Open Source)

Contractの継承関係
```Solidity

contract KittyAccessControl
contract KittyBase is KittyAccessControl
contract KittyOwnership is KittyBase, ERC721
contract KittyBreeding is KittyOwnership
contract KittyAuction is KittyBreeding
contract KittyMinting is KittyAuction
contract KittyCore is KittyMinting
```
## コードを読む3
コードを読む2の続き

#### 6.KittyMinting
https://github.com/onibakuchi/awesome-cryptokitties/blob/master/contracts/KittyMinting.sol

 - KittyAuctionを継承  
 - 運営がgen0の猫を作るのに関係する関数が定義されている
ここでは，あまりコードの理解や構成の理解に必要でないと思ったので，飛ばす．

#### 7. KittyCore
重大なバグがあった際，アップグレードできるようにしてある
```solidity
 // Set in case the core contract is broken and an upgrade is required
    address public newContractAddress;
```

saleAuctionとsiringAuction以外からの，etherの送金を受け付けない，フォールバック関数が定義されている.
Payableにしないといけない:payable コールと一緒にEtherが受け取れる. 
Ref：[fallback function のdoc ]( https://solidity-jp.readthedocs.io/ja/latest/contracts.html#fallback-function)
payable	がついない場合で，etherを受け取った場合requireスタイルの例外を吐く．
```solidity
    /// @dev Reject all Ether from being sent here, unless it's from one of the
    ///  two auction contracts. (Hopefully, we can prevent user accidents.)
function() external payable {
        require(
            msg.sender == address(saleAuction) ||
            msg.sender == address(siringAuction)
        );
    }
```
例外についてdocでは
> 内部では、Solidityは require-スタイルの例外に対してrevert操作（0xfd 命令）を行います。また、require-スタイルの例外を投げるために、無効な操作を実行します。どちらのケースにおいても、ステートに対してなされた変更の全てをEVMがrevertする様にします。revertする理由は、期待した効果が実際には起きず、命令の実行を続ける安全な方法がないためです。トランザクションの原子性（一連のやりとりが全て実行されるか、1つも実行されない状態になること）を保ちたいので、最も安全な方法は全ての変更をrevertし、トランザクション全体（もしくは少なくとも呼び出し）の結果を無効にすることです。assert-スタイルの例外は全ての使用可能なガスをその呼び出しに使ってしまう一方で、require-スタイルの例外はガスを使いません。これはMetropolisがリリースされた時からスタートしました。
[Ref:Doc](https://solidity-jp.readthedocs.io/ja/latest/security-considerations.html#sending-and-receiving-ether)

とある

```solidity
function withdrawBalance() external onlyCFO {
        uint256 balance = this.balance;
        // Subtract all the currently pregnant kittens we have, plus 1 of margin.
        uint256 subtractFees = (pregnantKitties + 1) * autoBirthFee;

        if (balance > subtractFees) {
            cfoAddress.send(balance - subtractFees);
        }
    }
}
```


## 重要なところ
Payable ，fallback function の挙動，使い方
## コードを読む第４章に続く
