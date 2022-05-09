<script>
  import * as firebase from "firebase/app";
  import {
    getFirestore,
    limit,
    onSnapshot,
    orderBy,
    query,
    serverTimestamp,
    collection,
    addDoc,
  } from "firebase/firestore";
  import { onDestroy, afterUpdate, beforeUpdate, onMount } from "svelte";
  import CreateUserForm from "./components/AuthForm.svelte";
  import MessageItem from "./components/Message-Item/MessageItem.svelte";
  import ConnectMetamask from "./components/ConnectMetamask.svelte";
  import ChatHead from "./components/ChatHead.svelte";
  import Sidebar from "./components/Sidebar.svelte";
  import SendMessageForm from "./components/Send-Form/SendMessageForm.svelte";
  import ChatTop from "./components/ChatTop.svelte";

  const firebaseConfig = {
    apiKey: "AIzaSyDc0mwx7dNlejQBrS_-cKZh3bI7DjB-zqs",
    authDomain: "svelte-chat-63d4c.firebaseapp.com",
    projectId: "svelte-chat-63d4c",
    storageBucket: "svelte-chat-63d4c.appspot.com",
    messagingSenderId: "773282306589",
    appId: "1:773282306589:web:2138c74591c369bc7584f4",
    measurementId: "G-1LT02K4PR0",
  };

  // Initialize Firebase
  const app = firebase.initializeApp(firebaseConfig);

  const db = getFirestore();

  $: userName = "";
  $: messageText = "";
  let user,
    userToken,
    unsubscribe = () => {},
    messages = [],
    isUpdating = false,
    updatingFormState = false,
    // autoscroll,
    chatMessageWrapper,
    tepmError,
    tepmErrorState = false,
    tempMessageWrap,
    chatHeight = 765,
    chatMain,
    chatForm;

  let innerWidth = window.innerWidth;

  beforeUpdate(() => {});

  afterUpdate(() => {
    if (chatMain && chatForm) {
      // set chat max height
      chatHeight =
        chatMain.clientHeight -
        chatForm.clientHeight -
        19;
      // scroll to bottom after component update
      chatMessageWrapper.scrollTo(0, chatMessageWrapper.scrollHeight);
    }
  });

  let q = query(collection(db, "Users"));
  let users = [];

  onMount(async () => {
    try {
      // get last 300 messages
      const q = query(
        collection(db, "Messages"),
        orderBy("createdAt", "desc"),
        limit(300)
      );
      // set it to new array and subscribe on updates
      unsubscribe = onSnapshot(
        q,
        { includeMetadataChanges: true },
        async (querySnapshot) => {
          updatingFormState = true;
          let temp = [];
          for (const doc of querySnapshot.docs) {
            if (doc.data().createdAt === null) {
              return false;
            }

            temp.push({
              ...doc.data(),
              id: doc.id,
            });
          }
          messages = [...temp];
          messages.reverse();
          updatingFormState = false;
        }
      );
    } catch (error) {
      console.error(error);
    }
  });
  onDestroy(() => {
    unsubscribe();
  });

  // check if user exist
  function checkIfUserExist() {
    isUpdating = true;
    // get all user data from server
    onSnapshot(q, async (querySnapshot) => {
      let temp = [];
      for (const doc of querySnapshot.docs) {
        temp.push({
          ...doc.data(),
        });
      }

      users = [...temp];

      // get user from local storage if it exist
      let userData = localStorage.getItem("userData");

      // find user by user token
      let findUser = findUserByToken(userToken);

      // user chache logic (with local storage)
      if (!userData && !findUser) {
        isUpdating = false;
        user = false;
        // Show register form
      } else if (!userData && findUser) {
        localStorage.setItem("userData", JSON.stringify(findUser));
        user = findUser;
        // Set data to local storage
      } else if (userData && !findUser) {
        isUpdating = false;
        user = false;
        // Show register form
      } else {
        let userFromLS = JSON.parse(userData);
        if (findUser.userToken != userFromLS.userToken) {
          // set ls new user data
          localStorage.setItem("userData", JSON.stringify(findUser));
          user = findUser;
        } else {
          user = userFromLS;
          // user exist
        }
        isUpdating = false;
      }
    });
  }

  // find user by token
  let findUserByToken = (token) => {
    let currentUser = {};
    users.forEach((item) => {
      if (item.userToken === token) {
        currentUser = item;
      }
    });
    if (Object.keys(currentUser).length === 0) {
      return false;
    } else {
      return currentUser;
    }
  };

  // user validation
  let validateUser = (name, token) => {
    let state = true;
    if (userName.length === 0) {
      alert("Enter your user name");
    } else {
      users.forEach((item) => {
        if (item.userName === name || item.userToken === token) {
          console.log("User exist in DB");
          state = false;
        }
      });
      return state;
    }
  };

  // create user
  let createUser = async () => {
    let data;
    if (validateUser(userName, userToken)) {
      data = {
        userName,
        userToken,
        userRole: "Passenger",
        createdAt: serverTimestamp(),
      };
      await addDoc(collection(db, "Users"), data);
      localStorage.setItem("userData", JSON.stringify(data));
      console.log("User created");
    } else {
      return false;
    }
  };

  // show temporary error message
  function showTepmErrorMessage(text) {
    if (!tepmErrorState) {
      tepmErrorState = true;
      tepmError = text;
      setTimeout(() => {
        tepmErrorState = false;
      }, 4000);

      setTimeout(() => {
        if (tempMessageWrap) {
          tempMessageWrap.style.opacity = 0;
        }
      }, 1100);
    }
  }

  // send message
  const sendMessage = async () => {
    updatingFormState = true;
    let data;
    if (messageText.length < 1) {
      showTepmErrorMessage(`Can't send empty message`);
      // Can't send empty message
      updatingFormState = false;
      return;
    }
    try {
      data = {
        body: messageText,
        authorID: user.userToken,
        userName: user.userName,
        userRole: user.userRole,
        createdAt: serverTimestamp(),
      };
      await addDoc(collection(db, "Messages"), data);
      updatingFormState = false;
    } catch (error) {
      console.error(error.message);
      return;
    }
    messageText = "";
  };

  // scroll to bottom
  function scrollToBottom() {
    setTimeout(() => {
      chatMessageWrapper.scrollTo({
        top: chatMessageWrapper.scrollHeight,
        left: 0,
        behavior: "smooth",
      });
    }, 50);
  }
