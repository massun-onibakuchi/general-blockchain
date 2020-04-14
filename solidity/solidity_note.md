##solidity
term

### transitions
writing data is called a transaction whereas reading data is called a call.
transactions fundamentally change the state of the network .
 transaction can be as simple as sending Ether to another account, or as complicated as executing a contract function or adding a new contract to the network. The defining characteristic of a transaction is that it writes (or changes) data. 

 - Cost gas (Ether)
 - Change the state of the network
 - Aren't processed immediately
 - Won't expose a return value (only a transaction id). 
### calls
Calls, on the other hand, are very different. Calls can be used to execute code on the network, though no data will be permanently changed. Calls are free to run, and their defining characteristic is that they read data. When you execute a contract function via a call you will receive the return value immediately. In summary, calls:

Are free (do not cost gas)
Do not change the state of the network
Are processed immediately
Will expose a return value (hooray!)
Choosing between a transaction and a call is as simple as deciding whether you want to read data, or write it.

### type
 - mapping :辞書型でデータを格納する際　これを宣言する
 - address :銀行口座の番号のようなもの？アカウント固有な値   20バイトくらいで英数字がはいってるアレ
 - msg.sender :グローバル変数  これを使用すると関数を呼び出したユーザまたはスマートコントラクト）sのaddressを参照できる  

 - storage : ポインタ?
 - memory ; メモリ内にデータをコピー？

## struct
: 複数のことなるデータ型を格納できる　
structへのstorageポインタは、privateやinternal関数の引数として渡すことができる
```
function _doStuff(Zombie storage _zombie) internal {
  //  _zombieを処理する
}
/*こうすれば、ゾンビIDを渡して探す代わりに、関数にゾンビの参照そのものを渡すことが可能*/
```
## contract
: クラス的なやつ　継承できる　この内側にコードをかく、最小の句の
としての役割りもある
```
contract Doge {
  function catchphrase() public returns (string) {
    return "So Wow CryptoDoge";
  }
}

contract BabyDoge is Doge {
  function anotherCatchphrase() public returns (string) {
    return "Such Moon BabyDoge";
  }
}
```
## 複数の値を渡すとき

```
function multipleReturns() internal returns(uint a, uint b, uint c) {
  return (1, 2, 3);
}

function processMultipleReturns() external {
  uint a;
  uint b;
  uint c;
  // これが複数に割り当てる方法だ：
  (a, b, c) = multipleReturns();

  /*このうち一つ渡したいときは以下のようにこうする
    uint c;
    (,,c) = multipleReturns();
  */
}
```

## 関数につけるやつ修飾子 visiblity    
https://solidity.readthedocs.io/en/latest/contracts.html#visibility-and-getters
### public
:全員アクセス可能
### private
:当該のcontractからのみアクセス可能
### internal
:public的　当該のcontractの継承したcontractのアクセスできるようになる   
当該のcontractと親のcontractのみがアクセスできるということ
### external
:private的　コントラクトの外からだけ呼び出すことができる　つまりコントラクト内部の別の関数では呼び出すことができない     
外部からのみアクセス可能   
> ＞Note  
Everything that is inside a contract is visible to all observers external to the blockchain. Making something private only prevents other contracts from reading or modifying the information, but it will still be visible to the whole world outside of the blockchain.

## Abstract contract
https://solidity.readthedocs.io/en/latest/contracts.html?highlight=interface#abstract-contracts
:Contracts are marked as abstract when at least one of their functions lacks an implementation as in the following example (note that the function declaration header is terminated by ;)

```
pragma solidity >=0.4.0 <0.7.0;

contract Feline {
    function utterance() public returns (bytes32);
}
```
Abstract contracts are useful in the same way that defining methods in an interface is useful.

## 関数修飾子
modifier は関数のように宣言して、就職したい関数を定義するときに　publicとかと同じようにつける

## now
unix時間？を得る

## interface 
contractを宣言するのと似ている形式 他のコントラクトを呼び出す際に用いる．

## view 修飾
** 外部から **呼び出す場合のみガスコスト０である
注意，同じコントラクトのview関数でない別の関数から呼び出される場合 ガスコスト発生　してしまう

## Gas Station Network のことで質問があります
目的は一般ユーザーが参加しやすくする（ブッロクチェーンのサービスの敷居が高いから）
手段が，ガスを払わなくてもトランザクションが送れるようにする　ですよね
relayerがガスコストを肩代わりする動機がわかりません

## Getter Functions

The compiler automatically creates getter functions for all public state variables. For the contract given below, the compiler will generate a function called data that does not take any arguments and returns a uint, the value of the state variable data. State variables can be initialized when they are declared.

pragma solidity >=0.4.0 <0.7.0;

