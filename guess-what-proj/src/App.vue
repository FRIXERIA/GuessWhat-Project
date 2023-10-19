<script setup>
import { ref, onMounted, computed } from "vue";
import { answers } from "../datas/answer.js";

//images, icons, etc.
const myLogo = "./imgs/logo.svg";
const scoreBg = "./imgs/bg-score.svg";
const pronounceBg = "./imgs/bg-pronounce.svg";
const hint = "./imgs/hint.svg";
const numBg = "./imgs/bg-num.svg";
const creditList = "./imgs/credit-list.svg";
const tuto = "./imgs/tutorial.svg";

//variables
const isDesktop = ref(false);
const ansCorrect = answers;
const ansInput = ref([]);
const correctPop = ref(false);
const incorrectPop = ref(false);
const score = ref(0);
const remainingTime = ref(0);
const showHint = ref(new Array(answers.length).fill(false));
const isButtonDisabled = ref(false);
const isSubmitted = ref([]);
const tutoBtn = ref(false);
const creditBtn = ref(false);
const timeoutPop = ref(false);
const scrollNext = ref();
const endPop = ref(false);

const tutoPop = () => {
  return (tutoBtn.value = true);
};
const creditPop = () => {
  return (creditBtn.value = true);
};

//hint fn
const hintBtn = (index) => {
  showHint.value[index] = !showHint.value[index];
  setTimeout(() => {
    showHint.value[index] = !showHint.value[index];
  }, 5000);
};

//scroll to the next question
const scrollToNextQuestion = (index) => {
  if (index < answers.length - 1) {
    console.log(scrollNext.value[index + 1]);
    window.scrollTo({
      top: scrollNext.value[index + 1].offsetTop, //document.getElementById(`question-${index + 1}`).offsetTop,
      behavior: "smooth",
    });
  } else if (index >= answers.length - 1) {
    incorrectPop.value = false;
    correctPop.value = false;
    return (endPop.value = true);
  }
};

//check ans fn
const checkAnswer = (index) => {
  if (!isSubmitted[index]) {
    isSubmitted[index] = true;
    const answer = ansInput.value;
    
    if (answer[index] === ansCorrect[index].ans) {
      // console.log(${answer[index]} - true - ${answers[index].ans});
      correctPop.value = true;
      setTimeout(() => {
        scrollToNextQuestion(index);
      }, 1000); //ตั้งหน่วงเวลาให้เห็นเฉลย 1 วิ ก่อนเลื่อนไปข้อถัดไป
      score.value++;
      isButtonDisabled.value[index] = true;
      return true;
    }
    if (answer[index] !== answers[index].ans) {
      // console.log(${answer[index]} - false - ${answers[index].ans});
      answer[index] = ansCorrect[index].ans; //เฉลย
      incorrectPop.value = true;
      setTimeout(() => {
        scrollToNextQuestion(index);
      }, 1000);
      return false;
    }
  }
};

//time out fn
const startTimeout = () => {
  remainingTime.value = 300000;
  const countdownInterval = setInterval(() => {
    remainingTime.value -= 100;
    if (remainingTime.value <= 0) {
      clearInterval(countdownInterval);
      timeoutPop.value = true;
    }
  }, 100);
};

const remainingMinutes = computed(() => {
  const minutes = Math.floor(remainingTime.value / 60000);
  return minutes < 10 ? `0${minutes}` : `${minutes}`;
});

const remainingSeconds = computed(() => {
  const seconds = Math.floor((remainingTime.value % 60000) / 1000);
  return seconds < 10 ? `0${seconds}` : `${seconds}`;
});

//responsive
const checkResolution = () => {
  if (window.innerWidth < 768) {
    isDesktop.value = false;
  } else {
    isDesktop.value;
  }
};


//onMounted
onMounted(() => {
  checkResolution();
  window.addEventListener("resize", checkResolution);
  return () => {
    window.removeEventListener("resize", checkResolution);
  };
});
</script>

