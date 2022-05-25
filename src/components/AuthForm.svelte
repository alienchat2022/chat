<script>
  export let userName = "";
  export let createUser;

</script>

<div class="pop-up-wrapper">
  <div class="createUser">
    <div class="createUser__wrapper">
      <p>Welcome on board!</p>
      <form class="createUser-form" on:submit|preventDefault={createUser}>
        <label for="name">Enter your name</label>
        <input type="text" name="name" bind:value={userName} />
        <div class="buttons-form">
          <button type="submit">Submit</button>
          <div class="cancel">Cancel</div>
        </div>
      </form>
    </div>
  </div>
</div>

<style>
  .pop-up-wrapper {
    width: 100%;
    height: 100vh;
    background: rgba(25, 2, 45, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .createUser {
    width: 425px;
    height: 328px;
    background: #ac78f1;
    border-radius: 20px;
  }
  .createUser__wrapper {
    padding: 40px;
  }
  .createUser p {
    font-weight: 900;
    font-size: 32px;
    line-height: 39px;
    text-transform: uppercase;
    color: #130427;
    margin-bottom: 32px;
  }
  .buttons-form {
    display: flex;
    margin-top: 32px;
  }
  .buttons-form button,
  .buttons-form .cancel {
    width: 160px;
    height: 52px;
    border-radius: 10px;
    font-weight: 700;
    font-size: 14px;
    line-height: 17px;
    text-transform: uppercase;
    color: #130427;
  }
  .buttons-form button {
    background: #c092ff;
    box-shadow: 4px 4px 19px rgba(0, 0, 0, 0.05);
    margin-right: 25px;
  }
  .buttons-form .cancel {
    border: 1px solid #c092ff;
    filter: drop-shadow(4px 4px 19px rgba(0, 0, 0, 0.05));
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .createUser-form input {
    height: 32px;
    background: none;
    border: none;
    border-bottom: 1px solid #130427;
    border-radius: 0;
    width: 100%;
    font-weight: 400;
    font-size: 16px;
    line-height: 20px;
    color: #130427;
    padding: 13px;
  }
  .createUser-form label {
    font-weight: 700;
    font-size: 12px;
    line-height: 15px;
    text-transform: uppercase;
    color: #130427;
    margin-bottom: 6px;
  }
  .createUser-form input:focus {
    background-color: none;
  }
  @media screen and (max-width: 991px) {
    .createUser {
    width: 90%;
    max-width: 400px;
    height: auto;
  }
  .createUser__wrapper {
    padding: 20px;
  }
  }
</style>
