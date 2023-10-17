<script setup>
import { ref, reactive, onMounted } from "vue";

const liveTime = ref("");

const updateTime = () => {
  const now = new Date();
  const hours = String(now.getHours()).padStart(2, "0");
  const minutes = String(now.getMinutes()).padStart(2, "0");
  const seconds = String(now.getSeconds()).padStart(2, "0");
  liveTime.value = `${hours}:${minutes}:${seconds}`;
};

const timeLeft = ref({
  days: 0,
  hours: 0,
  minutes: 0,
  seconds: 0,
});

function updateTimeLeft() {
  const now = new Date();
  const targetDate = new Date();

  // Find next Friday
  targetDate.setDate(now.getDate() + ((5 - now.getDay() + 7) % 7));
  targetDate.setHours(17, 0, 0, 0); // 17:00 local time

  const timeDifference = targetDate - now;

  timeLeft.value = {
    days: Math.floor(timeDifference / (1000 * 60 * 60 * 24)),
    hours: Math.floor(
      (timeDifference % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60)
    ),
    minutes: Math.floor((timeDifference % (1000 * 60 * 60)) / (1000 * 60)),
    seconds: Math.floor((timeDifference % (1000 * 60)) / 1000),
  };
}

const progress = ref(0);

function updateProgress() {
  const now = new Date();
  const weekStart = new Date();
  weekStart.setHours(0, 0, 0, 0); // Monday 00:00
  const weekendStart = new Date();
  weekendStart.setDate(
    weekStart.getDate() + ((5 - weekStart.getDay() + 7) % 7)
  );
  weekendStart.setHours(17, 0, 0, 0); // Friday 17:00

  const totalWeekMilliseconds = weekendStart - weekStart;
  const currentMilliseconds = now - weekStart;

  // Calculate progress as a percentage
  progress.value = (currentMilliseconds / totalWeekMilliseconds) * 100;
}

onMounted(() => {
  // Update time initially and then every second

  // Initialize the countdown timer immediately when the component is created
  updateTime();
  updateTimeLeft();
  updateProgress();
  // Update the countdown timer every second
  setInterval(() => {
    updateTime();
    updateTimeLeft();
    updateProgress();
  }, 1000);
});
</script>

<template>
  <!-- Hero -->
  <div class="overflow-hidden">
    <div class="max-w-[85rem] mx-auto px-4 sm:px-6 lg:px-8 py-20">
      <div class="text-center">
        <h2
          class="text-3xl text-gray-800 font-bold sm:text-5xl lg:text-6xl lg:leading-tight dark:text-gray-200 drop-shadow"
        >
          <span class="text-blue-500">Nädalavahetuseni jäänud</span>
        </h2>
      </div>
      <div class="relative mx-auto max-w-4xl grid space-y-5 sm:space-y-10">
        <!-- Title -->
        <div class="text-center drop-shadow-md">
          <h1
            class="mt-1 sm:mt-3 text-8xl md:text-9xl font-bold bg-clip-text bg-gradient-to-tr from-blue-600 to-purple-400 text-transparent"
          >
            {{ timeLeft.days }} päeva
          </h1>
          <h1
            class="mt-1 sm:mt-3 text-7xl md:text-8xl font-bold bg-clip-text bg-gradient-to-tr from-blue-600 to-purple-400 text-transparent"
          >
            {{ timeLeft.hours }} tundi
          </h1>
          <h1
            class="mt-1 sm:mt-3 text-5xl md:text-6xl font-bold bg-clip-text bg-gradient-to-tr f from-blue-600 to-purple-400 text-transparent"
          >
            {{ timeLeft.minutes }} minutit
          </h1>
          <h1
            class="mt-1 sm:mt-3 text-3xl md:text-4xl font-bold bg-clip-text bg-gradient-to-tr from-blue-600 to-purple-400 text-transparent"
          >
            {{ timeLeft.seconds }} sekundit
          </h1>
        </div>
        <!-- End Title -->
      </div>
    </div>

    <div class="max-w-[85rem] mx-auto px-4 sm:px-6 lg:px-8">
      <div
        class="flex w-full h-1.5 bg-gray-200 rounded-full overflow-hidden dark:bg-gray-700"
      >
        <div
          class="flex flex-col justify-center overflow-hidden bg-blue-500"
          role="progressbar"
          style="width: 25%"
          :aria-valuenow="progress"
          aria-valuemin="0"
          aria-valuemax="100"
        ></div>
      </div>
    </div>
  </div>
  <!-- End Hero -->

  <!-- ========== FOOTER ========== -->
  <footer class="absolute bottom-0 inset-x-0 text-center py-5">
    <div class="max-w-[85rem] mx-auto px-4 sm:px-6 lg:px-8">
      <p
        class="text-sm lg:text-1xl text-gray-800 font-bold lg:leading-tight dark:text-gray-200"
      >
        <span class="text-blue-500">Kell praegu</span>
      </p>
      <h2
        class="text-sm lg:text-3xl text-gray-800 font-bold lg:leading-tight dark:text-gray-200"
      >
        <span
          class="bg-clip-text bg-gradient-to-tr from-blue-600 to-purple-400 text-transparent"
          >{{ liveTime }}</span
        >
      </h2>
    </div>
  </footer>
  <!-- ========== END FOOTER ========== -->
</template>

<style scoped></style>
