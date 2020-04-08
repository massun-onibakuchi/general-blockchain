# Casper  
PoWの問題点，ゆるくPoSの概要からはじめて，EthereumのPoSのであるCasperについて

##  PoWからPoSへの移行
### PoWの問題
- マイナー（バリデータ）の中央集権化

  特にビットコインに当てはまるが，PoWでは規模の経済が成り立つため，大規模マイナーが小規模マイナーに対して有利にマイニングできる．

- エネルギー効率  

  よく言われる．

  エネルギー効率が高くてもネットワークへの参加コストが安い場合，攻撃コストも安くなってしまう．ビットコインの場合、ASICの費用と電気代というコストが掛かるため攻撃者に一定のコストを負わせることが可能である．EthereumのPoSの場合，自身がステイクしているETHが没収されるかもしれないという潜在的なリスクを負わせることによってネットワークのセキュリティを確保する．（参考文献[3]　詳細は後述）

 - 51%攻撃
 - ファイナリティ問題  
 確率的ファイナリティである．Bitcoinだと6コンファメーションは欲しいとよく聞く

## PoS  
PoSではバリデータという集団が次のブロックを提案，投票を行う．バリデータの投票の重みはそのバリデータがステイクした大きさに依存する．

PoWはハッシュパワーに応じて、PoSはステイクに応じてブロックを採掘できる確率が決定される．
"one unit of CPU power, one vote" it becomes "one currency unit, one vote". 

Ethereum公式のPoSのFAQによると，

In general, a proof of stake algorithm looks as follows. The blockchain keeps track of a set of validators, and anyone who holds the blockchain's base cryptocurrency (in Ethereum's case, ether) can become a validator by sending a special type of transaction that locks up their ether into a deposit. The process of creating and agreeing to new blocks is then done through a consensus algorithm that all current validators can participate in.

[Ethereum Wiki PoS-FAQ](https://github.com/ethereum/wiki/wiki/Proof-of-Stake-FAQ#what-is-proof-of-stake)


### PoSの問題  
 - 富の偏在化  
  
    大規模バリデーター以外のユーザーがそのネットワーに留まる理由が失われ、価格の下落によってセキュリティが脆弱になってしまう．
  ステイクする量が多いほどブロックを生成できる確率が高くなるため．富が集中しやすい．  

 - Nothing-At-Stake問題
  
    PoWの場合、マイナーは一番長いブロックチェーンに次のブロックをつけてマイニングするインセンティブがはたらく.
    なぜなら，もし自分が生成したブロックが含まれるフォークしたチェーンが生き残らなかった場合，報酬が得られないため，そのマイニングに投じたマシンの電気代などのコストが回収できなくなる．だから，攻撃したり，多数のフォークをマイニングするより，勝ちそうなチェーン，つまり，もっとも長いフォークにブロックを繋げていく方が理に適っているからである．

    一方で，PoSの場合，もっとも長いフォークにブロックをつなげるインセンティブはない．なぜなら，フォークが負けた場合もステイクは失われず、そのまま返金されるだけであるからである．そのため，全てのフォークをを同時に伸ばそうとする方が理に適っているため，正常に振る舞うインセンティブは低い．

 - ロングレンジアタック  
    

## Casperのモデル  
EthereumのPoSシステムはCasperと言われる  
 - CasperFFG   
Vitalikによって提案  
ハイブリッド型  

 - CapsperCBC  
Vlad Zamfirによって提案  
PoS型  
  
## CasperFFG  
バリデーターにはマイナーのように、ブロックを生成したり、ファイナリティを与える役割がある．Casper FFGではマイナーがPoWでブロックを生成し、バリデーターがPoSで特定のブロック高ごとに検証を行い、チェーンにファイナリティをもたらす.PoWによって生成されたブロックチェーンは確率的ファイナリティをもたらすに留まるが、Casper FFGではPoSによってファイナリティがもたらされる．

単純PoSの問題を解決するために懲罰アルゴリズムとしてslasherを導入

## CasperFFGの流れ
PoWでブロックを進めていく  
特定のブロック高(現在のところ 32ブロック)を1epochとする  
1epochの終わりごとにcheckpointがあり，checkpointでバリデータがPoSによる投票を行う．
全投票数の2/3以上の投票が集まるとjustfiedとなる.  justified：仮のfinalizedの状態のこと
また，次のepochまでPoWでブロックを進める  
次のepochがjustifiedされると，ひとつ前のjustifiedされたcheckpointはfinalizedとなる．  
finalisedされたところから，上述の過程を繰り返す．  

Casper FFGではバリデータによってFinalizeされたチェックポイントは後に覆されることはないため，ロングレンジアタックと呼ばれる過去のブロックを書き換えるような攻撃を防ぐ．また，checkpointとfinalizationによってブロックチェーンにファイナリティをもたらす．

https://blog.ethereum.org/2020/02/12/validated-staking-on-eth2-2-two-ghosts-in-a-trench-coat/　　
によると

 > FFG employs a clever trick. Votes actually consist of two sub-votes, one for the epoch that is attempting to be justified and another for an earlier epoch that is to become finalised. This saves a lot of extra communication between nodes and helps to achieve the goal of scaling to millions of validators.

checkpointでフォークがあった場合，二重に投票したり，両方に投票したりすることは許されず，これらのSlashing Conditionに違反したバリデータはslasherによってステイクを没収される

### slashingの役割
 1. to make it prohibitively expensive to attack eth2,
 2. to stop validators from being lazy by checking that they actually perform their duties. 

シバかれる条件slashing condition はeth2 phase0では今の所，二重投票や入れ子投票である（詳細はオリジナルの論文を参照）

### バリデータが善意にはたらくインセンティブ
eth2では，バリデータは怠惰で，賄賂を受け取り，インセンティブがない限りシステムに攻撃を仕掛けようとすることを想定している．
slashingによる罰は悪い行動を妨げるが，バリデータにeth2に恩恵を与える行動を取らせるためには報酬が必要である．(詳細は参考文献[1])以下は参考文献[1]より抜粋

 - Whistleblower rewards 🚓  
 違反しているバリデータを見つけてチクる？？と報酬がもらえる．  

 - Proposer rewards ⬜️⛓⬛️⛓⬜️  
 ブロックを生成する仕事にランダムに任命されたバリデータをプロポーザーproposerという．プロポーザーはある与えられる仕事を行うと報酬が与えられる．

 - Attester rewards ✔  
  バリデータによって投票される票はAttestationと言われる．  コンセンサスを形成する投票などの行動をとると，報酬が与えられる．（詳細は参考文献[1]）  

これらの報酬によって，バリデータはチェーンに貢献するように促進される．  

## 参考文献
[[1]Ethrerum Blog Validated, staking on eth2: #1 - Incentives](https://blog.ethereum.org/2020/01/13/validated-staking-on-eth2-1-incentives/) // Jan 2020

[[2]Ethereum  casper_basics.pdf](https://arxiv.org/pdf/1710.09437.pdf) // 22 Jan 2019

[[3]individua1  CasperFFG](https://individua1.net/casper-ffg-dynamic-validator-sets/) //June 2017

[YouTube-PoS,CapserFFGについて](https://www.youtube.com/watch?v=RwjJmsyfKFk)

[block-chain.jp byコンセンサス・ベイス　Casperについて](https://block-chain.jp/ethereum/casper1/)  //2017-2018

