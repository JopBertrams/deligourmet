<script>
  import { onMount } from 'svelte';
  import Swal from 'sweetalert2';
  import 'sweetalert2/src/sweetalert2.scss';

  let theWheel;
  let wheelPower = 2;
  let wheelSpinning = false;
  let wheelHasSpun = false;
  let colors = [
    '#FF0000',
    '#FF7F00',
    '#FFFF00',
    '#00FF00',
    '#0000FF',
    '#4B0082',
    '#9400D3',
    '#FF0000',
    '#FF7F00',
    '#FFFF00',
    '#00FF00',
    '#0000FF',
    '#4B0082',
    '#9400D3',
    '#FF0000',
    '#FF7F00',
    '#FFFF00',
    '#00FF00',
    '#0000FF',
    '#4B0082',
    '#9400D3',
    '#FF0000',
    '#FF7F00',
    '#FFFF00',
    '#00FF00',
    '#0000FF',
    '#4B0082',
    '#9400D3',
    '#FF0000',
    '#FF7F00',
    '#FFFF00',
    '#00FF00',
    '#0000FF',
    '#4B0082',
    '#9400D3',
    '#FF0000',
    '#FF7F00',
    '#FFFF00',
    '#00FF00',
    '#0000FF',
    '#4B0082',
    '#9400D3',
    '#FF0000',
    '#FF7F00',
    '#FFFF00',
    '#00FF00',
    '#0000FF',
    '#4B0082',
    '#9400D3',
    '#FF0000',
    '#FF7F00',
    '#FFFF00',
    '#00FF00',
    '#0000FF',
    '#4B0082',
    '#9400D3',
    '#FF0000',
    '#FF7F00',
  ];
  let discoInterval = null;
  let allItems = [];
  let itemsOnWheel = [];
  let categories = [
    {
      id: 'toppers',
      text: 'Toppers',
    },
    {
      id: 'mediterrane',
      text: 'Mediterrane',
    },
    {
      id: 'wraps',
      text: 'Wraps',
    },
    {
      id: 'broodjes_gezond',
      text: 'Broodje gezond',
    },
    {
      id: 'clubsandwiches',
      text: 'Clubsandwiches',
    },
    {
      id: 'filet_americain',
      text: 'Filet Americain',
    },
    {
      id: 'koud_beleg',
      text: 'Koud Beleg',
    },
    {
      id: 'salades',
      text: 'Salades',
    },
    {
      id: 'fruit',
      text: 'Fruit',
    },
    {
      id: 'dranken',
      text: 'Dranken',
    },
    {
      id: 'alles',
      text: 'Alles!',
    },
  ];
  let selectedCategory;

  let isVegan = false;
  let isMeat = true;
  let isFish = true;

  onMount(async () => {
    // Get broodjes from API
    fetch('http://broodjes.data.jopbertrams.nl/api/broodjes.php')
      .then((response) => response.json())
      .then((data) => {
        allItems = data.data;
        createWheel();
      });

    // Get range slider and bubble and set bubble to correct position
    const range = document.getElementById('range');
    const bubble = document.getElementById('bubble');
    range.addEventListener('input', () => {
      setBubble(range, bubble);
    });
    setBubble(range, bubble);
  });

  function createWheel() {
    // Get items from selected category
    itemsOnWheel = [];
    if (selectedCategory == 'alles') {
      allItems.forEach((item) => {
        itemsOnWheel = [
          ...itemsOnWheel,
          {
            fillStyle: colors[allItems.indexOf(item) % colors.length], // Set color to one of the colors, repeat if there are more items than colors
            text: (allItems.indexOf(item) + 1).toString(),
            textFontSize: 9,
            product: item.Product,
            beschrijving: item.Beschrijving,
            prijs: item.Prijs,
            categorie: item.Categorie,
          },
        ];
      });
    } else {
      allItems
        .filter((item) => item.Categorie == selectedCategory)
        .forEach((item) => {
          itemsOnWheel = [
            ...itemsOnWheel,
            {
              fillStyle: colors[allItems.indexOf(item) % colors.length], // Set color to one of the colors, repeat if there are more items than colors
              text: (allItems.indexOf(item) + 1).toString(),
              product: item.Product,
              beschrijving: item.Beschrijving,
              prijs: item.Prijs,
              categorie: item.Categorie,
            },
          ];
        });
    }

    // Create the wheel
    theWheel = new Winwheel({
      outerRadius: 290, // Set outer radius
      innerRadius: 100, // Make wheel hollow
      textFontSize: 24, // Default font size for the segments
      textOrientation: 'horizontal',
      textAlignment: 'outer', // Align text to outside of wheel
      numSegments: itemsOnWheel.length, // Specify number of segments
      segments: itemsOnWheel, // Define segments including colour and text
      animation: {
        type: 'spinToStop',
        duration: 10, // Duration in seconds
        spins: 3, // Default number of complete spins
        callbackFinished: wheelFinished,
        callbackSound: playSound,
        soundTrigger: 'pin', // Specify pins are to trigger the sound, the other option is 'segment'
      },
      pins: {
        number: itemsOnWheel.length,
        fillStyle: 'silver',
        outerRadius: 4,
      },
    });
  }

  // Loads the tick audio sound in to an audio object
  let audio = new Audio('audio/tick.mp3');

  // This function is called when the sound is to be played.
  function playSound() {
    // Stop and rewind the sound if it already happens to be playing
    audio.pause();
    audio.currentTime = 0;

    // Play the sound.
    audio.play();
  }

  // This function is called when the animation has finished
  function wheelFinished(indicatedSegment) {
    // Stop blinking background
    intervalManager(false);

    // Show alert with product and description
    Swal.fire({
      title: indicatedSegment.product,
      text: indicatedSegment.beschrijving,
      imageUrl: './videos/party-popper.gif',
      imageWidth: 100,
      imageHeight: 100,
      confirmButtonText: 'Die pak ik!',
      showDenyButton: true,
      denyButtonText:
        '<p style="font-size: 10px;">Boehoe ik gebruik een rad om te kiezen, maar ben het vervolgens niet eens met de keuze!</p>',
    }).then((result) => {
      if (result.isDenied) {
        Swal.fire({
          title: 'Nog een keer dan maar...',
          text: 'De broodjesgoden zijn het er niet mee eens, maar hebben het rad gereset.',
          imageUrl: './videos/rewind-time.gif',
          imageWidth: 300,
          confirmButtonText: 'Dankjewel',
        });
        resetWheel();
      }
    });

    wheelSpinning = false;
    wheelHasSpun = true;
  }

  // This function is called to start the wheel spinning
  function startSpin() {
    // Ensure that spinning can't be clicked again while already running.
    if (wheelSpinning == false) {
      // Based on the power level selected adjust the number of spins for the wheel, the more times is has
      // to rotate with the duration of the animation the quicker the wheel spins.
      if (wheelPower == 1) {
        theWheel.animation.spins = 3;
      } else if (wheelPower == 2) {
        theWheel.animation.spins = 6;
      } else if (wheelPower == 3) {
        theWheel.animation.spins = 10;
      }

      // Begin the spin animation by calling startAnimation on the wheel object
      theWheel.startAnimation();

      // Start blinking background
      intervalManager(true);
      // Set to true so that power can't be changed and spin button re-enabled during
      // the current animation. The user will have to reset before spinning again
      wheelSpinning = true;
    }
  }

  function resetWheel() {
    theWheel.stopAnimation(false); // Stop the animation, false as param so does not call callback function.
    theWheel.rotationAngle = 0; // Re-set the wheel angle to 0 degrees.
    theWheel.draw(); // Call draw to render changes to the wheel.
    wheelHasSpun = false; // Reset to false so spin can be clicked again.
  }

  function backgroundDisco() {
    document.body.style.setProperty(
      '--bgColor',
      colors[Math.floor(Math.random() * 5)]
    );
  }

  function intervalManager(start) {
    if (start) {
      discoInterval = setInterval(backgroundDisco, 300);
    } else {
      clearInterval(discoInterval);
      discoInterval = null;
    }
  }

  function setBubble(range, bubble) {
    const val = range.value;
    const min = range.min;
    const max = range.max;
    const newVal = Number(((val - min) * 100) / (max - min));
    if (val == 1) {
      bubble.innerHTML = 'Zacht';
    } else if (val == 2) {
      bubble.innerHTML = 'Normaal';
    } else if (val == 3) {
      bubble.innerHTML = 'Hard!';
    }

    // Move bubble to correct position
    bubble.style.left = `calc(${newVal}% + (${8 - newVal * 0.15}px))`;
  }

  function handleCheckboxChange(event) {
    const checkboxId = event.target.id;

    if (checkboxId === 'vegan') {
      isVegan = event.target.checked;
      if (isVegan) {
        isMeat = false;
        isFish = false;
      }
    } else if (checkboxId === 'meat') {
      isMeat = event.target.checked;
      if (isMeat) {
        isVegan = false;
      }
    } else if (checkboxId === 'fish') {
      isFish = event.target.checked;
      if (isFish) {
        isVegan = false;
      }
    }
  }
