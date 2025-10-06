<template>
  <div class="flex flex-col items-center w-full">
    <header class="w-full p-4 lg:p-6 flex flex-col items-center">
      <div class="w-full flex items-center justify-center mb-6 lg:mb-8 px-4 lg:px-8">
        <div class="flex items-center gap-0 mt-6 lg:mt-10">
          <img src="~/assets/svg/poly.svg" alt="Logo" class="h-12 lg:h-15" />
          <span class="text-white text-[24px] lg:text-[31px] font-bold">generator</span>
          <!-- fuck off okay -->
        </div>
      </div>
      <div class="text-[#bfc9d1] text-[0.875rem] font-medium text-center px-4">
        Love gambling? Well make it known! Create markets for anything you want and share them with the world.
      </div>
    </header>
    <div class="flex flex-col lg:flex-row gap-6 w-full max-w-4xl mx-auto mt-2 px-4">
      <div
        class="bg-[#2C3F4F] rounded-lg shadow-lg p-3 flex flex-col gap-3 h-[180px] w-full lg:w-[340px] border border-[#3d5266] outline-[#d1d5db50%] lg:flex-shrink-0"
      >
        <div class="flex flex-row items-start gap-3">
          <div class="flex flex-row items-center gap-3 flex-1">
            <div
              class="w-10 h-10 bg-gradient-to-br from-[#7f5af0] via-[#3e56b2] to-[#2cb67d] rounded-sm flex-shrink-0 relative overflow-hidden"
              @click="$refs.imageInput.click()"
              style="cursor: pointer"
            >
              <img
                v-if="uploaded"
                :src="uploaded"
                class="w-full h-full object-cover absolute inset-0"
                alt="Market image"
              />
              <input
                type="file"
                ref="imageInput"
                @change="onImageSelected"
                accept="image/*"
                class="hidden"
              />
            </div>
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
        <div class="flex flex-col justify-between flex-1">
          <TwoWay
            v-if="marketType !== 'multiway'"
            :yesText="yesText"
            :noText="noText"
          />
          <ThreeWay
            v-if="marketType === 'multiway'"
            :outcomes="multiwayOutcomes"
          />
          <Footer :vol="vol" :repeat="repeat" />
        </div>
      </div>

      <div class="flex flex-col gap-6 flex-1 min-w-0">
        <div>
          <label class="block text-[#bfc9d1] text-sm font-medium mb-1"
            >Market Icon</label
          >
          <div
            @click="$refs.imageInput.click()"
            @dragover.prevent="isDragging = true"
            @dragleave.prevent="isDragging = false"
            @drop.prevent="handleDrop"
            @paste.prevent="handlePaste"
            @focus="canPaste = true"
            @blur="canPaste = false"
            :class="[
              'w-full h-24 lg:h-32 rounded bg-[#22303c] border transition-colors flex items-center justify-center cursor-pointer relative',
              isDragging
                ? 'border-[#7f5af0] border-2'
                : canPaste
                ? 'border-[#7f5af0] border-dashed'
                : 'border-[#344452] hover:border-[#7f5af0]',
            ]"
            tabindex="0"
          >
            <div
              v-if="!uploaded"
              class="text-[#bfc9d1] text-sm text-center"
            >
              <span class="block">Drop/paste/click to upload image here</span>
              <span class="block text-xs text-[#8b95a1] mt-2">
                For best results, use a square image
              </span>
            </div>
            <img
              v-else
              :src="uploaded"
              class="h-full w-full object-cover rounded"
              alt="Preview"
            />
            <button
              v-if="uploaded"
              @click.stop="removeImage"
              title="Remove image"
              class="absolute top-1 right-1 w-5 h-5 rounded-full flex items-center justify-center text-[#e64800] text-xs font-bold transition-colors cursor-pointer"
            >
              <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 24 24"><path fill="currentColor" d="m12 13.4l-4.9 4.9q-.275.275-.7.275t-.7-.275t-.275-.7t.275-.7l4.9-4.9l-4.9-4.9q-.275-.275-.275-.7t.275-.7t.7-.275t.7.275l4.9 4.9l4.9-4.9q.275-.275.7-.275t.7.275t.275.7t-.275.7L13.4 12l4.9 4.9q.275.275.275.7t-.275.7t-.7.275t-.7-.275z"/></svg>
            </button>
          </div>
        </div>
        <div v-if="marketType === 'multiway'">
          <label class="block text-[#bfc9d1] text-sm font-medium mb-1 cursor-pointer"
            >Possible Outcomes</label
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
            <button @click="removeOutcome(idx)" class="text-red-400 px-2">✕</button>
          </div>
          <button @click="addOutcome" class="text-xs text-[#7f5af0] mt-1">
            + Add Outcome
          </button>
        </div>
        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
          <div>
            <label class="block text-[#bfc9d1] text-sm font-medium mb-1"
              >Market Title</label
            >
            <input
              v-model="marketTitle"
              type="text"
              aria-label="Market Title"
              class="w-full rounded bg-[#22303c] border border-[#344452] text-white px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#7f5af0]"
              placeholder="Enter your market question..."
            />
          </div>
          <div>
            <label class="block text-[#bfc9d1] text-sm font-medium mb-1">Market Type</label>
            <select
              v-model="marketType"
              aria-label="Market Type"
              class="w-full rounded bg-[#22303c] border border-[#344452] text-white px-3 py-2 text-sm"
            >
              <option value="yesno">Yes or No</option>
              <option value="twoway">Two Choice</option>
              <option value="multiway">Multiway</option>
            </select>
          </div>
        </div>
        <div v-if="marketType === 'twoway'" class="grid grid-cols-1 sm:grid-cols-2 gap-4">
          <div>
            <label class="block text-[#bfc9d1] text-sm font-medium mb-1"
              >Outcome A</label
            >
            <input
              v-model="optionA"
              type="text"
              aria-label="Outcome A"
              class="w-full rounded bg-[#22303c] border border-[#344452] text-white px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#27AE60]"
              placeholder="e.g. X thing"
            />
          </div>
          <div>
            <label class="block text-[#bfc9d1] text-sm font-medium mb-1"
              >Outcome B</label
            >
            <input
              v-model="optionB"
              type="text"
              aria-label="Outcome B"
              class="w-full rounded bg-[#22303c] border border-[#344452] text-white px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#E64800]"
              placeholder="e.g. Y thing"
            />
          </div>
        </div>

        <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
          <div>
            <label class="block text-[#bfc9d1] text-sm font-medium mb-1"
              >Current Odds (%)</label
            >
            <input
              v-model.number="chance"
              aria-label="Current Odds"
              type="number"
              min="0"
              max="100"
              class="w-full rounded bg-[#22303c] border border-[#344452] text-white px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#7f5af0]"
            />
          </div>
          <div>
            <label class="block text-[#bfc9d1] text-sm font-medium mb-1"
              >Trading Volume ($)</label
            >
            <input
              v-model.number="volume"
              aria-label="Trading Volume"
              type="number"
              min="0"
              class="w-full rounded bg-[#22303c] border border-[#344452] text-white px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#7f5af0]"
              placeholder="Enter volume..."
            />
          </div>
          <div>
            <label class="block text-[#bfc9d1] text-sm font-medium mb-1"
              >Reoccurring Market</label
            >
            <select
              v-model="repeat"
              class="w-full rounded bg-[#22303c] border border-[#344452] text-white px-3 py-2 text-sm"
              aria-label="Reoccurring Market"
            >
              <option value="none">Single time</option>
              <option value="Daily">Daily</option>
              <option value="Weekly">Weekly</option>
              <option value="Monthly">Monthly</option>
            </select>
          </div>
        </div>
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

