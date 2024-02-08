<script setup>
const route = useRoute();
const router = useRouter();
const config = useRuntimeConfig();
const {data: trainer, refresh} = await useFetch(
  () => `/api/trainer/${route.params.name}`,
  {
    default: () => [],
    baseUrl: config.public.backendOrigin,
  },
);
const onDelete = async () => {
  const response = await $fetch(`/api/trainer/${route.params.name}`, {
    baseURL: config.public.backendOrigin,
    method: "DELETE",
  }).catch((e) => e);
  if (response instanceof Error) return;
  router.push("/");
};
const nickname = ref("");
const onNickname = async (pokemon) => {
  const newTrainer = trainer.value;
  const index = newTrainer.pokemons.findIndex(({ id }) => id === pokemon.id);
  newTrainer.pokemons[index].nickname = trimAvoidCharacters(nickname.value);
  nickname.value = "";
  const response = await $fetch(`/api/trainer/${route.params.name}`, {
    baseURL: config.public.backendOrigin,
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(newTrainer),
  }).catch((e) => e);
  if (response instanceof Error) return;
  await refresh();
  onCloseNickname();
};
const onRelease = async (pokemonId) => {
  const response = await fetch(
    `/api/trainer/${route.params.name}/pokemon/${pokemonId}`,
    {
      baseURL: config.public.backendOrigin,
      method: "DELETE",
    },
  ).catch((e) => e);
  if (response instanceof Error) return;
  await refresh();
  onCloseRelease();
};
const {
  dialog: deleteDialog,
  onOpen: onOpenDelete,
  onClose: onCloseDelete,
} = useDialog();
const {
  dialog: nicknameDialog,
  onOpen: onOpenNickname,
  onClose: onCloseNickname,
} = useDialog();
const {
  dialog: releaseDialog,
  onOpen: onOpenRelease,
  onClose: onCloseRelease,
} = useDialog();
</script>


<template>
  <div>
    <!-- トレーナー情報を表示 -->
    <h1>トレーナー情報</h1>
    <div class="trainer-info">
      <img src="/avatar.png" />
      <span>{{trainer.name}}</span>
    </div>
    <GameifyButton @click="onOpenDelete(true)">マサラタウンに帰る(トレーナー情報の削除)</GameifyButton>
      <!-- 手持ちのポケモンを表示 -->
    <h2>手持ちのポケモン</h2>
    <CatchButton :to="`/trainer/${route.params.name}/catch`"
      >ポケモンを掴まえる</CatchButton>
    <!-- ポケモンのニックネーム変更、博士へ送る -->
      <GamifyList>
      <GamifyItem v-for="pokemon in trainer.pokemons" :key="pokemon.id">
        <img :src="pokemon.sprites.front_default" />
        <span class="pokemon-name">{{ pokemon.nickname || pokemon.name }}</span>
        <GamifyButton @click="onOpenNickname(pokemon)">ニックネームを付ける</GamifyButton>
        <GamifyButton @click="onOpenRelease(pokemon)">オーキド博士に送る</GamifyButton>      
    </GamifyList>
    <!-- マサラタウンに帰る前に、確認 -->
    <!-- オーキド博士に送る前に、確認 -->




    
  </div>

</template>