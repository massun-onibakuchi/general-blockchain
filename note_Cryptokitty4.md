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
_transfer :o委託されていた猫を新しい所有者に移す．
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
    }
```
## 重要なところ
Payable ，fallback function の挙動，使い方
## コードを読む第４章に続く
