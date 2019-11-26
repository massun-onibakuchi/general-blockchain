# Cryptokittyコードを読む第４章
ソースコードを読んだメモ


## 参照リンク
 - https://blockgeeks.com/guides/cryptokitties/
 - https://github.com/cryptocopycats/awesome-cryptokitties/blob/master/contracts
 - https://medium.com/loom-network/how-to-code-your-own-cryptokitties-style-game-on-ethereum-7c8ac86a4eb3
ERC20,721に実装される関数の説明とトークンを用いたサービスの設計について
 - https://blockchain.gunosy.io/entry/token-summary-and-token-contract#approve--transferFrom--allowance

## コントラクトの構成
ここで用いているコードは　こちらのGithub https://github.com/cryptocopycats/awesome-cryptokitties/blob/master/contracts 
からの抜粋にコメントをつけたもの

#### 実際にデプロイされているコントラクト
 - KittyCore (Open Source)
 - SaleClockAuction (Open Source)
 - SiringClockAuction (Open Source)

## コードを読む４
コードを読む３の続き  
３章まででkittyCoreの説明は終えたので，Auctionコントラクトに入る  
### ClockAuctionBaseコントラクト
```solidity
// @title Auction Core
/// @dev Contains models, variables, and internal methods for the auction.
/// @notice We omit a fallback function to prevent accidental sends to this contract.

contract ClockAuctionBase {

    // Represents an auction on an NFT
    struct Auction {
        // Current owner of NFT
        address seller;
        // Price (in wei) at beginning of auction
        uint128 startingPrice;
        // Price (in wei) at end of auction
        uint128 endingPrice;
        // Duration (in seconds) of auction
        uint64 duration;
        // Time when auction started
        // NOTE: 0 if this auction has been concluded
        uint64 startedAt;
    }

    // Reference to contract tracking NFT ownership
    ERC721 public nonFungibleContract;

    // Cut owner takes on each auction, measured in basis points (1/100 of a percent).
    // Values 0-10,000 map to 0%-100%
    uint256 public ownerCut;
    
    // Map from token ID to their corresponding auction.
    mapping (uint256 => Auction) tokenIdToAuction;
```
_escrow :このエスクローとしてこのコントラクトに，所有権を委託する
_transfer :委託されていた猫を新しい所有者に移す．
```solidity
/// @dev Escrows the NFT, assigning ownership to this contract.
    /// Throws if the escrow fails.
    /// @param _owner - Current owner address of token to escrow.
    /// @param _tokenId - ID of token whose approval to verify.
    function _escrow(address _owner, uint256 _tokenId) internal {
        // it will throw if transfer fails
        nonFungibleContract.transferFrom(_owner, this, _tokenId);
    }
/// @dev Transfers an NFT owned by this contract to another address.
    /// Returns true if the transfer succeeds.
    /// @param _receiver - Address to transfer NFT to.
    /// @param _tokenId - ID of token to transfer.
    function _transfer(address _receiver, uint256 _tokenId) internal {
        // it will throw if transfer fails
        nonFungibleContract.transfer(_receiver, _tokenId);
    
```

次に，入札するメソッド_bidを定義されている．送金が絡んでくるので，セキュリティ的に堅いsolidな設計が特に要求される．入札対象が存在するかや，入札額_bidAmountがpriceよりも大きいことを確認し，値のチェックをまず終える．次に，送金を行いたいが，その前に，reentrancy attackを防ぐために，送金よりも前に，このオークション自体を消す．その後，手数料auctioneerCutを引いて残った売り手の利益sellerProceedsを送金する．超過した入札額の分は入札者（落札者）msg.senderに返金する.このように，CheckしてからEffectsを行って最後に，Interactionsの手順を踏んでいる
*複雑なメソッドの途中で送金を行うことは，推奨されない，reentrancy attacksについてはdocにも記載がある．withdrawal-pattern  
Ref：https://solidity-jp.readthedocs.io/ja/latest/security-considerations.html#re-entrancy  
:https://solidity-jp.readthedocs.io/ja/latest/security-considerations.html#security-considerations
DOSについては，要調査．

