<template>
  <div class="flex flex-row gap-6 w-full max-w-2xl mx-auto mt-10">
    <!-- Unified Card for all market types -->
    <div
      class="bg-[#2C3F4F] rounded-lg shadow-lg p-3 flex flex-col gap-3 h-[180px] w-[340px] border border-[#344452]"
    >
      <div class="flex flex-row items-start gap-3">
        <div class="flex flex-row items-center gap-3 flex-1">
          <div
            class="w-10 h-10 bg-gradient-to-br from-[#7f5af0] via-[#7f5af0] to-[#2cb67d] rounded-sm flex-shrink-0"
          ></div>
          <div class="text-white font-semibold text-[0.875rem] leading-tight">
            {{ marketTitle }}
          </div>
        </div>
        <div
          class="flex-shrink-0 flex items-center"
          v-if="marketType !== 'multiway'"
        >
          <Gauge :value="chance" :title="optionA" />
        </div>
      </div>
      <!-- bottom flexbox, align these to the bottom -->
      <div class="flex flex-col justify-between flex-1">
        <TwoWay
          v-if="marketType !== 'multiway'"
          :greenLabel="greenLabel"
          :redLabel="redLabel"
          :greenChevron="greenChevron"
          :redChevron="redChevron"
        />
        <ThreeWay
          v-if="marketType === 'multiway'"
          :outcomes="multiwayOutcomes"
        />
        <!-- Bottom Info (Volume, Series, Icons) -->
        <Footer :formattedVolume="formattedVolume" :seriesType="seriesType" />
      </div>
    </div>
    <!-- Config Options -->
    <div class="flex flex-col gap-6 flex-1 min-w-[220px]">
      <div>
        <label class="block text-[#bfc9d1] text-sm font-medium mb-1"
          >type</label
        >
        <select
          v-model="marketType"
          class="w-full rounded bg-[#22303c] border border-[#344452] text-white px-3 py-2 text-sm"
        >
          <option value="yesno">Yes / No</option>
          <option value="twoway">Two Choice</option>
          <option value="multiway">three way</option>
        </select>
      </div>
      <!-- Multiway outcome input -->
      <div v-if="marketType === 'multiway'">
        <label class="block text-[#bfc9d1] text-sm font-medium mb-1"
          >outcome</label
        >
        <div
          v-for="(outcome, idx) in multiwayOutcomes"
          :key="idx"
          class="flex gap-2 mb-2"
        >
          <input
            v-model="outcome.name"
            placeholder="Outcome name"
            class="flex-1 rounded bg-[#22303c] border border-[#344452] text-white px-2 py-1 text-sm"
          />
          <input
            v-model.number="outcome.percent"
            type="number"
            min="0"
            max="100"
            placeholder="%"
            class="w-16 rounded bg-[#22303c] border border-[#344452] text-white px-2 py-1 text-sm"
          />
          <button @click="removeOutcome(idx)" class="text-red-400">✕</button>
        </div>
        <button @click="addOutcome" class="text-xs text-[#7f5af0] mt-1">
          + add
        </button>
      </div>
      <div>
        <label class="block text-[#bfc9d1] text-sm font-medium mb-1">mkt</label>
        <input
          v-model="marketTitle"
          type="text"
          class="w-full rounded bg-[#22303c] border border-[#344452] text-white px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#7f5af0]"
          placeholder="Enter your market question..."
        />
      </div>
      <div v-if="marketType === 'twoway'">
        <label class="block text-[#bfc9d1] text-sm font-medium mb-1"
          >opt a</label
        >
        <input
          v-model="optionA"
          type="text"
          class="w-full rounded bg-[#22303c] border border-[#344452] text-white px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#27AE60]"
          placeholder="e.g. X thing"
        />
      </div>
      <div v-if="marketType === 'twoway'">
        <label class="block text-[#bfc9d1] text-sm font-medium mb-1"
          >opt b</label
        >
        <input
          v-model="optionB"
          type="text"
          class="w-full rounded bg-[#22303c] border border-[#344452] text-white px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#E64800]"
          placeholder="e.g. Y thing"
        />
      </div>

      <div>
        <label class="block text-[#bfc9d1] text-sm font-medium mb-1">odd</label>
        <input
          v-model.number="chance"
          type="number"
          min="0"
          max="100"
          class="w-full rounded bg-[#22303c] border border-[#344452] text-white px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#7f5af0]"
        />
      </div>
      <div>
        <label class="block text-[#bfc9d1] text-sm font-medium mb-1"
          >volume</label
        >
        <input
          v-model.number="volume"
          type="number"
          min="0"
          class="w-full rounded bg-[#22303c] border border-[#344452] text-white px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#7f5af0]"
          placeholder="Enter volume in dollars..."
        />
      </div>
      <div>
        <label class="block text-[#bfc9d1] text-sm font-medium mb-1"
          >series</label
        >
        <select
          v-model="seriesType"
          class="w-full rounded bg-[#22303c] border border-[#344452] text-white px-3 py-2 text-sm"
        >
          <option value="none">None</option>
          <option value="Daily">Daily</option>
          <option value="Weekly">Weekly</option>
          <option value="Monthly">Monthly</option>
        </select>
      </div>
    </div>
  </div>
</template>

<script setup>
import Footer from "./components/footer.vue";
import Gauge from "./components/gauge.vue";
import TwoWay from "./components/twoWay.vue";
import ThreeWay from "./components/threeWay.vue";

const marketType = ref("yesno");
const marketTitle = ref("Will X thing happen?");
const chance = ref(54);
const volume = ref(250000);

const optionA = ref("X thing");
const optionB = ref("Y thing");

const greenLabel = computed(() =>
  marketType.value === "twoway" ? `Buy ${optionA.value}` : "Buy Yes"
);
const redLabel = computed(() =>
  marketType.value === "twoway" ? `Buy ${optionB.value}` : "Buy No"
);

const multiwayOutcomes = ref([
  { name: "X will happen", percent: 52 },
  { name: "X and Y will happen", percent: 21 },
  { name: "Y will happen", percent: 16 },
]);
function addOutcome() {
  multiwayOutcomes.value.push({ name: "", percent: 0 });
}
function removeOutcome(idx) {
  multiwayOutcomes.value.splice(idx, 1);
}

watch(marketType, (val) => {
  if (val === "yesno") {
    optionA.value = "Yes";
    optionB.value = "No";
  } else if (val === "twoway") {
    optionA.value = "X thing";
    optionB.value = "Y thing";
  } else if (val === "multiway") {
    if (multiwayOutcomes.value.length === 0) {
      multiwayOutcomes.value.push({ name: "", percent: 0 });
    }
  }
});

function abbreviateVolume(val) {
  if (val >= 1_000_000_000)
    return `$${(val / 1_000_000_000).toFixed(
      val % 1_000_000_000 === 0 ? 0 : 2
    )}b`;
  if (val >= 1_000_000)
    return `$${(val / 1_000_000).toFixed(val % 1_000_000 === 0 ? 0 : 2)}m`;
  if (val >= 1_000)
    return `$${(val / 1_000).toFixed(val % 1_000 === 0 ? 0 : 2)}k`;
  return `$${val}`;
}

const formattedVolume = computed(() => abbreviateVolume(volume.value));

const greenChevron = computed(() =>
  marketType.value === "twoway" ? "bx:bxs-chevrons-up" : "bx:bxs-chevrons-up"
);
const redChevron = computed(() =>
  marketType.value === "twoway" ? "bx:bxs-chevrons-up" : "bx:bxs-chevrons-down"
);

const seriesType = ref("none");
</script>
