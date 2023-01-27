https://github.com/zkSNACKs/WalletWasabi/pull/9925#issue-1530835591

> ## TL;DR
> **Proof of Concept for #7487**
> 
> With this PR, one can ❄️"freeze"/exclude🧊 his or her coins from being selected for a **CoinJoin** by the `CoinJoinManager`. You can achive it by double-clicking on a **Coin**🪙 in the **CoinList**🗒️(CTRL+C+D).
> 
> ## Description
> As this PR is only but a **PoC**, there is still work to do, mainly on the UI side (*wink* @zkSNACKs/visual-design-group 😉 ). Right now nothing indicates that a coin is excluded, you can check the Wallet file for the `"ExcludedCoinsFromCoinJoin" = [ ]`. But the main concept in the bussiness logic is this.
> 
> * Select and/or exclude Coins from the coinlist;
> * These Coins won't be participating in CoinJoins;
> * These Coins's `Outpoint` will be saved in the `KeyManager` (including the `Wallet` file);
> * Thanks to that, upon loading the `Wallet`, we also set by `Outpoint`, which Coins should still be excluded;
> * This is good, as one might move his/her wallet to another computer, but don't have to re-select 10s if not 100s Coins.
> 
> ## TODOs and Questions
> * The UI should have an icon next to the Confirmation check and CoinJoining icon, indicationg if it's "frozen"? (maybe an icecube 🧊 , or snowflake ❄️)?
> * Excluding a Coin should be a more obvious thing then double clicking _(wow)_.
> * There needs to be a way to [select-deselect more coins in one step](https://github.com/zkSNACKs/WalletWasabi/issues/7487#issuecomment-1294373008).
> 
> ### And the question:
> If we add this feature, the proper place for it is the CoinList. Which all started as a developer feature, but as I heard, there were user complains about this, "why I need to come to GitHub to find out that this feature exists". Until now it's like they found an easter egg🥚. At this point, we need to reconsider making a dedicated button for the Coinlist in the Wallet.



Testing result:

Business logic function works as expected (coin excluded from CoinJoin process) ✅ 

**positive test:**
1. open coin list with CTRL +C +D 
» result: Wallet Coins (UTXOs) opens
<img width="1028" alt="image" src="https://user-images.githubusercontent.com/123734066/215111169-20ac77d7-a595-4f83-95b9-46451dc85156.png">

2. double click on all coins in the coin list »» **UI part missing** ❗ make it visible for the user that coin is selected to be excluded
3. click on Done
4. go to Search bar on the top
5. type: Wallet Folder
6. open Wallet Folder
7. open json file with text file viewer
» **result**: Excluded coin's outpoint appears in the log
<img width="753" alt="image" src="https://user-images.githubusercontent.com/123734066/215106091-517cd02c-11d6-46ab-9d10-f3b8c6cf43a2.png">

8. run the code from Terminal (WalletWasabi.Fluent.Desktop - ps « dotnet run)
9. check CoinJoin Manager log message in terminal:
<img width="1088" alt="image" src="https://user-images.githubusercontent.com/123734066/215106882-d184e493-1eae-49e3-9d03-4cc0dff90937.png">
» result:
Wallet (xy): No candidate coins available to mix.

**inverse test:**
1. go back to Wallet
2. open coin list with CTRL +C +D 
» result: Wallet Coins (UTXOs) opens
3. double click on the coin in the coin list that was previously excluded
4. click on Done
5. click refresh to check json file with text file viewer / browser / Notepad ++
6. check that **no**/previously selected coin's outpoint appears in the Wallet json
field name: ExcludedCoinsFromCoinJoin

<img width="699" alt="image" src="https://user-images.githubusercontent.com/123734066/215110822-80e6ee79-aeed-412c-9d78-f48dd949119d.png">

7. go to Terminal
8. check that CoinJoin started 
» **result**:
Manager log contains the following lines:
Coin selection started
Selected the final selection candidate: 1 coins, x.xxxxxxx BTC

<img width="1192" alt="image" src="https://user-images.githubusercontent.com/123734066/215124164-e3dd8c46-56d1-4f9d-9185-35416463f66f.png">

9. Check Wallet if coin join progress started / in progress
Music box text message appears: Waiting for CoinJoin to start

In this test case transaction immediately finished Music box text message appears: 
Hurray! Your funds are private

<img width="1024" alt="image" src="https://user-images.githubusercontent.com/123734066/215126984-c09e52ff-f5b6-4bb4-8328-f42f3ff68630.png">
