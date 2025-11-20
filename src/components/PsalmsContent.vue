<script setup>
import { ref, onMounted } from "vue";

const verses = ref(null);

onMounted(async () => {
  const module = await import("../data/verse.json"); // Await required
  verses.value = module.default;                     // Actual JSON data
});
</script>
