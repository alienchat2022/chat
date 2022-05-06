<script>
  import { doc, deleteDoc, updateDoc } from "firebase/firestore";
  import { clickOutside } from "../clickOutside.js";
  export let message;
  export let user;
  export let db;
  export let scrollToBottom;
  export let index;
  export let messages;
  let editableDiv;
  

  let messageText = message.body;
  let updateState = false;
  let changeWrapeerState = false;
 

  function handleClickOutsideWrapper(event) {
    changeWrapeerState = false;
  }
  function handleClickOutsideInput(event) {
    messageText = message.body;
    updateState = false;
  }
  function handleWrapperState() {
    if (changeWrapeerState) {
      changeWrapeerState = false;
    } else {
      changeWrapeerState = true;
    }
  }

  async function deleteMessage() {
    changeWrapeerState = false;
    await deleteDoc(doc(db, "Messages", message.id));
    
  }

  function updateMessageState() {
    
    updateState = true;
    changeWrapeerState = false;
    if (messages.length - 1 === index) {
      scrollToBottom();
    }
  }
  async function updateMessage() {
    const currentDoc = doc(db, "Messages", message.id);

    // Set the "capital" field of the city 'DC'
    await updateDoc(currentDoc, {
      body: messageText,
    });
    updateState = false;
    changeWrapeerState = false;
  }
  function checkUpdateKeyCode(e){
   if(e.keyCode === 13 && !e.shiftKey) {
      e.preventDefault()
      updateMessage()
    }
  }

</script>

<li
  class:current={message.authorID === user.userToken}
  class="message--item"
  class:chenging={updateState}
