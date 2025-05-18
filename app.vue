<template>
  <div class="flex flex-row gap-6 w-full max-w-2xl mx-auto mt-10">
    <!-- Unified Card for all market types -->
    <div
      class="bg-[#2C3F4F] rounded-lg shadow-lg p-3 flex flex-col gap-3 h-[180px] w-[340px] border border-[#344452] justify-between"
    >
      <div
        class="flex flex-row items-start gap-3"
        v-if="marketType !== 'dynamic'"
      >
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
          v-if="marketType !== 'dynamic'"
        >
          <SemiGauge :value="chance" :title="optionA" />
        </div>
      </div>

      <div v-else class="flex flex-row items-center gap-3 flex-1">
        <div
          class="w-10 h-10 bg-gradient-to-br from-[#7f5af0] via-[#7f5af0] to-[#2cb67d] rounded-sm flex-shrink-0"
        ></div>
        <div class="text-white font-semibold text-[0.875rem] leading-tight">
          {{ marketTitle }}
        </div>
      </div>
      <!-- Outcomes List for Dynamic -->
      <div v-if="marketType === 'dynamic'">
        <div
          v-for="(item, idx) in dynamicOutcomes"
          :key="idx"
          class="flex items-center"
        >
          <div class="flex items-center w-full">
            <span class="text-white text-[0.95em] flex-1 truncate mb-1">{{
              item.name
            }}</span>
            <span class="text-white text-[0.85em] font-bold w-[38px] text-right"
              >{{ item.chance }}%</span
            >
            <button
              class="ml-2 px-3 py-1 rounded-[4px] font-semibold text-[#27AE60] bg-[#36615d] hover:bg-[#27AE60] hover:text-white text-xs transition-colors w-[37.2px] flex items-center justify-center"
            >
              Yes
            </button>
            <button
              class="ml-1 px-3 py-1 rounded-[4px] font-semibold text-[#E64800] bg-[#524350] hover:bg-[#b93a03] hover:text-white text-xs transition-colors w-[32.65px] flex items-center justify-center"
            >
              No
            </button>
          </div>
        </div>
      </div>
      <!-- Trade Buttons for Yesno/Twoway -->
      <div v-else class="flex flex-col gap-3 mt-auto">
        <div class="flex gap-3">
          <button
            class="group flex-1 py-2 rounded-[4px] font-semibold text-[#27AE60] text-[14px] bg-[#36615d] hover:bg-[#27AE60] hover:text-white transition-colors flex items-center justify-center gap-1"
          >
            {{ greenLabel }}
            <Icon
              :name="greenChevron"
              class="w-[18px] h-[18px] text-[#27AE60] flex-shrink-0 transition-colors group-hover:text-white group-hover:animate-pulse-opacity"
            />
          </button>
          <button
            class="group flex-1 py-2 rounded-[4px] font-semibold text-[#E64800] text-[14px] bg-[#524350] hover:bg-[#b93a03] hover:text-white transition-colors flex items-center justify-center gap-1"
          >
            {{ redLabel }}
            <Icon
              :name="redChevron"
              class="w-[18px] h-[18px] text-[#E64800] flex-shrink-0 transition-colors group-hover:text-white group-hover:animate-pulse-opacity"
            />
          </button>
        </div>
      </div>
      <!-- Bottom Info (Volume, Series, Icons) -->
      <div class="flex items-center mt-2">
        <span
          class="text-[#858D92] text-[12px] flex items-center gap-1 w-full h-[18px]"
        >
          {{ formattedVolume }} Vol.
          <svg
            v-if="seriesType !== 'none'"
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
          <span
            v-if="seriesType !== 'none'"
            class="text-[#858D92] text-[12px]"
            >{{ seriesType }}</span
          >
        </span>
        <div class="flex gap-2 items-center">
          <Icon name="lucide:gift" class="text-[#858d92]" size="16px" />
          <Icon name="lucide:bookmark" class="text-[#858d92]" size="16px" />
        </div>
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
          <option value="dynamic">Dynamic Outcomes</option>
        </select>
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
      <div v-if="marketType === 'dynamic'">
        <label class="block text-[#bfc9d1] text-sm font-medium mb-1"
          >Dynamic Outcomes</label
        >
        <div
          v-for="(item, idx) in dynamicOutcomes"
          :key="idx"
          class="flex gap-2 mb-2"
        >
          <input
            v-model="item.name"
            type="text"
            class="flex-1 rounded bg-[#22303c] border border-[#344452] text-white px-2 py-1 text-xs"
            placeholder="Outcome name"
          />
          <input
            v-model.number="item.chance"
            type="number"
            min="0"
            max="100"
            class="w-16 rounded bg-[#22303c] border border-[#344452] text-white px-2 py-1 text-xs"
            placeholder="%"
          />
          <button
            @click="removeOutcome(idx)"
            class="text-[#E64800] text-xs px-2"
          >
            Remove
          </button>
        </div>
        <button
          @click="addOutcome"
          class="mt-1 px-3 py-1 rounded bg-[#22303c] border border-[#344452] text-[#27AE60] text-xs"
        >
          Add Outcome
        </button>
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
import { ref, computed, watch } from "vue";
import SemiGauge from "./components/SemiGauge.vue";

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

watch(marketType, (val) => {
  if (val === "yesno") {
    optionA.value = "Yes";
    optionB.value = "No";
  } else if (val === "twoway") {
    optionA.value = "X thing";
    optionB.value = "Y thing";
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

const dynamicOutcomes = ref([
  { name: "George Simion", chance: 54 },
  { name: "Nicușor Dan", chance: 47 },
]);
function addOutcome() {
  dynamicOutcomes.value.push({ name: "", chance: 0 });
}
function removeOutcome(idx) {
  dynamicOutcomes.value.splice(idx, 1);
}
</script>