```solidity
/// @dev Computes the price and transfers winnings.
    /// Does NOT transfer ownership of token.
    function _bid(uint256 _tokenId, uint256 _bidAmount)
        internal
        returns (uint256)
    {
        // Get a reference to the auction struct
        Auction storage auction = tokenIdToAuction[_tokenId];

        // Explicitly check that this auction is currently live.
        // (Because of how Ethereum mappings work, we can't just count
        // on the lookup above failing. An invalid _tokenId will just
        // return an auction object that is all zeros.)
        require(_isOnAuction(auction));

        // Check that the bid is greater than or equal to the current price
        uint256 price = _currentPrice(auction);
        require(_bidAmount >= price);

        // Grab a reference to the seller before the auction struct
        // gets deleted.
        address seller = auction.seller;

        // The bid is good! Remove the auction before sending the fees
        // to the sender so we can't have a reentrancy attack.
        _removeAuction(_tokenId);

        // Transfer proceeds to seller (if there are any!)
        if (price > 0) {
            // Calculate the auctioneer's cut.
            // (NOTE: _computeCut() is guaranteed to return a
            // value <= price, so this subtraction can't go negative.)
            uint256 auctioneerCut = _computeCut(price);
            uint256 sellerProceeds = price - auctioneerCut;

            // NOTE: Doing a transfer() in the middle of a complex
            // method like this is generally discouraged because of
            // reentrancy attacks and DoS attacks if the seller is
            // a contract with an invalid fallback function. We explicitly
            // guard against reentrancy attacks by removing the auction
            // before calling transfer(), and the only thing the seller
            // can DoS is the sale of their own asset! (And if it's an
            // accident, they can call cancelAuction(). )
            seller.transfer(sellerProceeds);
        }

        // Calculate any excess funds included with the bid. If the excess
        // is anything worth worrying about, transfer it back to bidder.
        // NOTE: We checked above that the bid amount is greater than or
        // equal to the price so this cannot underflow.
        uint256 bidExcess = _bidAmount - price;

        // Return the funds. Similar to the previous transfer, this is
        // not susceptible to a re-entry attack because the auction is
        // removed before any transfers occur.
        msg.sender.transfer(bidExcess);

        // Tell the world!
        AuctionSuccessful(_tokenId, price, msg.sender);

        return price;
    }

```

## 重要なところ
 - 送金のメソッドtransfer と　reentrancy attacksを防ぐやり方．
送金の前に先に，状態変数の変化を行う．
 - コード実行手順パターン
CheckしてからEffectsを行って最後に，Interactions
 > https://solidity-jp.readthedocs.io/ja/latest/security-considerations.html#use-the-checks-effects-interactions-pattern
ほとんどのファンクションはまずいくつかのチェックで始まります（誰がファンクションを呼び出したのか、引数は範囲に入っているか、十分なEtherを送っているか、その人はトークンを持っているかなど）。これらのチェックは最初に終わらせなければいけません。

2つ目のステップとして、全てのチェックが通ったら、現在のコントラクトの状態変数の処理を行うはずです。 どんなファンクションでも他のファンクションとの相互作用は最後の最後に行われるはずです。

昔のコントラクトはいくつかの処理をデプロイしてからexternalのファンクションコールがノンエラーステートを返すのを待っていました。これは上記で説明したre-entrancy問題のため、しばしば大きな失敗となります。

さらに、既知のコントラクトをコールしても未知のコントラクトをどんどんコールする可能性があるということに気をつけて下さい。そのため、おそらく常にこのパターンを適用するのが良いでしょう。
## わからないところ
 - DOS攻撃　と　その防ぎかた，
 - 送金のメソッドcall, transfer...の仕様．	
## コードを読む第5章に続く
