<template>
  <div class="relative flex flex-col items-center divvy">
    <div class="relative w-20 h-20">
      <svg :viewBox="vb" class="g">
        <path
          :d="bgarc"
          fill="none"
          stroke="#4d657c"
          :stroke-width="strokew"
          stroke-linecap="round"
        />
        <path
          :d="aarc"
          fill="none"
          :stroke="color"
          :stroke-opacity="1"
          :stroke-width="strokew"
          stroke-linecap="round"
        />
      </svg>

      <div class="absolute inset-0 flex flex-col items-center justify-center">
        <p class="font-medium text-[16px] text-center text-white dark:text-white">
          {{ value }}%
        </p>
        <p class="font-medium text-gray-400 text-[11px] text-center line-clamp-2">
          {{ title === "Yes" ? "chance" : title }}
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from "vue";

const props = defineProps({
  value: {
    type: Number,
    required: true,
  },
  title: {
    type: String,
    default: "",
  },
});

const radius = 40;
const strokew = 6;
const size = radius * 2 + strokew * 2;
const center = size / 2;

const vb = computed(() => {
  const padding = strokew;
  return `${-center - padding} ${-center - padding} ${size + padding * 2} ${size + padding * 2}`;
});

const color = computed(() => {
  if (props.value < 26) return "#e13737"; // red
  if (props.value < 50) return "#fe9a00"; // amber
  return "#69d290"; // green
  // polymarket also adjusts opacity based on higher values
  // its cringe, im not doing that shit
});

function fuckass(centerX, centerY, radius, angle) {
  const rad = (angle - 90) * Math.PI / 180.0;
  return {
    x: centerX + (radius * Math.cos(rad)),
    y: centerY + (radius * Math.sin(rad))
  };
}

function arc(x, y, radius, starta, enda) {
  const start = fuckass(x, y, radius, enda);
  const end = fuckass(x, y, radius, starta);
  const flag = enda - starta <= 180 ? "0" : "1";

  return [
    "M", start.x, start.y,
    "A", radius, radius, 0, flag, 0, end.x, end.y
  ].join(" ");
}

const bgarc = computed(() => {
  return arc(0, 0, radius, -100, 100);
});

const aarc = computed(() => {
  if (props.value === 0) return "";

  const max = 200;
  const active = (props.value / 100) * max;
  const start = -100;
  const end = start + active - 5;

  return arc(0, 0, radius, start, end);
});
</script>

<style scoped>
.g {
  width: 100%;
  height: 100%;
  overflow: visible;
  display: block;
}
</style>
