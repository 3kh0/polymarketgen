<template>
  <div class="flex flex-col gap-3 mt-auto">
    <div class="flex gap-3">
      <button
        class="group flex-1 py-2 rounded-[4px] font-semibold text-[#27AE60] text-[14px] bg-[#36615d] hover:bg-[#27AE60] hover:text-white transition-colors flex items-center justify-center gap-1 h-[36px]"
      >
        {{ greenLabel }}
        <Icon
          :name="greenChevron"
          class="w-[18px] h-[18px] text-[#27AE60] flex-shrink-0 transition-colors group-hover:text-white group-hover:animate-pulse-opacity"
        />
      </button>
      <button
        class="group flex-1 py-2 rounded-[4px] font-semibold text-[#E64800] text-[14px] bg-[#524350] hover:bg-[#b93a03] hover:text-white transition-colors flex items-center justify-center gap-1 h-[36px]"
      >
        {{ redLabel }}
        <Icon
          :name="redChevron"
          class="w-[18px] h-[18px] text-[#E64800] flex-shrink-0 transition-colors group-hover:text-white group-hover:animate-pulse-opacity"
        />
      </button>
    </div>
  </div>
</template>

<script setup>
defineProps({
  greenLabel: String,
  redLabel: String,
  greenChevron: String,
  redChevron: String,
});
</script>
