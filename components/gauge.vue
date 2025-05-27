<template>
  <svg viewBox="0 0 1000 800">
    <path
      d="M 950 500 A 450 450 0 0 0 50 500"
      fill="none"
      stroke="#858d92"
      stroke-width="60"
    />
    <path
      :d="a"
      fill="none"
      :stroke="arcColor"
      stroke-width="60"
      class="data-arc"
      style="transition: stroke-dashoffset 0.6s cubic-bezier(0.4, 2, 0.6, 1)"
    />
    <!-- jank a tron 9000 -->
    <text
      class="chance"
      text-anchor="middle"
      alignment-baseline="middle"
      x="500"
      y="490"
      font-size="210"
      font-weight="450"
      fill="#fcfcfc"
    >
      {{ value }}%
    </text>
    <text
      class="title"
      text-anchor="middle"
      alignment-baseline="middle"
      x="500"
      y="720"
      font-size="160"
      font-weight="500"
      fill="#78848e"
    >
      {{ title === "Yes" ? "chance" : title }}
    </text>
  </svg>
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

const arcColor = computed(() => {
  return props.value < 50 ? "#c65e30" : "#31aa65";
});

function f(cx, cy, rad, angle) {
  const rd = ((angle - 90) * Math.PI) / 180.0;
  return [
    Math.round((cx + rad * Math.cos(rd)) * 100) / 100,
    Math.round((cy + rad * Math.sin(rd)) * 100) / 100,
  ];
}

function p(x, y, rad, sa, ea) {
  const start = f(x, y, rad, ea);
  const end = f(x, y, rad, sa);
  return `M ${start[0]} ${start[1]} A ${rad} ${rad} 0 0 0 ${end[0]} ${end[1]}`;
}

const a = computed(() => {
  const ea = (props.value / 100) * 180 - 90;
  return p(500, 500, 450, -90, ea);
});
</script>

<style scoped>
svg {
  font-family: "Open Sauce Sans", sans-serif;
  max-width: 70px;
  width: 100%;
  max-height: 70px;
  height: 100%;
  height: auto;
  display: block;
}
.chance,
.title {
  font-family: "Open Sauce Sans", sans-serif;
}
</style>
