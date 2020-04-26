Ethereum Architecture
  
https://github.com/chronaeon/beigepaper/blob/master/beigepaper.pdf  
 
https://github.com/ethereum/wiki/wiki/%5BJapanese%5D--Ethereum-Development-Tutorial#state-machine  
  
https://medium.com/@preethikasireddy/how-does-ethereum-work-anyway-22d1df506369  
  
https://kauri.io/ethereum-101-part-2-understanding-nodes/48d5098292fd4f11b251d1b1814f0bba/a  
  
https://medium.com/shyft-network-media/understanding-trie-databases-in-ethereum-9f03d2c3325d  
  
https://ethereum.stackexchange.com/questions/268/ethereum-block-architecture  


## Recursive Length Prefix (RLP)   
https://github.com/ethereum/wiki/wiki/%5BJapanese%5D-RLP    
https://medium.com/coinmonks/data-structure-in-ethereum-episode-1-recursive-length-prefix-rlp-encoding-decoding-d1016832f919     
https://y-nakajo.hatenablog.com/entry/2019/11/19/181425    
https://y-nakajo.hatenablog.com    
  
ひとことで言うと，Ethereumでのデータの保存の仕方，様式．  
  
In computer science, data serialization is necessary for many complex data forms to be stored or transmitted in only one formal format. Because of that, RLP is an encoding/decoding algorithm that helps Ethereum to serialize data and possible to reconstruct them quickly.

#### そもそも Data serialization データシリアル化とは
https://python-guideja.readthedocs.io/ja/latest/scenarios/serialization.html   
> データシリアライズは、構造化されたデータを、元の構造が復元されるように共有または格納できる形式に変換する概念です。場合によっては、データシリアライズの第2の目的は、シリアライズされたデータのサイズを最小限に抑え、ディスクのスペースまたは帯域幅の要件を最小限に抑えることです。

http://e-words.jp/w/バイト列.html    
> ファイル入出力やデータ送受信、暗号化、データ圧縮など、扱うデータの型や形式などに立ち入らず汎用的な処理を行いたい場合にデータをバイト列として扱う。その内容は実際には文字や数値であったり、あるいは画像や音声、機械語のプログラムであったりする。
バイト列とは
> バイト列とは、文字や数値といった特定の形式や意味が与えられていない、任意のビットパターンからなる1バイトのデータが並んだデータ集合のこと。
プログラミング言語によってバイト列の扱い方は様々で、専用のデータ型が用意されている場合や、バイト型変数の配列として扱うことが多い。文字列など他のデータ形式との間で相互に変換する機能が提供される場合もある。

Conclusion :Serialization is object translation to other language, in case you want to store or share data.

Serialization is the process of converting some in-memory object to another format that could be used to either store in a file or sent over the network. Deserialization is the inverse process meaning the actual object instance is restored from the given serialized representation of the object. This is very useful when communicating between various systems.
Serialization format : e.g) JSON,XML...

### RLPの定義とその方法
RLPはEthereumにおいてシリアライズされたオブジェクトを符号化する一般的な方法．特殊なアトミックなデータ型(例えば文字列型、整数型、浮動小数点型)の符号化はもっと上位のプロトコルに任せているので，RLPは，string,byte,listのシリアル化を行うのみである．
シリアル化の方法は，イエローペーパーと以下を参照すれば理解できる．
https://medium.com/coinmonks/data-structure-in-ethereum-episode-1-recursive-length-prefix-rlp-encoding-decoding-d1016832f919   
https://y-nakajo.hatenablog.com/entry/2019/11/19/181425  
https://y-nakajo.hatenablog.com  

## Patricia Tree 
### 前提：Basic Radix Trees
あるデータが改ざんされていないことを一部のデータや枝のハッシュから，ルートハッシュを計算して，信頼できる正しいルートハッシュと照らし合わせることで，容易に証明できる．莫大なデータの検証を行うこともこのメカニズムは可能にする．

