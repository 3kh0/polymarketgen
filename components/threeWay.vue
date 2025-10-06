<template>
  <div class="flex flex-col gap-2 max-h-[76px] overflow-y-scroll">
    <div
      v-for="(outcome, idx) in outcomes"
      :key="idx"
      class="flex flex-row items-center gap-2 text-white text-sm justify-between"
    >
      <div class="flex-1 flex flex-row items-center gap-2">
        <span class="text-left">{{ outcome.name }}</span>
      </div>
      <div class="flex flex-row items-center gap-1 min-w-[120px] justify-end">
        <div class="w-12 p-0 m-0 text-right font-[500]">
          {{ outcome.percent }}%
        </div>
        <button
          class="ml-1 px-2 py-1 rounded text-[#27AE60] bg-[#325355] hover:bg-[#27AE60] hover:text-white text-xs font-[500] min-w-[36px]"
        >
          Yes
        </button>
        <button
          class="px-2 py-1 rounded text-[#e13737] bg-[#524350] hover:bg-[#e13737] hover:text-white text-xs font-[500] min-w-[36px]"
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
