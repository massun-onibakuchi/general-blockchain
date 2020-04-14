# solidity security pattern
## Proxy Pattern
### 参考
https://qiita.com/ryu3/items/d3519d919b3c01a759ae   
https://zoom-blc.com/how-to-develop-upgradable-contracts

Proxy Contract と　Logic Contract に分ける  
Proxy ：Logi コントラクトのアドレスを保存，新しいロジックコントラクトのアドレスを参照するようにしておく．ユーザはこのコントラクトにトランザクションを送信する．このコントラクトは再デプロイしないので，機能を変えることはできない   
Logi ：このコントラクトを再デプロイすることで機能のアップグレーダブルを実現する．  
### Proxy Contract
Proxyコントラクトがユーザからトランザクションを受けっとたら，LogicコントラクトにdelegeteCallを　実行する  
．
### delegatecall vs call
msg.sender msg.valueが通常のcallと異なる働きをする  
 
Delegatecallとは、外部コントラクトへの呼び出しに対して呼び出し元のコントラクトの文脈で処理する関数です。ここで言う「コントラクトの文脈」とは、msg.senderやmsg.value、コントラクトのストレージのことなどを指しています。  

つまり、通常のcallであれば呼び出し先(call先)のコントラクトの文脈で関数を実行するのに対し、delegatecallの場合は外部コントラクトの関数を自身のコントラクトのストレージ文脈で処理することが可能になります。   
例えば、以下のようなコントラクトを考えていきましょう。この例では、コントラクトAがコントラクトBのsetN関数をdelegatecallし、関数を処理しています。
```solidity
contract A {
    uint public n;
    address public owner;
 
    function delegatecallSetN(address _b, uint _n) public {
        _b.delegatecall(bytes4(keccak256("setN(uint256)")), _n);
    }
}
 
contract B {
    uint public n;
    address public owner;
 
    function setN(uint _n) public {
        n = _n;
        owner = msg.sender;
    }
}
```
delegatecallなので、呼び出されたsetN関数はコントラクトAの文脈で処理されます。つまり、msg.senderはコントラクトAにトランザクションを送信したユーザーのアドレスであり、引数_nはコントラクトAのストレージ変数nにセットされます。

一方、これがdelegatecallではなくcallの場合は、msg.senderはコントラクトA自身のアドレスになり、引数_nはコントラクトBのストレージ変数nにセットされます。



## Oracle
https://www.toptal.com/ethereum/ethereum-oracle-contracts-tutorial-pt1

