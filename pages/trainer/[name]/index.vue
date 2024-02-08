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
    <GamifyDialog
      v-if="deleteDialog"
      id="confirm-delete"
      title="確認"
      description="本当にマサラタウンに帰るんだな？！この操作は取り消せないぞ！！"
      @close="onCloseDelete"
      >
      <GamifyList :border="false" direction="horizon">
        <GamifyItem>
          <GamifyButton @click="onCloseDelete">いいえ</GamifyButton>
        </GamifyItem>
        <GamifyItem>
          <GamifyButton @click="onDelete">はい</GamifyButton>
        </GamifyItem>
      </GamifyList>
    </GamifyDialog>

    <!-- ポケモンのニックネーム変更 -->
    <GamifyDialog
      v-if = "nicknameDialog"
      id="confirm-nickname"
      title="ニックネーム"
      :description="`${nicknameDialog.name}の新しいニックネームは？`"
      @close="onCloseNickname"
    >
      <div class="item">
        <label for="name">ニックネーム</label>
        <input
          id="name"
          v-model="nickname"
          @keydown.enter="onNickname(nicknameDialog)"
        />
      </div>
      <!-- ニックネーム変更にキャンセル or 決定 -->
      <GamifyList :border="false" direction="horizon">
        <GamifyItem>
          <GamifyButton @click="onCloseNickname">キャンセル</GamifyButton>
        </GamifyItem>
        <GamifyItem>
          <GamifyButton @click="onNickname(nicknameDialog)">決定</GamifyButton
          >
        </GamifyItem>
      </GamifyList>
    </GamifyDialog>
    <!-- オーキド博士に送る前に、確認 -->
  <GamifyDialog>
    
  </GamifyDialog>



    
  </div>

</template>