</script>

<div id="wheel">
  <h1>Het Deligourmet Rad Van Fortuin</h1>
  <div id="wheelAndSettings">
    <div id="broodjesSettings">
      <h3>Wat wil je?</h3>
      <div id="checkboxes">
        <div id="checkbox">
          <input
            type="checkbox"
            id="meat"
            name="meat"
            value="meat"
            checked={isMeat}
            on:change={handleCheckboxChange}
          />
          <label for="meat">Vlees</label>
        </div>
        <div id="checkbox">
          <input
            type="checkbox"
            id="fish"
            name="fish"
            value="fish"
            checked={isFish}
            on:change={handleCheckboxChange}
          />
          <label for="fish">Vis</label>
        </div>
        <div id="checkbox">
          <input
            type="checkbox"
            id="vegan"
            name="vegan"
            value="vegan"
            checked={isVegan}
            on:change={handleCheckboxChange}
          />
          <label for="vegan">Vegan</label>
        </div>
      </div>
      <select
        id="categorySelector"
        bind:value={selectedCategory}
        on:change={() => createWheel()}
      >
        {#each categories as categorie}
          <option value={categorie.id}>{categorie.text}</option>
        {/each}
      </select>
    </div>
    <div id="backgroundImage">
      <canvas id="canvas" width="600" height="600">
        Your browser does not support HTML5 Canvas to display the wheel.
      </canvas>
    </div>
    <div id="wheelSettings">
      <h3>Hoe hard draaien?</h3>
      <div class="power_indicator">
        <input
          id="range"
          type="range"
          min="1"
          max="3"
          value="2"
          on:change={() => (wheelPower = event.target.value)}
        />
        <output class="bubble" id="bubble" />
      </div>
      {#if wheelHasSpun}
        <button id="spin_button" on:click={resetWheel}>RESET</button>
      {:else if wheelSpinning}
        <button id="spin_button" disabled>IS SPINNING...</button>
      {:else}
        <button id="spin_button" on:click={startSpin}>SPIN</button>
      {/if}
    </div>
  </div>
</div>

<style>
  #wheel {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    height: 100vh;
  }

  h1 {
    font-size: 50px;
    font-weight: bold;
    color: var(--bgColor);
    filter: invert(1);
    margin-bottom: 0;
  }

  #backgroundImage {
    display: flex;
    justify-content: center;
    align-items: flex-end;
    background-image: url('../assets/wheelBackground.png');
    background-position: center;
    background-repeat: none;
    height: 772px;
  }

  #wheelAndSettings {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
  }

  #wheelSettings,
  #broodjesSettings {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-items: center;
    margin: 50px;
    height: 30%;
  }

  #broodjesSettings h3,
  #wheelSettings h3 {
    font-size: 30px;
    font-weight: bold;
    color: var(--bgColor);
    filter: invert(1);
    margin-bottom: 10px;
  }

  #broodjesSettings #checkboxes {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 100%;
  }

  #broodjesSettings #checkbox {
    display: flex;
    justify-content: flex-start;
    align-items: center;
    margin: 10px;
    width: 40%;
  }

  #broodjesSettings #checkbox input {
    margin-right: 10px;
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background-color: #f26522;
    color: white;
    font-size: 20px;
    font-weight: bold;
    text-align: center;
    border: none;
    outline: none;
    cursor: pointer;
  }

  #broodjesSettings #checkbox label {
    font-size: 20px;
    font-weight: bold;
    color: white;
  }

  #broodjesSettings select {
    width: 250px;
    height: 50px;
    padding: 10px;
    border-radius: 25px;
    background-color: #f26522;
    color: white;
    font-size: 20px;
    font-weight: bold;
    text-align: center;
    border: none;
    outline: none;
    cursor: pointer;
    -webkit-animation: blinkingButton 3s infinite;
    -moz-animation: blinkingButton 3s infinite;
    -o-animation: blinkingButton 3s infinite;
    animation: blinkingButton 3s infinite;
  }

  #broodjesSettings select option {
    background-color: #f26522;
    color: white;
    font-size: 20px;
    font-weight: bold;
    border: none;
    outline: none;
    cursor: pointer;
    -webkit-animation: blinkingButton 3s infinite;
    -moz-animation: blinkingButton 3s infinite;
    -o-animation: blinkingButton 3s infinite;
    animation: blinkingButton 3s infinite;
  }

  .power_indicator {
    position: relative;
    margin: 0 auto 3rem;
  }

  #range {
    width: 100%;
  }

  #bubble {
    background: red;
    color: white;
    padding: 4px 12px;
    position: absolute;
    border-radius: 4px;
    left: 50%;
    transform: translateX(-50%) translateY(90%);
  }

  #bubble::after {
    content: '';
    position: absolute;
    width: 10px;
    height: 10px;
    background: red;
    top: -5px;
    left: 50%;
    transform: translateX(-50%) rotate(45deg);
  }

  #spin_button {
    width: 250px;
    height: 50px;
    border-radius: 25px;
    background-color: #f26522;
    color: white;
    font-size: 20px;
    font-weight: bold;
    border: none;
    outline: none;
    cursor: pointer;
    -webkit-animation: blinkingButton 3s infinite;
    -moz-animation: blinkingButton 3s infinite;
    -o-animation: blinkingButton 3s infinite;
    animation: blinkingButton 3s infinite;
  }

  #spin_button:disabled {
    background-color: #a6a6a6;
    border: 3px solid #000000;
    -webkit-animation: none;
    -moz-animation: none;
    -o-animation: none;
    animation: none;
    cursor: not-allowed;
  }

  @keyframes blinkingButton {
    0%,
    49% {
      background-color: #f26522;
      border: 3px solid #3d00e5;
    }
    50%,
    100% {
      background-color: #3d00e5;
      border: 3px solid #f26522;
    }
  }

  @-webkit-keyframes blinkingButton {
    0%,
    49% {
      background-color: #f26522;
      border: 3px solid #3d00e5;
    }
    50%,
    100% {
      background-color: #3d00e5;
      border: 3px solid #f26522;
    }
  }
</style>