https://github.com/ethereum/wiki/wiki/%5BJapanese%5D-Patricia-Tree  
> Merkle Patricia tree は、あらゆる、「 ( key, value ) の帳簿 」を保存するのに使用できる、 暗号学的認証が付与されたデータ構造を提供しますが、 この仕様書においては、key 及び value は string 文字列 に制限します。 (この制約を解除するには、単に他のデータ型におけるシリアル化の形式を用いるだけです。) この データ木 は完全に決定的です。というのは、 全く同じ ( key, value ) の帳簿を持った Patricia tree は最後のバイトに至るまで正確に同じであり、 それ故同じ root hash を持つ事が保証されており、また、O(log(n)) という優秀な計算時間内でデータの挿入、検索、削除が可能であり、 red black tree のような複雑な比較基盤のデータ構造よりも、簡単に理解しコード化する事ができます。


# ブロック構造
 - Header
 - Transaction series
 - Ommer block headers
からなる　　

[Ethereumのブロック構造，EVM図解](http://takenobu-hs.github.io/downloads/ethereum_evm_illustrated.pdf)

**Ethereumのブロックは，ブロックヘッダーの集合であり，構成されたトランザクションに対応する情報と，Ommerブロックの情報のセット**　　
パトリシア木は、マークル木のコンセプトに対して修正がなされており、ノードの挿入・削除が可能であり、 ただ変わるだけでなく、効率が良くなります。 さらに、全状態の情報が、最新のブロックに含まれますので、ブロックチェインの全履歴を保存する必要がありません。

1. Header
  - difficulty:
  - extraData:
  - gasLimit:このブロックで使える Gasの最大値
  - gasUsed:
  - hash:
  - logsBloom: ブロック内のトランザクションから出力されるログがブルームフィルタと呼ばれる形で記録
  - miner(beneficiary): マイナーのアドレス（マイニングの報酬をもらうアドレス）
  - mixHash: nonce と組み合わせて、十分な量の計算を行ったことの証明
  - nonce: mixhash と組み合わせて、十分な量の計算を行ったことの証明
  - number:
  - **parentHash** : 現ブロックの直前のブロック(=親ブロック）のヘッダH_pのkeccak256のハッシュ　
  - receiptsRoot:
  - sha3Uncles:現在のブロックからみたUncleブロック配列のハッシュ
  - size:
  - **stateRoot**:　Merkle-Patricia State Trieのルートノードのハッシュ H_r
  - timestamp:
  - totalDifficulty:
  - transactions:このブロックに含まれている全てのトランザクションのハッシュの配列
  - **transactionsRoot**: ブロックに含めているトランザクションのルートノードのハッシュ
  - uncles:
 2. transactions: トランザクションのリスト
  -
 3. ommer_block_headers: 競合しているブロックのヘッダーのリスト

![](https://miro.medium.com/max/2112/1*4EQFjXD2-dbiVgVv-8Si8g.png)

The world state (state), is a map- ping between addresses (160-bit identifiers) and account states (a data structure serialised as RLP, see Appendix B). Though not stored on the blockchain
State Treeはブロックの外に保持しておき、各ブロックにはState Treeのrootの値のみ
ユーザーが持っているアカウントを表すデータはこのState Treeに保存されています
State treeはマークル木とパトリシア木を合体した構造なので、マークルパトリシア木の構造
Bitcoinで自分の残高を確認するためには今までに自分が受け取ってまだ使っていないやりとりを集める必要があります。State Treeに代表される状態を表す構造がないため、都度計算する必要があります。Ethereumで自分の残高を確認するためには、State Treeから自分が今持っている残高を取得するだけの処理で済みます
Storage Treeはスマートコントラクトの持つ情報が入っています
ブロックチェーンに記録されるのは，トランザクション情報のみ．全てに変数がブロックチェーン上に書き込まれている訳ではない

コントラクトそのものや，内部変数，状態変数はブロックチェーン上にない？
ここでポイントなのは、ブロックに含まれているのはこれらの要約値のみであり、実際の「全てのアカウント状態のデータ」「全てのレシートのデータ」はブロックチェーンの外で管理されているということです。
これはつまり、各フルノードがそれぞれのローカルストレージにこれらの値を保持していてこれらの実データはネットワークに伝搬されることはないということ

どこで contract が実行されるのか」という質問で、物理デバイス上でどこか？ということです。 この質問に対しては、シンプルな回答があります。 「 contract の実行プロセスは、その状態遷移関数の定義の一部であり、それ故ブロックの有効化アルゴリズムの一部となります。 なので、もしトランザクションがブロック B に付加されたならばそのトランザクションにより生まれるコード実行は全てのノードで実行されます。 その時点より未来において、ブロックBをダウンロードした全てのノードということです。」

## Account state σ[a]
Yellow Paperより抜粋
Account state は４つのフィールドからなる
 - ** nonce**:A scalar value equal to the number of trans- actions sent from this address or, in the case of accounts with associated code, the number of contract-creations made by this account. For ac- count of address a in state σ, this would be for- mally denoted σ[a]n
 - ** balance**:
 - ** storageRoot**:A 256-bit hash of the root node of a Merkle Patricia tree that encodes the storage con- tents of the account (a mapping between 256-bit integer values), encoded into the trie as a mapping from the Keccak 256-bit hash of the 256-bit integer keys to the RLP-encoded 256-bit integer values. The hash is formally denoted σ[a]s.
 - codeHash:

# アカウント
アカウントには２種類ある
 - EOA Externally Owned Account :普通にユーザが持っているアカウント
 - EOA でないアカウント　:Contract Account

ー抜粋ー
〜アカウントには2つのタイプがある〜 　オブジェクトの外側の世界からEVMを使用してアクセスすることができるアカウント（オブジェクト）として「外部所有アカウント（EOA,EOアカウント）」という名の特別なタイプがある。あるEOアカウントから他のどんなアカウントに対する「メッセージ」も、そのEOアカウントの秘密鍵で署名されたトランザクションを送ることが引き金となり、送ることが可能だ。EOアカウントでないものは「 契約、contract 」と呼ばれ、「メッセージ」の受け取り、自動的に内部コードを実行し、（自身のもつ内部ストレージに読み書きする能力を持つ。）受け取った「メッセージ」のストレージを読み取り、そして他の契約アカウントにメッセージを送る。（自分自身にも送ることができる。）　ひとつの捉え方として、contract とは、契約を遂行するためのプログラミングコードだ。
 https://github.com/ethereum/wiki/wiki/%5BJapanese%5D--Ethereum-Development-Tutorial#state-machine

# Transaction

### Transaction and messages
In the most basic sense, a transaction is a cryptographically signed piece of instruction that is generated by an externally owned account, serialized, and then submitted to the blockchain.

Transactionには２種類ある
 There are two types of transactions: those which result in message calls and those which result in the creation of new accounts with associatedcode (known informally as ‘contract creation’).

 1. message calls 
 2. contract creation

https://medium.com/@preethikasireddy/how-does-ethereum-work-anyway-22d1df506369  

https://ethereum.github.io/yellowpaper/paper.pdf  
Chapter 4.2 参照

fromとtoで場合分けすると次の４種類になるが，元を辿ると，トランザクションの初めの発行者は必ずEOAである

 - EOA => EOA 
 - EOA => Contract Address
 - Contract Address => Contract Address
 - Contract Address => EOA

Contracts that exist within the global scope of Ethereum’s state can talk to other contracts within that same scope. The way they do this is via “messages” or “internal transactions” to other contracts. We can think of messages or internal transactions as being similar to transactions, with the major difference that they are NOT generated by externally owned accounts. Instead, they are generated by contracts. They are virtual objects that, unlike transactions, are not serialized and only exist in the Ethereum execution environment.

![](https://miro.medium.com/max/3488/1*8wbH03HO4jL9bhT7Sa9ZCA.png)

One important thing to note is that internal transactions or messages don’t contain a gasLimit. This is because the gas limit is determined by the external creator of the original transaction (i.e. some externally owned account). The gas limit that the externally owned account sets must be high enough to carry out the transaction, including any sub-executions that occur as a result of that transaction, such as contract-to-contract messages. If, in the chain of transactions and messages, a particular message execution runs out of gas, then that message’s execution will revert, along with any subsequent messages triggered by the execution. However, the parent execution does not need to revert.

### Transaction の構造
message calls とcontract creationが共通でもつフィールド

 - nonce: A scalar value equal to the number of trans- actions sent by the sender; formally Tn.
 - gasPrice: A scalar value equal to the number of Wei to be paid per unit of gas for all computation costs incurred as a result of the execution of this transaction; formally Tp.
 - gasLimit: A scalar value equal to the maximum amount of gas that should be used in executing this transaction. This is paid up-front, before any computation is done and may not be increased later; formally Tg.
 - to: The 160-bit address of the message call’s recipi- ent or, for a contract creation transaction, ∅, used here to denote the only member of B0 ; formally Tt.
 - value: A scalar value equal to the number of Wei to be transferred to the message call’s recipient or, in the case of contract creation, as an endowment to the newly created account; formally Tv.
 - v, r, s: Values corresponding to the signature of the transaction and used to determine the sender of the transaction; formally Tw, Tr and Ts. This is expanded in Appendix F.

Additionally, a contract creation transaction contains:

 - init: An unlimited size byte array specifying the EVM-code for the account initialisation procedure, formally T_i.
init is an EVM-code fragment; it returns the body, a second fragment of code that executes each time the account receives a message call (either through a trans- action or due to the internal execution of code). init is executed only once at account creation and gets discarded immediately thereafter.

In contrast, a message call transaction contains:
 -  data: An unlimited size byte array specifying the
input data of the message call, formally T_d.

### contractが別のコントラクトを呼び出したときのgasの消費とgasが尽きたときの挙動
内部トランザクションはあくまで最初に外部アカウントからコントラクトアカウンΩトに送られたMessage Callに紐づくものであり、内部トランザクションで使用されたGasもそのMessage CallのGas Limitを消費していることになるからです。
また、もしある内部トランザクションを実行している最中に最初のMessage Callトランザクションの　Gasが足りなくなってしまった場合は、その内部トランザクションは実行されなかったことになりますが、それ以前のMessage Callトランザクションや内部トランザクションの実行はなかったことにはなりません。

# Gas and payment
Gas is the unit used to measure the fees required for a particular computation. Gas price is the amount of Ether you are willing to spend on every unit of gas, and is measured in “gwei.” “Wei” is the smallest unit of Ether, where 1⁰¹⁸ Wei represents 1 Ether. One gwei is 1,000,000,000 Wei.

With every transaction, a sender sets a gas limit and gas price. The product of gas price and gas limit represents the maximum amount of Wei that the sender is willing to pay for executing a transaction.Gas limit represents the maximum gas the sender is willing to spend money 
https://medium.com/@preethikasireddy/how-does-ethereum-work-anyway-22d1df506369

何に支払っているのか
 - 支払ったGasは 'beneficiary'　address　（マイナーのアドレス）に送られる  
 - Ethereumノードのストレージの使用に対する支払い  
 - Ethereumノードの行うcomputation stepに対する支払い  
  
 >Fees for storage have some nuanced aspects. For example, since increased storage increases the size of the Ethereum state database on all nodes, there’s an incentive to keep the amount of data stored small. For this reason, if a transaction has a step that clears an entry in the storage, the fee for executing that operation of is waived, AND a refund is given for freeing up storage space.  
  
# Gasの存在意義  
Solidityはチューリング完全であり，無限ループするスマコンのコードを実行することになった場合，ノードのリソースを食い尽くすまで止まらないことになってしまう．そのため，Gas　limit という制限を設けることで必ず処理が終わるようにする．  
[One important aspect of the way the Ethereum works is that every single operation executed by the network is simultaneously effected by every full node. ]  
  
> Turing machine is a machine that can simulate any computer algorithm (for those not familiar with Turing machines, check out this and this). This allows for loops and makes Ethereum susceptible to the halting problem, a problem in which you cannot determine whether or not a program will run infinitely. If there were no fees, a malicious actor could easily try to disrupt the network by executing an infinite loop within a transaction, without any repercussions. Thus, fees protect the network from deliberate attacks.  
