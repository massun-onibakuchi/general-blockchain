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
## コードを読む５
### ClockAuctionコントラクト
Pausable,ClockAuctionBaseを継承している．
The ERC-165 interface signature for ERC-721.については，
日本語Ref:https://y-nakajo.hatenablog.com/entry/2018/02/04/235505  
EIP165:https://github.com/ethereum/EIPs/blob/master/EIPS/eip-165.md  
StackExchangeに投稿された質問
:https://ethereum.stackexchange.com/search?q=eip+165+++interface  
:https://ethereum.stackexchange.com/questions/45431/how-is-a-contract-determined-to-be-erc20-compliant/45605#45605  
:https://ethereum.stackexchange.com/questions/71560/erc721-interface-id-registration/71631#71631
:https://ethereum.stackexchange.com/questions/40984/how-do-i-check-in-solidity-if-interface-is-supported/40989#40989
ERCトークンについては，https://qiita.com/shiki_tak/items/99785f509964d920485d
```solidity
/// @title Clock auction for non-fungible tokens.
/// @notice We omit a fallback function to prevent accidental sends to this contract.

    /// @dev The ERC-165 interface signature for ERC-721.
    ///  Ref: https://github.com/ethereum/EIPs/issues/165
    ///  Ref: https://github.com/ethereum/EIPs/issues/721
    bytes4 constant InterfaceSignature_ERC721 = bytes4(0x9a20483d);

    /// @dev Constructor creates a reference to the NFT ownership contract
    ///  and verifies the owner cut is in the valid range.
    /// @param _nftAddress - address of a deployed contract implementing
    ///  the Nonfungible Interface.
    /// @param _cut - percent cut the owner takes on each auction, must be
    ///  between 0-10,000.
    function ClockAuction(address _nftAddress, uint256 _cut) public {
        require(_cut <= 10000);
        ownerCut = _cut;

        ERC721 candidateContract = ERC721(_nftAddress);
        require(candidateContract.supportsInterface(InterfaceSignature_ERC721));
        nonFungibleContract = candidateContract;
    }
```
The ERC-165 standard provides a standard for generating an identifier for an interface.

This way, if the identifier of a given version of a given interface matches with your contract, it's a way of ensuring that your contract is compliant with that interface.

## 重要なところ

## わからないところ
 - ERC165インターフェースについて
## コードを読む第5章に続く
