# Combining Ghost and Casperのメモ
[Ethereum Combining Ghost and Casper paper](https://arxiv.org/abs/2003.03052) // 6 Mar 2020 (v1), last revised 12 Mar 2020 (this version, v2)

これは，上記の論文を読んでよく分からんかったところや，書き留めておきたいことのメモである

CapserとGHOSTのそれぞれの役割
eth2では，CapserとGHOSTを組み合わせたプロトコルを用いる．
Casper FFGはファイナリティをもたらすツールで，LMD GHOSTはフォークチョイスルール．
### LMD-GHOST votes and FFG votes
LMD GHOSTでは，バリデーターはBeacon chainによりランダムな構成員からなる１つcommitteに属して，ランダムに割り当てられたslotで，そのslotに割り当てられたバリデーターだけがチェーンのヘッドを決定するために投票する．

一方で，Casper FFGは，checkpointに対する投票で，全バリデーターが投票する.justification/finalizationやcrosslinkを処理する

 - 2.4 Slot and Epoch
we measure time in slots, defined as some   
 constant number of seconds (tentatively 12
 seconds ). We then define an epoch to be some constant number C (tentatively 64 slots).The epoch j of slot i is ep(i) = j = ⌊ i ⌋. In other words, the blocks belonging to epoch j have slot numbers jC + k as k runs through {0, 1, . . . , C − 1}.The genesis block Bgenesis has slot number 0 and is the first block of epoch 0.

epoch 0  => slot 0 ,1,2...63 epoch 1 => slot 64 ,65...127

## Casper FFG
 the concepts of justification and finalization

 - In each view G, there is a set of justified checkpoint blocks J(G) and a subset F(G) ⊂ J(G) of finalized checkpoint blocks.
 - In a view G, a checkpoint blockB is justified (by a checkpoint block A )if there are attestations voting for A → B with total weight at least 2/3 of total validator stake. Equivalently,
 we say there is a supermajority link A →−J B. This is a view-dependent condition, because the view in question may have or have not seen all the relevant attestations to break the 2/3 threshold (or even having seen the block B itself), which is why the set of justified blocks is parametrized by G.
 - In a view G, if A ∈ J(G) (equivalently, A is justified), and A →−J B is a supermajority link with h(B) = h(A) + 1, then we say A ∈ F (G) (equivalently, A is finalized).

## Gasper
combination of the GHOST and Casper FFG ideas.

 - 4 Main Protocol: Gasper  
 (Epoch boundary) pairs of a chain: given a chain, certain blocks are picked out, ideally one per epoch, to play the role of Casper’s checkpoints. However, a block may appear more than once as a checkpoint on the same chain (this is a nuance not found in Casper; we expound on this in Section 4.1), so we use ordered pairs (B, j), where B is a block and j is an epoch, to disambiguate. These will be called epoch boundary pairs, or pairs for short.

 - 4.1 Epoch Boundary Blocks and Pairs
  Recall any particular block B uniquely determines a chain, chain(B). For a block B and an epoch j, define EBB(B, j), the j-th epoch boundary block of B, to be the block with the highest slot less than or equal to jC in chain(B). Let the latest such block be LEBB(B), or the last epoch boundary block (of B). We make a few observations:

 - For every block B, EBB(B, 0) = B_genesis.
 - More generally, if slot(B) = jC for some epoch j, B will be an epoch boundary block in
every chain that includes it.
 - However, without such assumptions, a block could be an epoch boundary block in some chains but not others.
 
## よくわからない
 - supermajority linkがあると，どうなるのか，何の役に立つのか
 - supermajority linkとjustifidの関係
 - epoch boundary blockとepochの対応  
 まず，FFGにおけるcheckpointとを拡張したものが，epoch boundary block  

 For a block B and an epoch j, define EBB(B, j), the j-th epoch boundary block of B, to be the block with the highest slot less than or equal to jC in chain(B). Let the latest such block be LEBB(B), or the last epoch boundary block (of B).   
 4.1 章のFigure4の言わんとすることは，FFGにおいてcheckpointは，slot(B) = jCの各epochの境界ブロックだったが，Gasperにおけるcheckpoint すなわち,epoch boundary blockの該当するepochは必ずしもep(B)ではない　===>？？  

 To disambiguate situations like these, we add precision by introducing epoch boundary pairs (or pairs for short) (B, j), where B is a block and j is an epoch. Our main concepts of justification and finalization will be done on these pairs. Given a pair P = (B, j), we say P has attestation epoch j, using the notation aep(P ) = j, which is not necessarily the same as ep(B).  

 - FFGのfinalizationの条件  
 >  Finalization [1]  
 Only the 2nd, 3rd, and 4th most recent epochs can be finalized. This is done by checking:  
 Which epochs (within the past 4) are used as the source of the most recent justifications.  
 That the epochs between source and target are also justified (if any exist).  
 If these conditions are satisfied, the source is finalized. Note that we only consider k=1 and k=2 finality rules discussed in section 4.4.3 of the draft paper.  

 > 4.5 Finalization   
  
 Given our notion of justification and a new fork-choice rule, we are now ready to define the notion of finalization. Finalization is a stronger notion of justification in the sense that the moment any view considers a block B as finalized for some j, no view will finalize a block conflicting with B unless the blockchain is (1/3)-slashable.  

>  **Definition 4.9.** For a view G, we say (B0, j) is finalized (specifically, k-finalized) in G if (B0, j) = (Bgenesis,0)orifthereisanintegerk≥1andblocksB1,...,Bk ∈view(G)suchthatthefollowing holds:   
 - (B0,j),(B1,j+1),...,(Bk,j+k)areadjacentepochboundarypairsinchain(Bk);    
 - (B0,j),(B1,j+1),...,(Bk−1,j+k−1)areallinJ(G);    
 - (B0,j)→−J (Bk,j+k).      
We define F (G) to be the set of finalized pairs in the view G; we also say that a block B is finalized 
if (B,j) ∈ F(G) for some epoch j.   

 > 8.5 A Four-Case Finalization Rule   
 
 While Gasper’s Definition 4.9 captures the “general” version of the mathematical idea of finaliza- tion, the proposed implementation uses a “reduced” version only looking at the last 4 epochs for practicality. In particular, let B1, B2, B3, B4 be epoch boundary blocks for consecutive epochs, with B4 being the most recent epoch boundary block.

[[1]Phase0 for Humans v0100](https://notes.ethereum.org/@djrtwo/Bkn3zpwxB?type=view#Phase-0-for-Humans-v0100)

 [[2]Ethereum2.0の情報まとめ](https://notes.ethereum.org/@protolambda/eth2_start#Eth2-Information)