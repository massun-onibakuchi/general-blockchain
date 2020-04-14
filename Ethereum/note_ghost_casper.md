# Combining Ghost and Casperのメモ
[Ethereum Combining Ghost and Casper paper](https://arxiv.org/abs/2003.03052) // 6 Mar 2020 (v1), last revised 12 Mar 2020 (this version, v2)

これは，上記の論文を読んでよく分からんかったところや，書き留めておきたいことのメモである

CapserとGHOSTのそれぞれの役割
eth2では，CapserとGHOSTを組み合わせたプロトコルを用いる．
Casper FFGはファイナリティをもたらすツールで，LMD GHOSTはフォークチョイスルール．

LMD GHOSTでは，バリデーターはBeacon chainによりランダムな構成員からなる１つcommitteに属して，ランダムに割り当てられたslotで，そのslotに割り当てられたバリデーターだけがチェーンのヘッドを決定するために投票する

一方で，Casper FFGは，checkpointに対する投票で，全バリデーターが投票する

2.4 Slot and Epoch
we measure time in slots, defined as some   
 constant number of seconds (tentatively 12
 seconds ). We then define an epoch to be some constant number C.The epoch j of slot i is ep(i) = j = ⌊ i ⌋. In other words, the blocks belonging to epoch j have slot numbers jC + k as k runs through {0, 1, . . . , C − 1}.

## Casper FFG
 the concepts of justification and finalization

 - In each view G, there is a set of justified checkpoint blocks J(G) and a subset F(G) ⊂ J(G) of finalized checkpoint blocks.
 - InaviewG,acheckpointblockBisjustified(byacheckpointblockA)ifthereareattesta- tions voting for A → B with total weight at least 2/3 of total validator stake. Equivalently,
 we say there is a supermajority link A →−J B. This is a view-dependent condition, because the view in question may have or have not seen all the relevant attestations to break the 2/3 threshold (or even having seen the block B itself), which is why the set of justified blocks is parametrized by G.
 - In a view G, if A ∈ J(G) (equivalently, A is justified), and A →−J B is a supermajority link with h(B) = h(A) + 1, then we say A ∈ F (G) (equivalently, A is finalized).


## よくわからない
 - supermajority linkがあると，どうなるのか，何の役に立つのか
 - supermajority linkとjustifidの関係
 - epoch boundary blockとepochの対応
 For a block B and an epoch j, define EBB(B, j), the j-th epoch boundary block of B, to be the block with the highest slot less than or equal to jC in chain(B). Let the latest such block be LEBB(B), or the last epoch boundary block (of B). 
 - FFGのfinalizationの条件  
 >  Finalization [1]  
 Only the 2nd, 3rd, and 4th most recent epochs can be finalized. This is done by checking:  
 Which epochs (within the past 4) are used as the source of the most recent justifications.  
 That the epochs between source and target are also justified (if any exist).  
 If these conditions are satisfied, the source is finalized. Note that we only consider k=1 and k=2 finality rules discussed in section 4.4.3 of the draft paper.  

[[1]Phase0 for Humans v0100](https://notes.ethereum.org/@djrtwo/Bkn3zpwxB?type=view#Phase-0-for-Humans-v0100)

 [[2]Ethereum2.0の情報まとめ](https://notes.ethereum.org/@protolambda/eth2_start#Eth2-Information)