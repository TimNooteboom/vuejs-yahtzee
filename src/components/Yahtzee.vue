<template>
  <div class="container">
    <!-- currentValue {{ currentValue }} <br />
    values: {{ values }} <br /><br /><br /> -->
    <h1>Yahtzee</h1>
    <div id="board">
      <!-- Left Column -->
      <div class="column">
        <div
          class="section"
          v-for="cat in leftCategories"
          :key="`die-${cat.index}`"
        >
          <img draggable="false" :src="cat.img" />
          <input
            @click="input(cat.index)"
            :value="displayValues[cat.index]"
            :disabled="!diceFace.length || values[cat.index] !== ''"
            readonly
          />
        </div>
        <div class="progress-container">
          <div class="circular-progress" :style="progressStyle">
            <span class="progress-value" :class="{ bonus: gotUpperBonus }">
              {{ upperCatSum }}/{{ UPPER_SECTION_TARGET }}
            </span>
          </div>
        </div>
      </div>
      <!-- Right Column -->
      <div class="column">
        <div
          class="section"
          v-for="cat in rightCategories"
          :key="`die-${cat.index}`"
        >
          <img draggable="false" :src="cat.img" />
          <input
            @click="input(cat.index)"
            :value="displayValues[cat.index]"
            :disabled="!diceFace.length || values[cat.index] !== ''"
            readonly
          />
        </div>
      </div>
    </div>
    <div class="dice-wrapper">
      <Dice
        @selected="selectedDice"
        :dice="diceFace"
        :disabled="disabled"
        :reset="reset"
        :rolling="rolling"
      />
    </div>
    <p v-if="gameOver" class="total">{{ total }}</p>
    <div class="buttonWrapper">
      <button :class="{ disabled: disabled }" @click="roll">
        <template v-if="gameOver">Play again</template>
        <template v-else>
          <span class="roll-label">Roll</span>
          <span class="roll-boxes">
            <span
              v-for="n in MAX_ROLLS"
              :key="n"
              class="roll-box"
              :class="{ used: currentRoll >= n }"
              >{{ n }}</span
            >
          </span>
        </template>
      </button>
      <button
        :class="{ disabled: currentRoll == 0 || !viewed }"
        @click="submit"
      >
        Play
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, computed, watch } from "vue";
import Dice from "./Dice.vue";
import oneImg from "../assets/dice/one.svg";
import twoImg from "../assets/dice/two.svg";
import threeImg from "../assets/dice/three.svg";
import fourImg from "../assets/dice/four.svg";
import fiveImg from "../assets/dice/five.svg";
import sixImg from "../assets/dice/six.svg";
import threeOfAKindImg from "../assets/dice/three-of-a-kind.svg";
import fourOfAKindImg from "../assets/dice/four-of-a-kind.svg";
import fullHouseImg from "../assets/dice/full-house.svg";
import smStraightImg from "../assets/dice/small-straight.svg";
import lgStraightImg from "../assets/dice/large-straight.svg";
import fiveOfAKindImg from "../assets/dice/yahtzee.svg";
import chanceImg from "../assets/dice/chance.svg";

/******************/
// setting the same deg value on a conic-gradient creates a sharp, hard color line instead of a smooth fade. The first color stops right at n degrees, and the second color starts instantly at n degrees.
const progressStyle = computed(() => ({
  // expose only the fill angle; the gradient + transition live in CSS so it can animate
  "--deg": (upperCatSum.value * 360) / UPPER_SECTION_TARGET,
}));
/******************/

// score helpers
const sum = (dice) => dice.reduce((a, b) => a + b, 0);
const sumFace = (dice, face) =>
  dice.filter((d) => d === face).reduce((a, b) => a + b, 0);

const categories = [
  // left section
  { img: oneImg, score: (d) => sumFace(d, 1) },
  { img: twoImg, score: (d) => sumFace(d, 2) },
  { img: threeImg, score: (d) => sumFace(d, 3) },
  { img: fourImg, score: (d) => sumFace(d, 4) },
  { img: fiveImg, score: (d) => sumFace(d, 5) },
  { img: sixImg, score: (d) => sumFace(d, 6) },
  // right section
  { img: threeOfAKindImg, score: (d) => hasMultiples(d, 3) },
  { img: fourOfAKindImg, score: (d) => hasMultiples(d, 4) },
  { img: fullHouseImg, score: (d) => hasFullHouse(d) },
  { img: smStraightImg, score: (d) => hasStraight(d, 4) },
  { img: lgStraightImg, score: (d) => hasStraight(d, 5) },
  { img: fiveOfAKindImg, score: (d) => hasMultiples(d, 5) },
  { img: chanceImg, score: (d) => sum(d) },
];

