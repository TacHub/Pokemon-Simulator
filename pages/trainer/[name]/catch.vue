<script setup>
const route = useRoute();
const router = useRouter();
const config = useRuntimeConfig();
const page = ref(0);
const limit = ref(20);
const offset = computed(() => page.value * limit.value);
const {data: pokemons, refresh} = await useFetch(
  () =>
    `https://pokeapi.co/api/v2/pokemon?offset=${offset.value}&limit=${limit.value}`,
  {
    default: () => [],
  },
);
const hasPrev = computed(() => page.value > 0);
const maxPage = computed(() => Math.floor(pokemons.value.count / limit.value));
const hasNext = computed(() => page.value < maxPage.value);
const onPrev = async () => {
  page.value--;
  await refresh();
};
const onNext = async () => {
  page.value++;
  await refresh();
};
const onCatch = async (pokemon) => {
  const response = await $fetch(
    `/api/trainer/${route.params.name}/pokemon/${pokemon.name}`,
    {
      baseURL: config.public.backendOrigin,
      method: "PUT",
    },
  ).catch((e) => { // エラーをキャッチして、エラーメッセージを表示する
    console.error(e);
    alert("エラーが発生しました。");
    return e;
  });
  if (response instanceof Error) return;
  router.push(`/trainer/${route.params.name}`);
};
const { dialog, onOpen, onClose } = useDialog();
</script>
<template></template>
