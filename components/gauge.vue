<template>
  <svg viewBox="0 0 1000 800" class="gauge-svg">
    <!-- Track -->
    <path
      d="M 950 500 A 450 450 0 0 0 50 500"
      fill="none"
      stroke="#858d92"
      stroke-width="60"
    />
    <!-- Value Arc -->
    <path
      :d="arcPath"
      fill="none"
      :stroke="arcColor"
      stroke-width="60"
      class="data-arc"
      style="transition: stroke-dashoffset 0.6s cubic-bezier(0.4, 2, 0.6, 1)"
    />
    <!-- chance label -->
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
    <!-- Title label -->
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
      {{ title }}
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

function polarToCartesian(cx, cy, radius, angle) {
  const radians = ((angle - 90) * Math.PI) / 180.0;
  return [
    Math.round((cx + radius * Math.cos(radians)) * 100) / 100,
    Math.round((cy + radius * Math.sin(radians)) * 100) / 100,
  ];
}

function svgArcPath(x, y, radius, startAngle, endAngle) {
  const start = polarToCartesian(x, y, radius, endAngle);
  const end = polarToCartesian(x, y, radius, startAngle);
  return `M ${start[0]} ${start[1]} A ${radius} ${radius} 0 0 0 ${end[0]} ${end[1]}`;
}

const arcPath = computed(() => {
  // Value is 0-100, map to 0-180 degrees
  const endAngle = (props.value / 100) * 180 - 90;
  return svgArcPath(500, 500, 450, -90, endAngle);
});
</script>

<style scoped>
.gauge-svg {
  font-family: "Open Sauce Sans", sans-serif;

  max-width: 70px;
  width: 100%;
  max-height: 70px;
  height: 100%;
  height: auto;
  display: block;
}
.chance {
  font-family: "Open Sauce Sans", sans-serif;
}
.title {
  font-family: "Open Sauce Sans", sans-serif;
}
</style>
