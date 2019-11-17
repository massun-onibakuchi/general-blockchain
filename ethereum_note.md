Ethereum memo

https://github.com/ethereum/wiki/wiki/%5BJapanese%5D--Ethereum-Development-Tutorial#state-machine

## イーサリアムのブロックチェーンとビットコインとかのブロックチェーンとどう異なるか
イーサリアムのブロックチェーンは，ブロックチェーンに格納するデータにプログラミング言語が組み込まれたブロックチェーンというふうに説明できる．あるいは、大衆の思惑による決定（コンセンサス）を基板とした地球規模の仮想マシンとも言える。実用的な観点からして、EVMは膨大な量のアカウント（というオブジェクト）を内部に保持する分散型コンピュータと考えられる.

わたしたちが Bitcoin を用いてやろうとしているのは、分散型通貨システムの構築です。 なので、トランザクションの順番をみんなが合意できることを確約するために、 状態遷移システム と 大衆意思決定のシステム をくっつけてやらなくてはなりません。 Bitcoin の分散型大衆決定プロセスでは、「ブロック」と呼ばれる「トランザクションを梱包したもの」を作り続けようとする、 ネットワーク上のノードが必要です

https://cdn.amebaowndme.com/madrid-prd/madrid-web/images/sites/542680/6ba37a143c5201473c0b52d3e2dd885b_5ad8c62b8a735951c7eb4b810d3107b5.png?width=1300

## アカウント
アカウントには２種類ある
 - EOA Externaly Owned Account :普通にユーザが持っているアカウント
 - EOA でないアカウント　:Contract Account

ー抜粋ー
〜アカウントには2つのタイプがある〜 　オブジェクトの外側の世界からEVMを使用してアクセスすることができるアカウント（オブジェクト）として「外部所有アカウント（EOA,EOアカウント）」という名の特別なタイプがある。あるEOアカウントから他のどんなアカウントに対する「メッセージ」も、そのEOアカウントの秘密鍵で署名されたトランザクションを送ることが引き金となり、送ることが可能だ。EOアカウントでないものは「 契約、contract 」と呼ばれ、「メッセージ」の受け取り、自動的に内部コードを実行し、（自身のもつ内部ストレージに読み書きする能力を持つ。）受け取った「メッセージ」のストレージを読み取り、そして他の契約アカウントにメッセージを送る。（自分自身にも送ることができる。）　ひとつの捉え方として、contract とは、契約を遂行するためのプログラミングコードだ。
 https://github.com/ethereum/wiki/wiki/%5BJapanese%5D--Ethereum-Development-Tutorial#state-machine

## GHOSTプロトコル　Greedy Heaviest Observed Subtree
Bitcoin:もっとも長い歴史を持つ＝長いチェーンをメインチェーンとする  
Ethereum:もっとも計算量のかけられたチェーンを採用
＝重いチェーンの採用
＝
https://medium.com/@wshino/ethereumはbitcoinと何が違うのか-85f2bbabb084
### メインチェーンの決め方
マイニングにより最も多く計算が蓄積されているチェーンをメインチェーンとしているのです。ここで「最も長いチェーン」ではなく「最も多く計算が蓄積されているチェーン」をメインチェーンに選択

## Account state σ[a]
Yellow Paperより抜粋
Account state は４つのフィールドからなる
 - ** nonce**:A scalar value equal to the number of trans- actions sent from this address or, in the case of accounts with associated code, the number of contract-creations made by this account. For ac- count of address a in state σ, this would be for- mally denoted σ[a]n
 - ** balance**:
 - ** storageRoot**:A 256-bit hash of the root node of a Merkle Patricia tree that encodes the storage con- tents of the account (a mapping between 256-bit integer values), encoded into the trie as a mapping from the Keccak 256-bit hash of the 256-bit integer keys to the RLP-encoded 256-bit integer values. The hash is formally denoted σ[a]s.
 - codeHash:

## ブロックデータ構造
 - header
 - transaction
 - ommer_block_headers
からなる

## Transactionには２種類ある
 There are two types of transactions: those which result in message calls and those which result in the creation of new accounts with associatedcode (known informally as ‘contract creation’).

https://ethereum.github.io/yellowpaper/paper.pdf
Chapter 4.2 参照
 1. message calls を引き起こすTx
 2. contract creation と言われる，コードに結ぶつけらた新しいアカウントを作るTx  
## Transaction データ構造
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

## Block構造
** Ethereumのブロックは，ブロックヘッダーの集合であり，構成されたトランザクションに対応する情報と，おmmerブロックの情報のセット**
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


The world state (state), is a map- ping between addresses (160-bit identifiers) and account states (a data structure serialised as RLP, see Appendix B). Though not stored on the blockchain
State Treeはブロックの外に保持しておき、各ブロックにはState Treeのrootの値のみ
ユーザーが持っているアカウントを表すデータはこのState Treeに保存されています
State treeはマークル木とパトリシア木を合体した構造なので、マークルパトリシア木の構造
Bitcoinで自分の残高を確認するためには今までに自分が受け取ってまだ使っていないやりとりを集める必要があります。State Treeに代表される状態を表す構造がないため、都度計算する必要があります。Ethereumで自分の残高を確認するためには、State Treeから自分が今持っている残高を取得するだけの処理で済みます
Storage Treeはスマートコントラクトの持つ情報が入っています
ブロックチェーンに記録されるのは，トランザクション情報のみ．全てに変数がブロックチェーン上に書き込まれている訳ではない
## コントラクトそのものや，内部変数，状態変数はブロックチェーン上にない？
ここでポイントなのは、ブロックに含まれているのはこれらの要約値のみであり、実際の「全てのアカウント状態のデータ」「全てのレシートのデータ」はブロックチェーンの外で管理されているということです。
これはつまり、各フルノードがそれぞれのローカルストレージにこれらの値を保持していてこれらの実データはネットワークに伝搬されることはないということ

どこで contract が実行されるのか」という質問で、物理デバイス上でどこか？ということです。 この質問に対しては、シンプルな回答があります。 「 contract の実行プロセスは、その状態遷移関数の定義の一部であり、それ故ブロックの有効化アルゴリズムの一部となります。 なので、もしトランザクションがブロック B に付加されたならばそのトランザクションにより生まれるコード実行は全てのノードで実行されます。 その時点より未来において、ブロックBをダウンロードした全てのノードということです。」

### contractが別のコントラクトを呼び出したときのgasの消費とgasが尽きたときの挙動
内部トランザクションはあくまで最初に外部アカウントからコントラクトアカウントに送られたMessage Callに紐づくものであり、内部トランザクションで使用されたGasもそのMessage CallのGas Limitを消費していることになるからです。
また、もしある内部トランザクションを実行している最中に最初のMessage Callトランザクションの　Gasが足りなくなってしまった場合は、その内部トランザクションは実行されなかったことになりますが、それ以前のMessage Callトランザクションや内部トランザクションの実行はなかったことにはなりません。


