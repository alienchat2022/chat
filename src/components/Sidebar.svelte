<script>
  export let user;
</script>

<div class="left__side">
  <div class="left__side--top">
    <div class="chat__names">
      <p class="chat_user-name">{user.userName}</p>
      <p class="chat_user-role">{user.userRole}</p>
    </div>

    <div
      class="close-chat"
      on:click={() => {
        location.reload();
      }}
    >
      <img
        src="https://uploads-ssl.webflow.com/623494ba6746d1d287d735b3/6253cec920ab868ad5498b16_logout.svg"
        alt=""
      />
    </div>
  </div>
  <div class="left__side--main">
    <div class="channel">
      <!-- <img
        src="https://uploads-ssl.webflow.com/623494ba6746d1d287d735b3/6253cec9f871710806fa1fd7_hashicon.svg"
        alt="hash icon"
      /> -->
      <svg class="hash-icon" width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path fill-rule="evenodd" clip-rule="evenodd" d="M5.88633 21C5.57523 21 5.33966 20.7189 5.39403 20.4126L5.99977 17H2.59487C2.28425 17 2.04881 16.7198 2.10235 16.4138L2.27735 15.4138C2.31922 15.1746 2.52698 15 2.76987 15H6.34977L7.40977 9H4.00487C3.69425 9 3.45881 8.71977 3.51235 8.41381L3.68735 7.41381C3.72922 7.17456 3.93698 7 4.17987 7H7.75977L8.39652 3.41262C8.43889 3.17391 8.64639 3 8.88882 3H9.8732C10.1843 3 10.4199 3.28107 10.3655 3.58738L9.75977 7H15.7598L16.3966 3.41262C16.4389 3.17391 16.6464 3 16.8889 3H17.8732C18.1843 3 18.4199 3.28107 18.3655 3.58738L17.7598 7H21.1647C21.4753 7 21.7108 7.28023 21.6572 7.58619L21.4822 8.58619C21.4404 8.82544 21.2326 9 20.9897 9H17.4098L16.3498 15H19.7547C20.0653 15 20.3008 15.2802 20.2472 15.5862L20.0722 16.5862C20.0304 16.8254 19.8226 17 19.5797 17H15.9998L15.363 20.5874C15.3207 20.8261 15.1132 21 14.8707 21H13.8864C13.5753 21 13.3397 20.7189 13.3941 20.4126L13.9998 17H7.99976L7.36301 20.5874C7.32064 20.8261 7.11313 21 6.8707 21H5.88633ZM9.41021 9L8.3502 15H14.3502L15.4102 9H9.41021Z" fill="#6332A6"/>
        </svg>
      <p>general</p>
    </div>
  </div>
</div>

<style>
  .close-chat{
    cursor: pointer;
  }
  .left__side {
    background: linear-gradient(180deg, #130427 0%, #300455 100%);
    height: 100%;
  }
  .left__side--top {
    padding: 3px 18px 0px 14px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    height: 48px;
    border-bottom: 1px solid #2e1054;
  }
  .chat_user-name {
    font-weight: 700;
    font-size: 14px;
    line-height: 17px;
    color: #ffffff;
  }
  .chat_user-role {
    font-weight: 400;
    font-size: 12px;
    line-height: 15px;
    color: #b9bbbe;
  }
  .left__side--main {
    padding: 28px 14px;
  }

  .channel {
    display: flex;
    align-items: center;
    padding: 4px;
    width: 100%;
    max-width: 237px;
    background: #c092ff;
    border-radius: 4px;
  }
  .channel p {
    font-weight: 500;
    font-size: 16px;
    line-height: 20px;
    color: #18042e;
    padding-left: 4px;
  }
  @media screen and (max-width: 991px) {
    .close-chat img{
      width: 24px;
      height: 24px;
    }
    .left__side--top {
      padding: 3px 18px 0px 20px;
    }
    .channel {
      background: transparent;
      padding: 0;
    }
    .left__side--main {
      padding: 12px 20px;
    }
    .channel p {
      color: #C092FF;
      text-transform: capitalize;
    }
    svg.hash-icon path{
      fill: #C092FF;
    }
  }
</style>
