<template>
  <div class="flex items-center mt-2">
    <span
      class="text-[#858D92] text-[12px] flex items-center gap-1 w-full h-[18px]"
    >
      {{ vol }} Vol.
      <svg
        v-if="repeat !== 'none'"
        stroke="currentColor"
        fill="currentColor"
        stroke-width="0"
        viewBox="0 0 24 24"
        style="
          color: #828282;
          opacity: 0.87;
          width: 1.15rem;
          height: 1.15rem;
          margin-bottom: 1px;
        "
        height="1em"
        width="1em"
        xmlns="http://www.w3.org/2000/svg"
      >
        <path
          d="M19 7c0-.553-.447-1-1-1h-8v2h7v5h-3l3.969 5L22 13h-3V7zM5 17c0 .553.447 1 1 1h8v-2H7v-5h3L6 6l-4 5h3V17z"
        ></path>
      </svg>
      <span v-if="repeat !== 'none'" class="text-[#858D92] text-[12px]">{{
        repeat
      }}</span>
    </span>
    <div class="flex gap-2 items-center">
      <Icon name="lucide:gift" class="text-[#858d92]" size="16px" />
      <Icon name="lucide:bookmark" class="text-[#858d92]" size="16px" />
      <!-- it seems like they use lucide? not exactly sure tbh-->
    </div>
  </div>
</template>

<script setup>
defineProps({
  vol: String,
  repeat: String,
});
</script>