// add the index to each category object then split into upper and lower
const indexedCategories = categories.map((c, index) => ({ ...c, index }));
const leftCategories = indexedCategories.slice(0, 6);
const rightCategories = indexedCategories.slice(6);
const upperCatSum = computed(() =>
  sum(values.value.slice(0, 6).filter((i) => i !== "")),
);

const diceFace = ref([]);
const selectedCopy = ref([]);
const currentRoll = ref(0);
const MAX_ROLLS = 3;
const DICE_SIDES = 6;
const UPPER_SECTION_TARGET = 63;
const UPPER_SECTION_BONUS = 35;
const FULL_HOUSE_SCORE = 25;
const SM_STRAIGHT_SCORE = 30;
const LG_STRAIGHT_SCORE = 40;
const YAHTZEE_SCORE = 50;
const CATEGORIES = categories.length;
const disabled = ref(false);
const viewed = ref(false);
const reset = ref(false);
const rolling = ref(false);
const values = ref(Array(CATEGORIES).fill(""));
const currentValue = ref(Array(CATEGORIES).fill(""));
const total = ref("");
const gameOver = ref(false);

// when we are out of rolls, send 'disabled' to the dice component
watch(currentRoll, (newValue) => {
  if (newValue === MAX_ROLLS) {
    disabled.value = true;
  }
});

// determine 3x, 4x or 5x
function hasMultiples(arr, multiple) {
  const result = arr.some(
    (num) => arr.filter((item) => item === num).length >= multiple,
  );
  if (result) {
    return multiple === 5 ? YAHTZEE_SCORE : sum(arr);
  } else {
    return 0;
  }
}

// determine if we have a small or large straight: 4|5 for size
function hasStraight(arr, size) {
  const uniqueArr = arr
    .filter((item, index) => arr.indexOf(item) == index)
    .sort();

  // we can also filter out duplicates by using a Set
  // const uniqueArr = [...new Set(arr)].sort((a, b) => a - b)

  // console.log("sorted and no dups: ", uniqueArr);
  let count = 0;
  const targetLinks = size - 1;
  for (let i = 0; i < uniqueArr.length - 1; i++) {
    if (uniqueArr[i] + 1 == uniqueArr[i + 1]) {
      count++;
      if (targetLinks == count) {
        return size === 4 ? SM_STRAIGHT_SCORE : LG_STRAIGHT_SCORE;
      }
    } else {
      count = 0;
    }
  }
  return 0;
}

function hasFullHouse(arr) {
  //sort the array but leave the original untouched
  const sortedArray = [...arr].sort();
  // we have a 3 of a kind....now see if we have 2 of a kind
  if (hasMultiples(arr, 3)) {
    const lastIndex = sortedArray.lastIndexOf(sortedArray[0]);
    // the array is sorted. Find the first number, if the last index is 1 then we have two of them
    if (lastIndex === 0) return 0;
    if (lastIndex === 1) return FULL_HOUSE_SCORE;
    // if the 3 of a kind is at the beginning, check the last two to make sure they match
    const newArr = sortedArray.slice(lastIndex + 1);
    if (hasMultiples(newArr, 2)) return FULL_HOUSE_SCORE;
    return 0;
  } else {
    return 0;
  }

  /* determine if we have a full house - ALT solution

    function hasFullHouse(arr) {
      const [a, b, c, d, e] = [...arr].sort();

      // Pattern 1 (AAABB): First three match AND last two match AND they aren't all identical
      const pattern1 = (a === c && d === e && a !== e);

      // Pattern 2 (AABBB): First two match AND last three match AND they aren't all identical
      const pattern2 = (a === b && c === e && a !== e);

      return pattern1 || pattern2;
    }

    console.log(hasFullHouse(array)); // true
    console.log(array); // Still untouched!
*/
}

const resetCurrentValue = () =>
  (currentValue.value = Array(CATEGORIES).fill(""));

const input = (index) => {
  // a category that has already been scored can't be previewed again
  if (values.value[index] !== "") return;

  viewed.value = true;
  // preview only: reset the transient preview, then set the clicked category
  resetCurrentValue();
  currentValue.value[index] = categories[index].score(diceFace.value);
};

// each cell shows the committed score if present, otherwise the live preview
const displayValues = computed(() =>
  values.value.map((v, i) => (v !== "" ? v : currentValue.value[i])),
);

const gotUpperBonus = computed(() => true); // upperCatSum.value >= UPPER_SECTION_TARGET