</script>

<svelte:window bind:innerWidth />
{#if !userToken}
  <ConnectMetamask bind:userToken {checkIfUserExist} />
{/if}

<main class="main__chat__wrapper">
  {#if user}
    <ChatHead />
  {/if}
  {#if window.innerWidth > 991}
    <div class="wrapper">
      <img
        src="https://uploads-ssl.webflow.com/623494ba6746d1d287d735b3/6256b2a766f2134292eeaa81_Alien%20in%20the%20cabin%20live%20chat%201-min.jpg"
        alt="chat-bg"
        class="chat__bg"
      />
    </div>
  {/if}

  {#if isUpdating}
    <div class="preloader" />
  {/if}
  {#if user}
    <div class="bg-wrapper">
      <img
        src="https://uploads-ssl.webflow.com/623494ba6746d1d287d735b3/6253cec9466cfff07e2a0dc7_form-bg.svg"
        class="main__bg--chat"
        alt=""
      />
      <div class="container-chat">
        <div class="chat__wrapper">
          <div class="column">
            <Sidebar {user} />
          </div>
          <div class="column">
            <div class="chat" >
              <ChatTop />

              <div class="chat__main"  bind:this={chatMain}>
                <div>
                  <div
                    class="chat__messages-wrapper"
                    bind:this={chatMessageWrapper}
                    style="min-height:100%;  max-height: {chatHeight}px;"
                  >
                    <ul class="chat__messages">
                      {#each messages as message, index}
                        <MessageItem
                          {message}
                          {user}
                          {db}
                          {scrollToBottom}
                          {index}
                          {messages}
                        />
                      {/each}
                    </ul>
                  </div>
                </div>
                <div bind:this={chatForm}>
                  <SendMessageForm
                    {sendMessage}
                    bind:messageText
                    {updatingFormState}
                    {tepmErrorState}
                    {tempMessageWrap}
                    {tepmError}
                  />
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  {:else if !user}
    <CreateUserForm {createUser} bind:userName />
  {/if}
</main>

<style>
  .main__chat__wrapper {
    position: relative;
  }
  ul.chat__messages {
    padding: 0;
    margin-bottom: 0;
  }
  .wrapper {
    position: absolute;
    width: 100%;
    height: 100%;
    z-index: -1;
  }

  .chat__bg {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: -1;
  }
  .container-chat {
    max-width: 1378px;
    width: 100%;
    height: 878px;
    margin: 0 auto;
    overflow: hidden;
    position: relative;
    top: -9px;
    right: -6px;
    z-index: 1;
  }
  .chat {
    background: #1c0b32;
    height: 100%;
    max-height: 880px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }

  .preloader {
    position: fixed;
    top: 0;
    z-index: 99;
    width: 100%;
    height: 100%;
    background-color: #130427;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .chat__wrapper {
    display: grid;
    grid-template-columns: 272px 1fr;
    grid-column-gap: 0px;
    grid-row: inherit;
    height: 100%;
  }

  .chat__messages-wrapper {
    overflow: auto;
    padding-top: 24px;
    transition: all ease 0.5s;
    max-height: 770px;
  }

  .chat__main {
    padding: 0px 24px 20px 24px;
    height: 100%;
    overflow: hidden;
  }

  .bg-wrapper {
    margin: 21px 0 112px 0;
    width: 1557px;
    height: 1054px;
    display: flex;
    justify-content: center;
    align-items: center;
    margin: 20px auto 0 auto;
    position: relative;
  }
  .main__bg--chat {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: -1;
  }

  .chat__messages-wrapper {
    -ms-overflow-style: none; /* Internet Explorer 10+ */
    scrollbar-width: 0px; /* Firefox */
  }
  .chat__messages-wrapper::-webkit-scrollbar {
    display: none; /* Safari and Chrome */
  }
  .chat__messages-wrapper {
    scrollbar-color: #fff #fff;
    scrollbar-width: thin; /* Safari and Chrome */
  }

  .chat__messages-wrapper::-moz-scrollbar {
    display: none;
  }

  @media screen and (max-width: 1550px) {
    .bg-wrapper {
      width: 1200px;
      height: 811px;
    }
    .container-chat {
      width: 1060px;
      height: 677px;
      top: -7px;
      right: -4px;
    }
    .chat__messages-wrapper {
      height: 617px;
    }
    .chat__main {
      height: 100%;
    }
    .chat{
      max-height: 685px;
    }
  }
  @media screen and (max-width: 1230px) {
    .bg-wrapper {
      width: 900px;
      height: 608px;
    }
    .chat{
      max-height: 515px;
    }
    .wrapper {
      height: 100%;
    }
    .container-chat {
      width: 795px;
      height: 506px;
      top: -5px;
      right: -4px;
    }
    .chat__messages-wrapper {
       height: 447px; 
    }
    .chat__main {
      height: 100%;
    }
    .chat__wrapper {
      grid-template-columns: 220px 1fr;
    }
  }
  @media screen and (max-width: 991px) {
    .chat{
      max-height: none;
    }
    .chat__bg {
      display: none;
    }
    .chat__main {
      height: 600px;
      padding: 0px 20px 20px 20px;
    }
    .bg-wrapper {
      width: 100%;
      height: auto;
    }
    .chat__wrapper {
      grid-template-columns: 1fr;
    }
    .chat__messages-wrapper {
      height: 532px;
    }
    .container-chat {
      top: 0;
      right: 0;
      width: 100%;
      height: 100%;
    }
  }
</style>
