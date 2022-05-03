<template>
  <div class="container jumbotron my-5 anadiv pb-4">
    <h2 class="display-4">Hızlı Yazma Egzersizleri</h2>
    <p class="lead">
      Klavye Hızınızı Test Edebilirsiniz...Süreniz Yeşil kutucukta yazmaktadır.
    </p>
    <div
      class="status bg-light mb-3 fs-5 d-flex justify-content-between align-content-center"
    >
      <div class="mesaj">
        Doğru Kelime Sayısı : {{ trueCount }} - Yanlış Kelime Sayısı :
        {{ falseCount }}
      </div>
      <div>
        <button
          class="btn btn-lg btn-primary"
          style="border-radius : 0px"
          type="button"
          @click="yenile()"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="16"
            height="16"
            fill="currentColor"
            class="bi bi-arrow-clockwise"
            viewBox="0 0 16 16"
          >
            <path
              fill-rule="evenodd"
              d="M8 3a5 5 0 1 0 4.546 2.914.5.5 0 0 1 .908-.417A6 6 0 1 1 8 2v1z"
            />
            <path
              d="M8 4.466V.534a.25.25 0 0 1 .41-.192l2.36 1.966c.12.1.12.284 0 .384L8.41 4.658A.25.25 0 0 1 8 4.466z"
            />
          </svg>
        </button>
      </div>
    </div>
    <div v-if="!isFinished">
      <div class="card mb-2">
        <div class="card-body">
          <span
            v-for="(k, i) in state.kelime.filter((data, index) => index < 15)"
            :key="i"
            :class="i != 0 || classControl"
            class="m-2 fs-4 fw-normal"
          >
            {{ k }}
          </span>
        </div>
      </div>

      <div class="card">
        <div class="card-body bg-secondary">
          <div class="input-group input-group-lg">
            <input
              type="text"
              class="form-control"
              placeholder="Start Typing"
              v-model="writed"
              @keydown="isStarted != true ? startTimer() : ''"
              @keydown.enter="bittiMi"
            />
            <div class="input-group-append" id="button-addon4">
              <button
                class="btn btn-lg btn-success"
                style="border-radius : 0px"
                type="button"
              >
                {{ timer }}
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div v-else>
      <h1>SÜRE TAMAMLANDI</h1>
    </div>
  </div>
</template>
<script setup>
import { reactive, watch, ref, onMounted } from "vue";
import kelimeler from "../assets/data.json";
const state = reactive({
  kelime: []
});

onMounted(() => {
  state.kelime = kelimeler.sort(() => Math.random() - 0.5).splice(0, 250);
});
const writed = ref("");

let isTrue = ref(1);
const classControl = ref("");
const trueCount = ref(0);
const falseCount = ref(0);
const timer = ref(60);

const isStarted = ref(false);
const isFinished = ref(false);
watch(writed, newV => {
  if (isStarted) {
    let lastNew = newV.replace(" ", "");
    if (state.kelime[0].slice(0, newV.length) === lastNew) {
      isTrue = 1;
    } else {
      isTrue = 0;
    }
    classControl.value = isTrue == 1 ? "wordClass" : "wordClass bg-danger";
    if (newV.indexOf(" ") !== -1) {
      isTrue == 1 ? trueCount.value++ : falseCount.value++;
      state.kelime.splice(0, 1);
      writed.value = "";
    }
  }
});
const bittiMi = () => {
  if (state.kelime[0] === writed.value) {
    isTrue = 1;
  } else {
    isTrue = 0;
  }
  classControl.value = isTrue == 1 ? "wordClass" : "wordClass bg-danger";
  isTrue == 1 ? trueCount.value++ : falseCount.value++;
  state.kelime.splice(0, 1);
  writed.value = "";
};
const startTimer = () => {
  isStarted.value = true;

  setInterval(() => {
    if (timer.value != 0) {
      timer.value--;
    } else {
      clearInterval();
      isFinished.value = true;
    }
  }, 1000);
};

const yenile = () => {
  location.reload();
};
</script>

<style scoped>
.lead {
  font-family: "Trebuchet MS", "Lucida Sans Unicode", "Lucida Grande",
    "Lucida Sans", Arial, sans-serif;
}
.anadiv {
  background-color: #266d69e6;
  color: #111;
  border-radius: 10px;
  box-shadow: 5px 5px 10px;
  height: 500px;
  padding: 30px;
  font-family: "Lucida Sans", "Lucida Sans Regular", "Lucida Grande",
    "Lucida Sans Unicode", Geneva, Verdana, sans-serif;
}
.wordClass {
  background-color: #eee;
  padding: 10px;
  border-radius: 4px;
}
.cervceve {
  background-color: #111;
}
.mesaj {
  height: 45px;
  line-height: 45px;
}
</style>