const optionA = ref("chance");
const optionB = ref("Y thing");

const yesText = computed(() =>
  marketType.value === "twoway" ? `${optionA.value}` : "Yes"
);
const noText = computed(() =>
  marketType.value === "twoway" ? `${optionB.value}` : "No"
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

function avol(val) {
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

const vol = computed(() => avol(volume.value));

const repeat = ref("none");

const uploaded = ref(null);
const imageInput = ref(null);
const isDragging = ref(false);
const canPaste = ref(false);

function handleDrop(event) {
  isDragging.value = false;
  const file = event.dataTransfer.files[0];
  if (file && file.type.startsWith("image/")) {
    const reader = new FileReader();
    reader.onload = (e) => {
      uploaded.value = e.target.result;
    };
    reader.readAsDataURL(file);
  }
}

function onImageSelected(event) {
  const file = event.target.files[0];
  if (file && file.type.startsWith("image/")) {
    const reader = new FileReader();
    reader.onload = (e) => {
      uploaded.value = e.target.result;
    };
    reader.readAsDataURL(file);
  }
}

function removeImage() {
  uploaded.value = null;
  if (imageInput.value) {
    imageInput.value.value = '';
  }
}

function handlePaste(event) {
  const items = event.clipboardData?.items;
  if (!items) return;

  for (const item of items) {
    if (item.type.indexOf("image") === 0) {
      const file = item.getAsFile();
      if (file) {
        const reader = new FileReader();
        reader.onload = (e) => {
          uploaded.value = e.target.result;
        };
        reader.readAsDataURL(file);
        break;
      }
    }
  }
}

onMounted(() => {
  window.addEventListener("paste", (e) => {
    if (
      document.activeElement.tagName !== "INPUT" &&
      document.activeElement.tagName !== "TEXTAREA"
    ) {
      handlePaste(e);
      // why the fuck is it this hard to handle paste
    }
  });
});

onUnmounted(() => {
  window.removeEventListener("paste", handlePaste);
});
</script>
