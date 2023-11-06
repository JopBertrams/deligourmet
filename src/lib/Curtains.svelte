<script>
  // @ts-nocheck
  import { fade } from 'svelte/transition';

  let buttonVisible = true;
  let pointingManFinished = false;
  let leonardoDicaprioFinished = false;

  function startTheShow() {
    buttonVisible = false;
    let audio = new Audio('audio/intro.mp3');
    let curtains = document.getElementById('curtains');
    let man_pointing = document.getElementById('man_pointing');
    let leonardo_dicaprio_pointing = document.getElementById(
      'leonardo_dicaprio_pointing'
    );
    //audio.play();
    curtains.play();
    setTimeout(() => {
      man_pointing.play();
      leonardo_dicaprio_pointing.play();
      setTimeout(() => {
        pointingManFinished = true;
      }, 11500);
      setTimeout(() => {
        leonardoDicaprioFinished = true;
      }, 2000);
    }, 4000);
    curtains.onended = function () {
      document.getElementById('curtains').style.zIndex = -1;
    };
  }
</script>

<div>
  <video
    src="./videos/curtains.webm"
    id="curtains"
    muted
    playsinline
    disablePictureInPicture
    oncontextmenu="return false;"
    style="z-index: 1;"
  />
  <video
    src="./videos/man_pointing.webm"
    id="man_pointing"
    class:out={pointingManFinished}
    muted
    playsinline
    disablePictureInPicture
    oncontextmenu="return false;"
    style="z-index: 0;"
  />
  <video
    src="./videos/leonardo_dicaprio_pointing.webm"
    id="leonardo_dicaprio_pointing"
    class:out={leonardoDicaprioFinished}
    muted
    playsinline
    disablePictureInPicture
    oncontextmenu="return false;"
    style="z-index: 0;"
  />
  {#if buttonVisible}
    <button style="z-index: 2;" out:fade on:click={startTheShow}>
      Klik hier!
    </button>
  {/if}
</div>

<style>
  #curtains {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    object-fit: fill;
  }

  #man_pointing {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 50%;
    object-fit: fill;
    transform: translate(0, 0);
    transition: transform 1s ease-in-out;
  }

  #man_pointing.out {
    transform: translate(0, 100%);
  }

  #leonardo_dicaprio_pointing {
    position: fixed;
    bottom: 0;
    right: 0;
    width: 50%;
    object-fit: fill;
    transform: translate(0, 0);
    transition: transform 1s ease-in-out;
  }

  #leonardo_dicaprio_pointing.out {
    transform: translate(0, 100%);
  }

  button {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    border: 1px transparent;
    -webkit-border-radius: 40px;
    border-radius: 40px;
    color: #fff;
    cursor: pointer;
    font-size: 24px;
    padding: 20px 30px;
    text-align: center;
    text-decoration: none;
    -webkit-animation: glowing 1300ms infinite;
    -moz-animation: glowing 1300ms infinite;
    -o-animation: glowing 1300ms infinite;
    animation: glowing 1300ms infinite;
  }

  @-webkit-keyframes glowing {
    0% {
      background-color: #0091b2;
      -webkit-box-shadow: 0 0 3px #0091b2;
    }
    50% {
      background-color: #21c7ed;
      -webkit-box-shadow: 0 0 15px #21c7ed;
    }
    100% {
      background-color: #0091b2;
      -webkit-box-shadow: 0 0 3px #0091b2;
    }
  }
  @keyframes glowing {
    0% {
      background-color: #0091b2;
      box-shadow: 0 0 3px #0091b2;
    }
    50% {
      background-color: #21c7ed;
      box-shadow: 0 0 15px #21c7ed;
    }
    100% {
      background-color: #0091b2;
      box-shadow: 0 0 3px #0091b2;
    }
  }
</style>
