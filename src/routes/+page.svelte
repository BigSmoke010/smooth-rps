<script>
  import random from "random";
  import Paper from "./Paper.svg?component";
  import Rock from "./Rock.svg?component";
  import Scissors from "./Scissors.svg?component";
  import { fade, scale } from "svelte/transition";
  import { quintOut } from "svelte/easing";
  let selected = "";
  let seconds = 5;
  let listofchosable = ["rock", "paper", "scissors"];
  let listofmids = [0, 60, 120];
  let botselected = listofchosable[random.integer(0, 2)];
  let setfive = false;
  let setfour = false;
  let setthree = false;
  let settwo = false;
  let setone = false;
  let setzero = false;
  let listofsets = [setzero, setone, settwo, setthree, setfour, setfive];
  let gameover = false;
  let winnermsg = "none";
  let ballX = 0;
  let randomX = 0;
  let chosenint = 0;
  let noclick = true;
  function moveBall(event) {
    const container = event.currentTarget;
    const containerRect = container.getBoundingClientRect();
    const containerLeft = containerRect.left;
    const containerWidth = containerRect.width;

    const clickX = event.clientX - containerLeft;

    // Check if the click falls within the desired ranges
    if (clickX >= 0 && clickX <= 50) {
      ballX = 0;
    } else if (clickX >= 60 && clickX <= 110) {
      ballX = 60;
    } else {
      ballX = 120;
    }
  }

  function handleClick(event) {
    noclick = false;
    moveBall(event);
  }
  function startcountdown() {
    gameover = false;
    seconds = 5;
    winnermsg = "";
    const countdown = setInterval(() => {
      if (seconds > 0) {
        seconds--;
        chosenint = random.integer(0, 2);
        botselected = listofchosable[chosenint];
        randomX = listofmids[chosenint];
        listofsets[seconds - 1] = true;
        if (seconds - 1 < 4) {
          listofsets[seconds] = false;
        }
      } else {
        clearInterval(countdown);
        gameover = true;
        // Compare the choices and determine the winner
        if (botselected === selected) {
          winnermsg = "It's a tie!";
        } else if (
          (botselected === "rock" && selected === "scissors") ||
          (botselected === "paper" && selected === "rock") ||
          (botselected === "scissors" && selected === "paper")
        ) {
          winnermsg = "Bot wins!";
        } else {
          winnermsg = "Player wins!";
        }
      }
    }, 1000);
  }
  startcountdown();
</script>

<div
  class:botwon={winnermsg === "Bot wins!"}
  class:playerwon={winnermsg === "Player wins!"}
  class="main"
>
  <div class="container" style="border:4px red solid;">
    <div class="smoothball" style="left: {randomX}px;" />
    <div class="item">
      <Rock />
    </div>
    <div class="item">
      <Paper />
    </div>
    <div class="item">
      <Scissors />
    </div>
  </div>
  <div
    class="container"
    style="border:4px rgba(0, 255, 0, 0.5) solid;"
    on:click={handleClick}
  >
    {#if noclick === false}
      <div
        class="smoothball"
        style="left: {ballX}px; background-color: rgb(24,100,2)"
        transition:fade
      />
    {/if}
    <div class="item" on:click={() => (selected = "rock")}>
      <Rock />
    </div>
    <div class="item" on:click={() => (selected = "paper")}>
      <Paper />
    </div>
    <div class="item" on:click={() => (selected = "scissors")}>
      <Scissors />
    </div>
  </div>

  {#if seconds === 4}<p class="countdownnum">4</p>{/if}
  {#if seconds === 3}<p class="countdownnum">3</p>{/if}
  {#if seconds === 2}<p class="countdownnum">2</p>{/if}
  {#if seconds === 1}<p class="countdownnum">1</p>{/if}
  {#if seconds === 0}<p class="countdownnum">0</p>{/if}
  {#if gameover === true}
    <h1 transition:fade={{ duration: 1000 }} class="winner">{winnermsg}</h1>
    <button transition:fade class="retrybtn" on:click={startcountdown}
      >retry</button
    >
  {/if}
</div>

<style>
  @import url("https://fonts.googleapis.com/css2?family=Nunito&display=swap");
  * {
    font-family: Nunito;
    margin: 0;
    padding: 0;
  }
  p {
    color: white;
  }
  .main {
    transition: all 1.5s;
    display: flex;
    flex-direction: column;
    justify-content: center;
    background-color: black;
    align-items: center;
    height: 100vh;
    top: 0;
    position: fixed;
    right: 0;
    bottom: 0;
    left: 0;
  }
  .playerwon {
    box-shadow: inset 10px 20px 1000px rgba(0, 255, 0, 0.5);
  }
  .botwon {
    box-shadow: inset 10px 20px 1000px rgba(182, 11, 11, 0.5);
  }
  .container {
    margin-bottom: 30px;
    display: flex;
    position: relative;
    align-items: center;
    gap: 10px;
    justify-content: center;
    width: 170px;
    background-color: transparent;
    height: 55px;
    border-radius: 60px;
  }
  .smoothball {
    transition: all 1s ease;
    position: absolute;
    width: 55px;
    height: 55px;
    border-radius: 50%;
    background-color: red;
    left: 0;
    top: 0;
    filter: drop-shadow(0px 0px 10px);
  }
  .retrybtn {
    position: absolute;
    transition: background-color 1s;
    border: white 2px solid;
    border-radius: 50px;
    color: white;
    font-size: 25px;
    background-color: transparent;
    width: 100px;
    height: 55px;
    bottom: 10%;
  }
  .retrybtn:hover {
    cursor: pointer;
    background-color: white;
    color: black;
  }
  .countdownnum {
    font-size: 42px;
    position: absolute;
    top: 10%;
    animation: showhide 1s forwards;
  }
  @keyframes showhide {
    0% {
      top: 5%;
      scale: 50%;
    }
    50% {
      top: 13%;
      scale: 200%;
      transform: rotate(20deg);
    }
    100% {
      top: 20%;
      scale: 10%;
      transform: rotate(30deg);
      opacity: 0.1;
    }
  }
  .winner {
    font-size: 38px;
    color: white;
    margin-bottom: 20px;
    position: absolute;
    bottom: 30%;
  }
  .item {
    border-radius: 50%;
    filter: drop-shadow(0px 0px 12px);
  }
  .item:hover {
    cursor: pointer;
  }
</style>
