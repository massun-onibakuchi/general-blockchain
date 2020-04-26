# Blockchain
主にEthereumのブロックチェーンに関するメモ．

## EthereumとBitcoinの金銭の管理方式の違い
アカウント方式/Account based model　vs　コイン識別方式/UTXO

Account based model
 - Replay attackに弱い

> Replay attack
悪意のあるノードが繰り返し同じトランザクションを投げつけること．
unique number(nonce) ,timeなどをトランザクションに含めることで防ぐ

UTXO model
 - 残高を数えるのがアカウントベースに比べて面倒
 - All coins are not the same
 - Refer to specific coins when spending
 - Coins are consumed; create new ones
 - A coin can only be spent once

Input and Ouput 
Output become an another output

## イーサリアムのブロックチェーンとビットコインとかのブロックチェーンとどう異なるか  
イーサリアムのブロックチェーンは，ブロックチェーンに格納するデータにプログラミング言語が組み込まれたブロックチェーンというふうに説明できる．あるいは、大衆の思惑による決定（コンセンサス）を基板とした地球規模の仮想マシンとも言える。実用的な観点からして、EVMは膨大な量のアカウント（というオブジェクト）を内部に保持する分散型コンピュータと考えられる.  
  
わたしたちが Bitcoin を用いてやろうとしているのは、分散型通貨システムの構築です。 なので、トランザクションの順番をみんなが合意できることを確約するために、 状態遷移システム と 大衆意思決定のシステム をくっつけてやらなくてはなりません。 Bitcoin の分散型大衆決定プロセスでは、「ブロック」と呼ばれる「トランザクションを梱包したもの」を作り続けようとする、 ネットワーク上のノードが必要です  
  
https://cdn.amebaowndme.com/madrid-prd/madrid-web/images/sites/542680/6ba37a143c5201473c0b52d3e2dd885b_5ad8c62b8a735951c7eb4b810d3107b5.png?width=1300  
  
https://blog.ethereum.org/2015/11/15/merkling-in-ethereum/



## GHOSTプロトコル　Greedy Heaviest Observed Subtree
Bitcoin:もっとも長い歴史を持つ＝長いチェーンをメインチェーンとする  
Ethereum:もっとも計算量のかけられたチェーンを採用
＝重いチェーンの採用  

https://medium.com/@wshino/ethereumはbitcoinと何が違うのか-85f2bbabb084

### メインチェーンの決め方
マイニングにより最も多く計算が蓄積されているチェーンをメインチェーンとしているのです。ここで「最も長いチェーン」ではなく「最も多く計算が蓄積されているチェーン」をメインチェーンに選択

[[1]Medium Ethereum 2.0: A Complete Guide. Casper and the Beacon Chain.  // Colin Schwarz ](https://medium.com/chainsafe-systems/ethereum-2-0-a-complete-guide-casper-and-the-beacon-chain-be95129fc6c1)