>
  <div class="message_item--wrapper" class:editing={updateState}>
    <div class="user__data">
      <div class="user__name">{message.userName}</div>
      <div class="user__role">{message.userRole || "Passenger"}</div>
    </div>
    <div class="message__data">
      <div class="message__body">
        {#if updateState}
          <form
            on:submit|preventDefault={updateMessage}
            class="update__form"
            use:clickOutside
            on:click_outside={handleClickOutsideInput}
          >
            <div
              class="editable"
              contenteditable="true"
              bind:textContent={messageText}
              bind:this={editableDiv}
              on:keydown={checkUpdateKeyCode}
            >
              {messageText}
            </div>
            <button type="submit"
              ><img
                src="https://uploads-ssl.webflow.com/623494ba6746d1d287d735b3/625963374b316c6650e62265_check%201.svg"
                alt="update"
              />
            </button>
          </form>
        {:else if !updateState}
          {message.body}
        {/if}
      </div>
      <div class="message__time">
        {#if message.createdAt?.toDate}
          {`${message.createdAt.toDate().getHours()}:${
            message.createdAt.toDate().getMinutes() < 10
              ? "0" + message.createdAt.toDate().getMinutes()
              : message.createdAt.toDate().getMinutes()
          } ${message.createdAt.toDate().getHours() < 12 ? "am" : "pm"}`}
        {/if}
      </div>
    </div>
    {#if message.authorID === user.userToken && !updateState}
      <div class="change__message--icon" on:click={handleWrapperState}>
        <div class="dot-icon" />
      </div>
      {#if changeWrapeerState}
        <div
          class="change__message--wrapper"
          use:clickOutside
          on:click_outside={handleClickOutsideWrapper}
        >
          <div class="delete_message" on:click={deleteMessage}>Delete</div>
          <div class="update_message" on:click={updateMessageState}>Update</div>
        </div>
      {/if}
    {/if}
  </div>
</li>

<style>
  @import url("https://fonts.googleapis.com/css2?family=Montserrat:wght@400;500;700&display=swap");

  .message_item--wrapper{
    padding-right: 20px;
  }
  .message_item--wrapper.editing{
    padding-right: 0px;
  }
  .message--item {
    position: relative;
    font-style: normal;
    list-style: none;
    max-width: 340px;
    background: #59338c;
    border-radius: 14px 14px 14px 4px;
    padding: 8px;
    color: white;
    margin: 0 auto 8px 0;
  }
  .message--item.current {
    margin: 0 0 8px auto;
    border-radius: 14px 14px 4px 14px;
    background: #8f5dd3;
    text-align: right;
  }
  .message--item.current.chenging {
    max-width: 404px;
  }

  .delete_message:hover,
  .update_message:hover {
    transition: all ease 0.3s;
    opacity: 1;
  }
  .message--item.current .user__data {
    display: none;
  }
  .update__form {
    display: flex;
    max-width: 100%;
  }
  .update__form img{
    width: 24px;
    height: 24px;
  }
  .editable {
    width: 100%;
    max-width: 360px;
    border: 1px solid #ffffff;
    padding: 10px;
    border-radius: 8px;
     white-space: pre-wrap; 
     text-align: left;
  }
  .update__form button {
    cursor: pointer;
    width: 24px;
    height: 24px;
    margin-left: 4px;
    border: none;
    border-radius: 50%;
    color: transparent;
    background-color:  transparent;
    padding: 0;
    margin-top: 0;
    margin-bottom: 0;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .update__form button:hover {
    transition: all ease 0.3s;
    opacity: 0.6;
  }
  .user__data {
    display: flex;
    justify-content: space-between;
    margin-bottom: 4px;
  }
  .user__name {
    color: #c8504f;
    font-weight: 700;
    font-size: 12px;
    line-height: 15px;
  }
  .user__role,
  .message__time {
    font-weight: 400;
    font-size: 12px;
    line-height: 15px;
    color: #ab8cd4;
  }

  .change__message--wrapper {
    width: 82px;
    height: 72px;
    background: #472c6b;
    padding: 10px 14px;
    border-radius: 14px 14px 0px 14px;
    position: absolute;
    top: -48px;
    right: 22px;
    display: flex;
    align-items: center;
    flex-direction: column;
    justify-content: space-between;
  }
  .change__message--wrapper > * {
    font-weight: 600;
    font-size: 14px;
    line-height: 20px;
    cursor: pointer;
    color: #c092ff;
  }
  .change__message--wrapper div:hover {
    color: #ffffff;
  }
  .change__message--icon {
    position: absolute;
    top: 8.5px;
    right: 0px;
    width: 16px;
    height: 16px;
    cursor: pointer;
  }
  .dot-icon {
    position: relative;
    top: 7px;
    left: 3px;
    width: 3px;
    height: 3px;
    background: #fff;
    opacity: 1;
    border-radius: 50%;
  }
  .dot-icon::before,
  .dot-icon::after {
    content: "";
    position: absolute;
    width: 3px;
    height: 3px;
    background: #fff;

    border-radius: 50%;
  }
  .dot-icon::before {
    top: -5px;
    left: 0;
  }
  .dot-icon::after {
    bottom: -5px;
    left: 0;
  }

  .message__time {
    margin-top: 4px;
    text-transform: uppercase;
    text-align: right;
  }
  .message--item.current .message__time {
    color: #442a66;
  }

  .message__body {
    font-weight: 400;
    font-size: 14px;
    line-height: 19px;
    margin: 0;
    word-break: break-word;
    white-space: pre-line;
  }
  @media screen and (max-width: 991px) { 
    .message--item.current.chenging {
    width: 335px;
  }
  .change__message--wrapper {
    top: -40px;
    right: 32px;
  }
     .change__message--icon {
    top: 10.5px;
    right: 0px;
    width: 24px;
    height: 24px;
  }
  .message--item.current {
    padding: 8px 16px 8px 19px;
  }
  .dot-icon{
    left: 3px;
    width: 4.5px;
    height: 4.5px;
  }
  .dot-icon::before,
  .dot-icon::after {
    width: 4.5px;
    height: 4.5px;
  }
  .dot-icon::before {
    top: -7px;
  }
  .dot-icon::after {
    bottom: -7px;
  }
}
  @media screen and (max-width: 468px) {
    .message--item {
      max-width: 280px;
    }
  
  }
</style>
