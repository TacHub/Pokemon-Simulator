<script setup>
const route = useRoute();
const router = useRouter();
const config = useRuntimeConfig();
const page = ref(0);
const limit = ref(20);
const offset = computed(() => page.value * limit.value);



</script>