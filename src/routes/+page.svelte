<script>
  import random from "random";
  import Paper from "./Paper.svg?component";
  import Rock from "./Rock.svg?component";
  import Scissors from "./Scissors.svg?component";
  import { fade, fly } from "svelte/transition";
  let selected = "";
  let seconds = 5;
  let listofchosable = ["rock", "paper", "scissors"];
  let listofmids = [0, 60, 120];
  let botselected = listofchosable[random.integer(0, 2)];
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
    if (
      (clickX >= -10 && clickX <= 50) ||
      (clickX >= 60 && clickX <= 110) ||
      (clickX >= 120 && clickX <= 170)
    ) {
      const rangeIndex = Math.floor((clickX - 10) / 60); // Calculate the index of the clicked range
      const middlePosition = 60 * rangeIndex + 35; // Calculate the middle position of the clicked range

      ballX = middlePosition - 27.5; // Half the width of the ball

      // Restrict the ball position within the container
      ballX = Math.max(0, Math.min(ballX, containerWidth - 55)) - 7.5;
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
  <div class="container">
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
  <div class="container" on:click={handleClick}>
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

  <p>you have {seconds} seconds left!!!!!</p>
  {#if gameover === true}
    <p>{winnermsg} !!!!!</p>
    <button class="retrybtn" on:click={startcountdown}>retry</button>
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
    background-color: #7f13ab;
    height: 55px;
    border: #dd0453 solid 4px;
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
    transition: background-color 1s;
    border: white 2px solid;
    border-radius: 50px;
    color: white;
    background-color: transparent;
    width: 100px;
    height: 55px;
  }
  .retrybtn:hover {
    cursor: pointer;
    background-color: white;
    color: black;
  }
  .item {
    border-radius: 50%;
    filter: drop-shadow(0px 0px 12px);
  }
  .item:hover {
    cursor: pointer;
  }
</style>