contract C {
    uint public data = 42;
}

contract Caller {
    C c = new C();
    function f() public view returns (uint) {
        return c.data();
    }
}
The getter functions have external visibility. If the symbol is accessed internally (i.e. without this.), it evaluates to a state variable. If it is accessed externally (i.e. with this.), it evaluates to a function.

pragma solidity >=0.4.0 <0.7.0;

contract C {
    uint public data;
    function x() public returns (uint) {
        data = 3; // internal access
        return this.data(); // external access
    }
}


状態変数　ブロックチェーン上に記録されるデータすべて？のこと．
関数　
messagecall言われる
 two kinds of function calls (internal ones that do not create an actual EVM call (also called a “message call”) and external ones that do)


truffle migrate でデプロイ
>
pragma solidity 0.4.2;

contract Election {
    // Read/write candidate
    string public candidate;

    // Constructor
    function Election () public {
        candidate = "Candidate 1";
    }
}

これをデプロイしたなら，truffle develop or console で
app.candidate()
// => 'Candidate 1'
**
ができるが，これはcandidate変数にコントラクトデプロイの過程で勝手にgetterメソッドを自動生成するから可能なのだ


# inheritance
## Arguments for Base Constructors
#### サブクラス（継承されるコントラクトのコンストラクタが引数を持つ時の記述の仕方
### https://solidity-jp.readthedocs.io/ja/latest/contracts.html#arguments-for-base-constructors

全てのベースコントラクトのコンストラクタは下記で説明される線形ルールに則り呼び出されます。もしベースコンストラクタが引数を持っていたら、継承したコントラクトはその全てを指定する必要があります。 2通りの方法でできます:

pragma solidity >=0.4.22 <0.6.0;

contract Base {
    uint x;
    constructor(uint _x) public { x = _x; }
}

// Either directly specify in the inheritance list...
contract Derived1 is Base(7) {
    constructor() public {}
}

// or through a "modifier" of the derived constructor.
contract Derived2 is Base {
    constructor(uint _y) Base(_y * _y) public {}
}
1つ目の方法は直接継承のリストに入れることです(is Base(7))。もう1つの方法は継承したコンストラクタの一部としてmodifierを呼び出します(Base(_y * _y))。もしコンストラクタの引数が定数で、コントラクトの挙動を決めるもしくは表現するものである場合、最初の方法の方が便利です。もしベースのコンストラクタの引数が継承したコントラクトによるのであれば、2つ目の方法を使う必要があります。引数は継承のリスト、もしくは継承したコンストラクタのmodifierスタイルで与えられる必要があります。 両方で引数を指定するとエラーとなります。

もし継承したコントラクトがベースコントラクトのコンストラクタに対する引数を決めなかった場合、そのコントラクトは抽象コントラクトになります。
## Multiple Inheritance 
Python と同じ C3線形　方式　ただし，継承の順序が逆で右から左 最初に継承したいのを最後に書く　

## Creating Contracts via new
#### https://solidity-jp.readthedocs.io/ja/latest/control-structures.html#creating-contracts-via-new

コントラクトは new というキーワードを使って他のコントラクトを作ることができます。コントラクトを作るコントラクトがコンパイルされる時に、作られるコントラクトのフルコードは既知である必要があります。そうすれば、再帰的なcreation-dependenciesは起きません。
## Assignment
memorie か　storage か
独立したコピー　か　参照　関係のこと
### https://solidity-jp.readthedocs.io/ja/latest/control-structures.html#assignment

### Complications for Arrays and Structs

### Complications for Arrays and Structs

### https://solidity-jp.readthedocs.io/ja/latest/control-structures.html#complications-for-arrays-and-structs

配列や構造体に変数をアサインするときの，セマンティックは複雑
memorie か　storage かどうかが重要
独立したコピー　か　参照　か

## Scoping and Declarations
### https://solidity-jp.readthedocs.io/ja/latest/control-structures.html#scoping-and-declarations
スコープのルールはC９９に従う

## error handling
Assert or require の使い方
Revert 

# ABI
ABIはコントラクトに接続するための標準的なインターフェース．コントラクトもABIもブロックチェーン外で作成され、ABIはコントラクトとコントラクトを接続するために使用されます。このドキュメントで紹介されているように、データはABIに従う形でエンコーディングされます

# November 24 ,2019
## Event
引数は任意の変数。ここに書いた変数の値がイベント発火時に渡される
Ref:Eventでコントラクトの監視をする:https://qiita.com/satons/items/d9c2dabfbf513e44f78b  :
:eventについて，：（アルゴリズムとかオーダーとか）https://daiki-sekiguchi.com/2018/07/24/ethereum-solidity-event/
：
