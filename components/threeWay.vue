<template>
  <div class="outcome-scroll flex flex-col gap-2">
    <div
      v-for="(outcome, idx) in outcomes"
      :key="idx"
      class="flex flex-row items-center gap-2 text-white text-sm outcome-row justify-between"
    >
      <div class="flex-1 flex flex-row items-center gap-2">
        <span class="text-left">{{ outcome.name }}</span>
      </div>
      <div class="flex flex-row items-center gap-1 min-w-[120px] justify-end">
        <div class="w-12 p-0 m-0 text-right font-[500]">
          {{ outcome.percent }}%
        </div>
        <button
          class="ml-1 px-2 py-1 rounded text-[#27AE60] bg-[#2c4b51] hover:bg-[#27AE60] hover:text-white text-xs font-[500]"
        >
          Yes
        </button>
        <button
          class="px-2 py-1 rounded text-[#E64800] bg-[#404250] hover:bg-[#b93a03] hover:text-white text-xs font-[500]"
        >
          No
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  outcomes: {
    type: Array,
    required: true,
  },
});
</script>

<style scoped>
button {
  min-width: 36px;
}
.outcome-scroll {
  max-height: 76px;
  overflow-y: scroll;
}

.outcome-row {
  /* Ensure row spacing and alignment */
  align-items: center;
}
</style>