const submit = () => {
  // commit the previewed score into the permanent values
  currentValue.value.forEach((val, i) => {
    if (val !== "") values.value[i] = val;
  });
  // clear the preview so the next turn starts fresh
  resetCurrentValue();

  // the game is over once every category has been scored
  if (!values.value.some((el) => el === "")) {
    let totalScore = values.value.reduce((a, b) => a + b, 0);
    if (gotUpperBonus.value) {
      totalScore += UPPER_SECTION_BONUS;
    }
    total.value = `The game is over! Your score is ${totalScore}.`;
    gameOver.value = true;
  }

  currentRoll.value = 0;
  viewed.value = false;
  disabled.value = false;
  selectedCopy.value = [];
  diceFace.value = [];
  reset.value = true;
};

const rollDice = () => {
  [...Array(5)].map((el, index) => {
    if (!selectedCopy.value.includes(index)) {
      diceFace.value[index] = Math.ceil(Math.random() * DICE_SIDES);
    }
  });
};

const roll = () => {
  reset.value = false;
  viewed.value = false;
  resetCurrentValue();
  total.value = "";

  if (gameOver.value) {
    values.value = Array(CATEGORIES).fill("");
  }
  // don't start a new roll while one is already animating
  if (rolling.value) return;

  rolling.value = true;
  // shake the dice for a second, then reveal the new values
  setTimeout(() => {
    rollDice();
    rolling.value = false;
  }, 1000);
  currentRoll.value++;
};

// we don't want to animate the selected dice
const selectedDice = (sel) => {
  selectedCopy.value = [...sel];
};
</script>

<style>
/* registered so the browser can interpolate it — this is what makes the ring animate */
@property --deg {
  syntax: "<number>";
  inherits: false;
  initial-value: 0;
}
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}
h1 {
  font-family: Georgia, "Times New Roman", serif;
  font-size: 56px;
  font-weight: 800;
  text-align: center;
  letter-spacing: 2px;
  margin-bottom: 0.5em;

  /* real wood photo (public domain, Wikimedia Commons) clipped into the letters.
     the #7a4a24 fallback shows through if the image ever fails to load, so the
     title can't go fully invisible */
  background: #7a4a24 url("../assets/wood.svg") center / cover;
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;

  /* depth that follows the letter shapes (drop-shadow, not text-shadow) */
  filter: drop-shadow(0 2px 1px rgba(0, 0, 0, 0.45));
}

.container {
  width: 400px;
  font-family: "Poppins", sans-serif;
  padding: 20px;
  margin: 20px auto;
  background-color: #fff;
  border: 1px solid;
  box-shadow: 0 5px 35px rgb(50, 50, 50, 0.15);
  border-radius: 8px;
  .column {
    flex: max-content;
  }
}
.disabled {
  filter: opacity(0.5);
}
#board {
  border-top: 1px solid #eee;
  padding-top: 1em;
  margin-bottom: 20px;
  display: flex;
  .section {
    display: flex;
    margin-bottom: 10px;
    img {
      height: 50px;
      margin-right: 10px;
    }
    input {
      margin-top: 2px;
      height: 45px;
      width: 45px;
      font-size: 20px;
      text-align: center;
    }
    input:not(:disabled) {
      cursor: pointer;
    }
  }
  .progress-container {
    .circular-progress {
      display: flex;
      align-items: center;
      justify-content: center;
      position: relative;
      height: 50px;
      width: 50px;
      border-radius: 50%;
      background: conic-gradient(
        #2a3ae8 calc(var(--deg) * 1deg),
        #ededed calc(var(--deg) * 1deg)
      );
      transition: --deg 0.6s ease;

      &::before {
        content: "";
        position: absolute;
        height: 40px;
        width: 40px;
        border-radius: 50%;
        background-color: #fff;
      }
    }

    .progress-value {
      position: relative;
      font-size: 12px;
      font-weight: 600;
      color: #2a3ae8;
    }
  }
}
.dice-wrapper {
  border: 2px solid;
  height: 65px;
  padding: 5px;
  margin-bottom: 20px;
  img {
    height: 50px;
    margin-right: 10px;
    &.select {
      filter: opacity(0.5);
    }
  }
}

p {
  font-size: 16px;
  margin: 30px 0;
  font-weight: 500;
}
.buttonWrapper {
  display: flex;
  justify-content: space-around;
}

button {
  background-color: #e92e3d;
  border: none;
  outline: none;
  color: #fff;
  padding: 15px 0;
  width: 150px;
  letter-spacing: 1px;
  border-radius: 5px;
  text-transform: uppercase;
  cursor: pointer;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
}
.roll-boxes {
  display: inline-flex;
  gap: 4px;
}
.roll-box {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 20px;
  height: 20px;
  border: 1px solid rgba(255, 255, 255, 0.7);
  border-radius: 4px;
  font-size: 12px;
  line-height: 1;

  &.used {
    background-color: rgba(0, 0, 0, 0.35);
    border-color: transparent;
  }
}
</style>
