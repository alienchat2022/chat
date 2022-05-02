<script>
  let metamaskErrorMessage,
    matamaskErrorState = false,
    connectHeading = "ONLY FOR MEMBERS",
    mintOrBuyMessage = false;
  export let userToken;
  export let checkIfUserExist;
  let balance;
  async function getBalance() {
    const { address, contractMethods } = await nft.connectMetamask();
    balance = await contractMethods.getBalance(address);
    return balance;
  }

  // get metamask account
  async function getAccount() {
    try {
      const accounts = await ethereum.request({
        method: "eth_requestAccounts",
      });
      let currentBalance = await getBalance();
      if (currentBalance > 0) {
        userToken = accounts[0];
        checkIfUserExist();
      } else {
        connectHeading = "You should buy or mint Alien";
        mintOrBuyMessage = true;
        console.log("You don`t have permitions");
      }
    } catch (e) {
      if (e.code == "-32002") {
        metamaskErrorMessage = "You should unlock your metamask";
        matamaskErrorState = true;
      }
    }
  }


  async function connectMetamaskWalet() {
    if (typeof window.ethereum !== "undefined") {
      let networkCorrect = nft.checkMetamaskNetwork();
      if (networkCorrect) {
        getAccount();
      } else {
        metamaskErrorMessage = "Change MetaMask network";
        matamaskErrorState = true;
        setTimeout(() => {
          matamaskErrorState = false;
        }, 2500);
      }
    } else {
      window.open(
        "https://metamask.app.link/dapp/alien-airway.webflow.io",
        "_system"
      );
    }
  }
</script>

<div class="connect__pop-up">
  {#if !mintOrBuyMessage}
    <div class="connect__pop-up-items">
      <p>{connectHeading}</p>
      <p class="errorMessage" class:active={matamaskErrorState}>
        {metamaskErrorMessage}
      </p>
      <button on:click={connectMetamaskWalet}>Connect Metamask</button>
    </div>
  {:else if mintOrBuyMessage}
    <div class="connect__pop-up-items">
      <p>{connectHeading}</p>
      <p class="errorMessage" class:active={matamaskErrorState}>
        {metamaskErrorMessage}
      </p>
      <a href="/home">Go to Home Page</a>
    </div>
  {/if}
</div>

<style>
  .connect__pop-up {
    position: fixed;
    top: 0px;
    z-index: 5;
    display: flex;
    width: 100%;
    height: 100%;
    -webkit-box-pack: center;
    -webkit-justify-content: center;
    -ms-flex-pack: center;
    justify-content: center;
    -webkit-box-align: start;
    -webkit-align-items: flex-start;
    -ms-flex-align: start;
    align-items: flex-start;
    backdrop-filter: blur(8px);
    background-color: rgba(19, 4, 39, 0.85);
  }

  .connect__pop-up-items {
    display: -webkit-box;
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex;
    margin-top: 20%;
    -webkit-box-orient: vertical;
    -webkit-box-direction: normal;
    -webkit-flex-direction: column;
    -ms-flex-direction: column;
    flex-direction: column;
    -webkit-box-pack: center;
    -webkit-justify-content: center;
    -ms-flex-pack: center;
    justify-content: center;
    -webkit-box-align: center;
    -webkit-align-items: center;
    -ms-flex-align: center;
    align-items: center;
    position: relative;
  }
  .connect__pop-up-items p {
    margin-bottom: 24px;
    color: #fff;
    font-size: 32px;
    line-height: 39.01px;
    font-weight: 900;
    text-transform: uppercase;
  }
  .connect__pop-up-items p.errorMessage {
    position: absolute;
    top: 60px;
    opacity: 0;
    -webkit-transition: opacity 200ms ease;
    transition: opacity 200ms ease;
    color: #f07d7f;
    font-family: Montserrat, sans-serif;
    font-size: 16px;
    line-height: 26px;
    text-transform: inherit;
    font-weight: 400;
  }
  p.errorMessage.active {
    opacity: 1;
  }
  .connect__pop-up-items button,
  .connect__pop-up-items a {
    padding: 17.5px 51px;
    border-radius: 10px;
    background-color: #c092ff;
    box-shadow: 0 0 19px 0 rgb(0 0 0 / 5%);
    -webkit-transition: all 200ms ease;
    transition: all 200ms ease;
    color: #130427;
    font-size: 14px;
    line-height: 17.07px;
    font-weight: 700;
    text-transform: uppercase;
    border: none;
    margin-top: 60px;
    cursor: pointer;
  }
  @media screen and (max-width: 991px) {
    .connect__pop-up-items p {
      font-size: 29px;
      line-height: 32.01px;
    }
    .connect__pop-up-items {
      margin-top: 40%;
    }
  }
</style>
