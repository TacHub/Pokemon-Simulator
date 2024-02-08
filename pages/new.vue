<script setup>
const router = useRouter();
const config = useRuntimeConfig();
const trainerName = ref("");
const safeTrainerName = computed(() => trimAvoidCharacters(trainerName.value)); // 1
const valid = computed(() => safeTrainerName.value.length > 0); // 2
const onSubmit = async () => {
  const response = await $fetch("/api/trainer", { // 3
    baseURL: config.public.backendOrigin,
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      name: safeTrainerName.value,
    }),
  }).catch((e) => e);
  if (response instanceof Error) return;
  router.push(`/trainer/${safeTrainerName.value}`);
};
const { dialog, onOpen, onClose } = useDialog(); // 4
</script>

<template>
  <div>
    <h1>あたらしくはじめる</h1>
    <!-- ページに反映させる文字などを記述する -->
    <p>では初めに、君の名前を教えてもらおう！</p>
    <form @submit.prevent> 
      <div class="item">
        <label for="name">名前</label>
        <span id="name-description">特定の文字は取り除かれるぞ！</span>
        <input
          id="name"
          v-model="trainerName"
          aria-describedby="name-description"
          @keydown.enter="valid && onOpen(true)"
        />
      </div>
      <GamifyButton type="button" :disabled="!valid" @click="onOpen(true)"
        >決定</GamifyButton
      >
    </form>
    <!-- ここまで、名前の入力と「決定」ボタンを反映 -->
    <GamifyDialog
      v-if="dialog"
      id="confirm-submit"
      title="確認"
      :description="`ふむ・・・君の名前は「${safeTrainerName}」というんだな！`"
      @close="onClose"
    >
      <GamifyList :border="false" direction="horizon">
        <GamifyItem>
          <GamifyButton @click="onClose">いいえ</GamifyButton>
        </GamifyItem>
        <GamifyItem>
          <GamifyButton @click="onSubmit">はい</GamifyButton>
        </GamifyItem>
      </GamifyList>
    </GamifyDialog>
    <!-- 名前の確認欄を実装 -->
  </div>
</template>

<style scoped>
form {
  border-radius: 0.5rem;
  border: solid 4px #555;
  padding: 1.5rem 3rem;
}

form > :not(:last-child) {
  display: block;
  margin-bottom: 1rem;
}

.item > label,
.item > span {
  display: block;
  margin-bottom: 0.25rem;
}
.item > span {
  font-size: 0.875rem;
}
</style>