<template>
  <div v-if="remainingTime <= 0">
    <div
      class="bg-bg-firstPage bg-cover w-full flex flex-col items-center"
      style="height: 750px"
    >
      <img :src="myLogo" style="width: 608px; height: 423px" />
      <div class="flex flex-col space-y-6">
        <button
          @click="startTimeout"
          class="cursor-pointer rounded-full bg-yellow-300 font-semibold transition duration-200 ease-in-out hover:bg-yellow-200 text-indigo-950 text-5xl"
          style="width: 311px; height: 78px"
        >
          START
        </button>
        <div>
          <button
            class="cursor-pointer rounded-full bg-yellow-300 font-semibold transition duration-200 ease-in-out hover:bg-yellow-200 text-indigo-950 text-5xl"
            style="width: 311px; height: 78px"
            @click="tutoPop()"
          >
            TUTORIAL
          </button>
          <div v-show="tutoBtn">
            <div class="popup overflow-hidden">
              <div
                class="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-screen h-screen bg-black opacity-60"
              ></div>
            </div>
            <div class="popup relative">
              <div
                class="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 bg-yellow-200 rounded-xl"
                style="width: 1051px; height: 679px"
              >
                <div class="top-10">
                  <button
                    style="margin-left: 980px"
                    class="absolute font-bold text-gray-800 mt-2 p-2 text-2xl rounded-md hover:bg-red-400 transition duration-500 ease-in-out flex-col"
                    @click="tutoBtn = false"
                  >
                    X
                  </button>
                  <div>
                    <img
                      :src="tuto"
                      style="margin-left: 73px; padding-top: 20px"
                    />
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div>
          <button
            class="cursor-pointer rounded-full bg-yellow-300 font-semibold transition duration-200 ease-in-out hover:bg-yellow-200 text-indigo-950 text-5xl"
            style="width: 311px; height: 78px"
            @click="creditPop()"
          >
            CREDIT
          </button>
          <div v-show="creditBtn">
            <div class="popup overflow-hidden">
              <div
                class="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-screen h-screen bg-black opacity-60"
              ></div>
            </div>
            <div class="popup">
              <div
                class="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 bg-yellow-200 rounded-xl"
                style="width: 994px; height: 623px"
              >
                <div class="top-10">
                  <button
                    style="margin-left: 950px"
                    class="font-bold text-gray-800 mt-2 p-2 text-2xl rounded-md hover:bg-red-400 transition duration-500 ease-in-out flex-col"
                    @click="creditBtn = false"
                  >
                    X
                  </button>
                  <div>
                    <img :src="creditList" style="padding-right: 100px" />
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <p
        class="font-semibold text-yellow-300 text-xl"
        style="padding-left: 1350px"
      >
        By QUARTZ
      </p>
    </div>
  </div>
  <div v-if="remainingTime > 0">
    <div
      class="bg-bg-question bg-cover w-full border border-b-8 border-orange-400"
      style="height: 770px"
      v-for="(question, index) in answers"
      :key="index"
      ref="scrollNext"
    >
      <div>
        <div class="w-full relative" id="header">
          <img
            :src="numBg"
            class="absolute left-10"
            style="padding-top: 50px; width: 120px"
          />
          <p
            class="absolute font-extrabold text-6xl top-16"
            style="padding-top: 10px"
            :style="
              question.Number >= 10
                ? 'padding-left: 65px;'
                : 'padding-left: 81px;'
            "
          >
            {{ question.Number }}
          </p>
          <img :src="scoreBg" class="absolute top-4 end-7" />
          <p class="absolute top-6 end-20 font-extrabold text-6xl">
            {{ score }} / 10
          </p>
          <div class="flex justify-center">
            <img :src="pronounceBg" class="absolute top-24 pt-[20px]" />
            <p class="absolute top-24 pt-[20px] font-extrabold text-3xl">
              {{ question.pronounce }}
            </p>
            <div
              v-for="(img, index) in question.pic"
              :key="index"
              class="pt-52"    
            >
              <img
                :src="img.path"
                class="ml-2 mr-2"
                style="width: 214px; height: 203px"
              />
            </div>
          </div>
        </div>
        <div
          class="w-full flex flex-row justify-center items-center pt-28"
          id="middle"
        >
          <input
            type="text"
            class="w-1/4 p-7 border rounded-xl mr-5 font-bold text-3xl text-center"
            v-model="ansInput[index]"
          />
          <button
            @click="checkAnswer(index)"
            type="submit"
            class="btn bg-amber-950 rounded-full w-32 h-12 font-bold text-lg text-yellow-100"
            :disabled="isSubmitted[index] || isButtonDisabled"
          >
            ANSWER
          </button>
        </div>
        <div id="footer" class="w-full relative">
          <div>
            <p
              v-if="remainingTime > 0"
              class="text-white font-extrabold text-2xl top-16 ml-10 absolute"
            >
              Remaining Time: {{ remainingMinutes }}:{{ remainingSeconds }}
              minutes
            </p>
          </div>
          <button v-on:click="hintBtn(index)">
            <img :src="hint" class="absolute end-7 floating-img" />
          </button>
          <div
            class="floating-img flex justify-end mr-36"
            v-show="showHint[index]"
          >
            <p
              class="text-center text-xl font-bold text-black border-8 hintText bg-orange-200 border-orange-400 rounded-3xl w-[280px] p-5"
            >
              {{ question.hint }}
            </p>
          </div>
        </div>
      </div>
    </div>
    <div v-show="correctPop">
      <div class="popup overflow-hidden">
        <div
          class="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-screen h-screen bg-black opacity-60"
        ></div>
      </div>
      <div class="popup">
        <div
          class="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-1/4 h-36 bg-green-500 rounded-xl"
        >
          <div class="top-10">
            <button
              style="margin-left: 330px"
              class="font-bold text-gray-800 pl-3 pr-3 mt-2 text-2xl rounded-md hover:bg-red-400 transition duration-500 ease-in-out flex-col"
              @click="
                correctPop = false;
                scrollToNextQuestion(index);
              "
            >
              X
            </button>
            <p class="text-black text-center text-4xl mt-2">CORRECT!!</p>
          </div>
        </div>
      </div>
    </div>
    <div v-show="incorrectPop">
      <div class="popup">
        <div
          class="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-screen h-screen bg-black opacity-60"
        ></div>
      </div>
      <div class="popup">
        <div
          class="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-1/4 h-36 bg-red-500 rounded-xl"
        >
          <div class="top-10">
            <button
              style="margin-left: 330px"
              class="font-bold text-gray-800 pl-3 pr-3 mt-2 text-2xl rounded-md hover:bg-red-400 transition duration-500 ease-in-out flex-col"
              @click="
                incorrectPop = false;
                scrollToNextQuestion(index);
              "
            >
              X
            </button>
            <p class="text-black text-center text-4xl mt-2">INCORRECT!!</p>
          </div>
        </div>
      </div>
    </div>
    <div v-show="endPop">
 
      <div class="popup overflow-hidden">
        <div
          class="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-screen h-screen bg-black opacity-60"
        ></div>
      </div>
      <div class="popup">
        <div
          class="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-1/3 h-60 bg-yellow-200 rounded-xl"
        >
          <div class="">
            <button
              style="margin-left: 450px"
              class="font-bold text-gray-800 pl-3 pr-3 mt-2 text-2xl rounded-md hover:bg-red-400 transition duration-500 ease-in-out flex-col"
              @click="
                endPop = false;
                remainingTime = 0;
                ansInput = [];
                score = 0;
              "
            >
              X
            </button>
            <div class="flex flex-col space-y-3 justify-center items-center">
              <p class="text-center text-4xl text-green-700">
                Correct: {{ score }}
              </p>
              <p class="text-center text-4xl text-red-700">
                False: {{ answers.length - score }}
              </p>
              <p
                class="text-center text-4xl text-indigo-950 bg-amber-500 pt-1 h-14 w-96 rounded-full"
              >
                Total Score: {{ score }} / {{ answers.length }}
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
p {
  font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
    Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
}

@keyframes floatAnimation {
  0% {
    transform: translateY(0px);
  }
  50% {
    transform: translateY(-10px);
  }
  100% {
    transform: translateY(0px);
  }
}
.floating-img {
  animation: floatAnimation 3s ease-in-out infinite;
}
.not-ans {
  border: 5px solid white;
}
.correct-ans {
  border: 5px solid limegreen;
}
.incorrect-ans {
  border: 5px solid red;
}
</style>
