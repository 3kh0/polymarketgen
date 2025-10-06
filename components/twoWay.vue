<template>
  <div class="flex flex-col gap-3 mt-auto">
    <div class="flex gap-3">
      <button
        class="group flex-1 py-2 rounded-[4px] font-semibold text-[#43c773] text-[14px] bg-[#325355] hover:bg-[#43c773] hover:text-white transition-colors flex items-center justify-center gap-1 h-[36px]"
      >
        {{ yesText }}
      </button>
      <button
        class="group flex-1 py-2 rounded-[4px] font-semibold text-[#e13737] text-[14px] bg-[#524350] hover:bg-[#e13737] hover:text-white transition-colors flex items-center justify-center gap-1 h-[36px]"
      >
        {{ noText }}
      </button>
    </div>
  </div>
</template>

<script setup>
defineProps({
  yesText: String,
  noText: String,
});
</script>
