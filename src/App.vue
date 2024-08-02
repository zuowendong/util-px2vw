<template>
  <header class="mb-5">
    <h1 className="text-3xl font-bold underline text-[#23ac23] mb-5">
      PX TO VW
    </h1>

    <div class="flex items-center">
      <span class="shrink-0 mr-3">设计稿宽度</span>

      <label class="input input-bordered flex items-center gap-2">
        <input
          v-model="defaultScreenWidth"
          type="text"
          class="grow"
          placeholder="设计稿宽度"
          @keyup.enter="handleChangeScreen"
        />
        <span class="badge badge-info">px</span>
      </label>
    </div>
  </header>

  <main>
    <section class="mb-5">
      <div class="mb-3">请输入目标像素值</div>

      <label class="input input-bordered flex items-center gap-2">
        <input
          v-model="targetPXValue"
          type="text"
          class="grow"
          placeholder="目标像素值"
          @keyup.enter="handleSubmit"
        />
        <span class="badge badge-info">px</span>
      </label>
    </section>

    <section class="mb-5">
      <div class="mb-3">计算结果</div>

      <div class="flex items-center">
        <div
          v-if="resultVal"
          tabindex="0"
          ref="resultRef"
          class="w-[100px] h-[32px] flex items-center justify-center border border-[#666] rounded-xl cursor-pointer"
          @click="handleCopy"
        >
          {{ resultVal }}
        </div>
      </div>
    </section>

    <section>
      <div class="mb-3">历史记录</div>
      <div v-for="(item, index) in historyList" :key="index" class="mb-2">
        <span>{{ item[0] }}px</span>
        <span class="px-3">--></span>
        <span>{{ item[1] }}</span>
      </div>
    </section>
  </main>
</template>

<script setup lang="ts">
import { onMounted, ref } from "vue";
import { useClipboard } from "@vueuse/core";

const resultRef = ref();
const { copy } = useClipboard();
const isCopy = ref(false);
const handleCopy = () => {
  copy(resultRef.value.innerText);

  isCopy.value = true;
};

const defaultScreenWidth = ref(0);

onMounted(() => {
  const value = localStorage.getItem("defaultScreenWidth");
  defaultScreenWidth.value = Number(value || 375);
});

const targetPXValue = ref("");
const resultVal = ref("");

const historyMap = ref(new Map());
const historyList = ref<any>([]);

function handleSubmit() {
  const val1 = Number(targetPXValue.value);
  const val2 = Number(defaultScreenWidth.value);

  if (val1 > val2) {
    resultVal.value = `${val1}PX`;
  } else {
    const res = (val1 / val2) * 100;
    resultVal.value = `${res.toFixed(2)}vw`;
  }

  historyMap.value.set(targetPXValue.value, resultVal.value);
  historyList.value = Array.from(historyMap.value);
}

function handleChangeScreen() {
  localStorage.setItem("defaultScreenWidth", String(defaultScreenWidth.value));
}
</script>

<style scoped></style>
