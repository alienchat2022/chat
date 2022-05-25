<script>
  let metamaskErrorMessage,
    matamaskErrorState = false,
    connectHeading = "ONLY FOR MEMBERS",
    mintOrBuyMessage = false;
  export let userToken;
  export let checkIfUserExist;
  let balance;
  let connectButtonText = 'Connect Metamask'; 

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

// connect wallet
  async function connectMetamaskWalet() {
    if (typeof window.ethereum !== "undefined") {
      connectButtonText = 'Connecting...'
      let networkCorrect = nft.checkMetamaskNetwork();
      if (networkCorrect) {
        getAccount();
      } else {
        connectButtonText = 'Connect Metamask';
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
  <div class="connect__pop-up-wrapper">
    <img class="connect__pop-up-img-mob top" src="https://uploads-ssl.webflow.com/623494ba6746d1d287d735b3/6274bc5c9fea49802900fad5_UFO_3-min.png" alt="pop up img">
    <div class="connect__pop-up-items">
      {#if !mintOrBuyMessage}
     
      <img class="connect__pop-up-img" src="https://global-uploads.webflow.com/623494ba6746d1d287d735b3/6270f418dfac0164e4d550c8_pop-up-planes-p-1080.png" alt="pop up img">
      <p>{connectHeading}</p>
      <p class="errorMessage" class:active={matamaskErrorState}>
        {metamaskErrorMessage}
      </p>
      <button on:click={connectMetamaskWalet}>{connectButtonText}</button>
      
      {:else if mintOrBuyMessage}
      <img class="connect__pop-up-img" src="https://global-uploads.webflow.com/623494ba6746d1d287d735b3/6270f418dfac0164e4d550c8_pop-up-planes-p-1080.png" alt="pop up img">
      <p>{connectHeading}</p>
      <p class="errorMessage" class:active={matamaskErrorState}>
        {metamaskErrorMessage}
      </p>
      <a href="/home">Go to Home Page</a>
      {/if}
    </div>
    <img class="connect__pop-up-img-mob bottom" src="https://uploads-ssl.webflow.com/623494ba6746d1d287d735b3/6274bd22efef3160bd8e8c13_Planet-min.png" alt="pop up img">
  
  </div>

</div>

<style>
  .connect__pop-up-img-mob{
    display: none;
  }
  .connect__pop-up {
    position: fixed;
    top: 0px;
    z-index: 5;
    display: flex;
    width: 100%;
    height: 100vh;
    -webkit-box-pack: center;
    -webkit-justify-content: center;
    -ms-flex-pack: center;
    justify-content: center;
    -webkit-box-align: start;
    -webkit-align-items: flex-start;
    -ms-flex-align: start;
    align-items: flex-start;
    background-image: url(https://global-uploads.webflow.com/623494ba6746d1d287d735b3/6270f3e9e3d11daa7e9a5c40_pop-up-bg.png), linear-gradient(180deg, #130427, #320459);
    background-position: 50% 50%, 0px 0px;
    background-size: cover, auto;
  }
  .connect__pop-up-img{
    width: 60%;
    margin-top: 100px;
    margin-bottom: 60px;
  }

  .connect__pop-up-items {
    display: -webkit-box;
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex;
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
    top: auto;
    bottom: 70px;
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
   
  }
  @media screen and (max-width: 768px) {
    .connect__pop-up-items p.errorMessage{
      top: 46px;
    }
    .connect__pop-up-items p{
      text-align: center;
    }
    .connect__pop-up-items button,
  .connect__pop-up-items a{
    margin-top: 40px;
  }
    .connect__pop-up-img{
      display: none;
    }
    .connect__pop-up-img-mob{
    display: block;
  }
  .connect__pop-up-img-mob.top{
    width: 45%;
    margin: 65px auto 31px auto;
  }
  .connect__pop-up-img-mob.bottom{
    width: 40%;
    margin-top: 62px;
    margin-right: 20px;
    margin-left: auto;
  }
    .connect__pop-up-img{
      display: none;
    }
    .connect__pop-up {
      background-image: url(https://uploads-ssl.webflow.com/623494ba6746d1d287d735b3/6274bb762ccafd758aec83d2_pop-up-mob-bg.png), linear-gradient(180deg, #130427, #320459);
    }
   
  }
</style>
