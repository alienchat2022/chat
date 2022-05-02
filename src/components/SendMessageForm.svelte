<script>
  import { EmojiButton } from "@joeattardi/emoji-button";
  export let sendMessage;
  export let messageText = "";
  export let tempMessageWrap;
  export let tepmErrorState;
  export let tepmError;

  import { resize } from "../resize";
  let textarea = null,
       height = 48;
  function onResize(e) {
    console.log(e.target)
    textarea = e.target;
    height = e.target.CR?.height;
    console.log(height)
  }

  $: rows = (messageText.match(/\n/g) || []).length + 1 || 1;

  function showEmoji() {
    const picker = new EmojiButton({
      zIndex: 2,
      showSearch: false,
      theme: "dark",
    });
    picker.togglePicker(this);

    picker.on("emoji", (selection) => {
      // handle the selected emoji here
      messageText += selection.emoji;
    });
  }


  function checkKeyCode(e){
    if(e.keyCode === 13 && !e.shiftKey) {
      e.preventDefault()
      sendMessage()
    }
  }
</script>

<div class="chat__form">
  <form on:submit|preventDefault={sendMessage}>
    <div class="form__wrapper">
      <div class="input__wrapper">
        <textarea
        name=""
        id="inputForm"
        bind:value={messageText}
        {rows}
        bind:this={textarea}
        use:resize
        on:resize={onResize}
        on:keydown={checkKeyCode}
        style="--height: auto"
      />
      </div>
     
     
      {#if messageText === ""}
        <div class="placeholder">Message #general</div>
      {/if}
      <div class="emoji-trigger" on:click={showEmoji}>
        <img
          src="https://uploads-ssl.webflow.com/623494ba6746d1d287d735b3/6253cec967bd41c07fec8137_Slightly-smiling-face.svg"
          alt="smile"
        />
      </div>
      <button type="submit" id="submit">
        <img
          src="https://uploads-ssl.webflow.com/623494ba6746d1d287d735b3/62594ecca696a51ecf71d653_Send.svg"
          alt="send message"
        /></button
      >
  
    </div>

    {#if tepmErrorState}
      <div class="tepm__message" bind:this={tempMessageWrap}>
        <div class="tepm__message--text">{tepmError}</div>
      </div>
    {/if}
  </form>
</div>

<style>
  textarea {
    border: none;
    background-color: transparent;
    resize: none;
    outline: none;
    resize: none;
  }

  .chat__form {
    position: relative;
  }

  #inputForm {
    position: relative;
    width: 100%;
    padding: 10px 25px 14px 12px;
    background: #2d0450;
    margin: 0;
    border: none;
    color: white;
    height: var(--height);
    min-height: 45px;
    resize: horizontal;
    overflow: hidden;

  }
  #inputForm:focus {
    z-index: 3;
    background: #2d0450;
  }
  #inputForm:focus-visible {
    border: none;
  }
  .placeholder {
    font-weight: 400;
    font-size: 16px;
    line-height: 20px;
    color: #c092ff;
    position: absolute;
    top: 13px;
    left: 15px;
    z-index: 2;
  }
  .form__wrapper {
    background: #2d0450;
    overflow: hidden;
    display: flex;
    align-items: flex-end;
    border-radius: 8px;
  }
  .input__wrapper{
    position: relative;
    width: 100%;
  }

  .input__wrapper::after{
    content: '';
    position: absolute;
    bottom: 0;
    right: 0;
    width: 25px;
    height: 25px;
    z-index: 3;
    background: #2d0450;
  }

  #submit {
    cursor: pointer;
    width: 48px;
    height: 48px;
    color: transparent;
    border: none;
    margin: 0;
    border-radius: 0px 8px 8px 0px;
    background-color: #c092ff;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .emoji-trigger {
    cursor: pointer;
    width: 48px;
    height: 48px;
    background: #2d0450;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .emoji-trigger img {
    max-width: 24px;
  }
  .tepm__message {
    width: 300px;
    border-radius: 8px 8px 8px 8px;
    background-color: #de695f;
    color: whitesmoke;
    padding: 10px;
    position: absolute;
    top: -45px;
    left: -5px;
    transition: all ease 2.2s;
  }
  .tepm__message--text {
    font-weight: 600;
    font-size: 14px;
    line-height: 20px;
    color: #130427;
  }
  @media screen and (max-width: 991px) {
    .emoji-trigger {
      right: 55px;
    }
    #inputForm {
      padding: 12px 8px;
    }
    .tepm__message {
      width: 220px;
    }
  }
</style>
