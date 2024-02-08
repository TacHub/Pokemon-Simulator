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
    <form @submit.prevent></form>
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
