<template>
  <!-- Compact Mode (Soft Highlighted Design) -->
  <div v-if="compact" class="bg-gradient-to-br from-emerald-50/70 to-white rounded-xl shadow-md border border-emerald-500/35 px-4 py-3.5 transition-all hover:shadow-lg hover:border-emerald-500/50 relative overflow-hidden">
     <!-- Top brand glow bar -->
     <div class="absolute top-0 left-0 w-full h-[3px] bg-emerald-600"></div>

     <!-- Loading -->
     <div v-if="loading && !weather" class="flex items-center justify-center w-full py-2">
       <div class="animate-spin rounded-full h-5 w-5 border-b-2 border-emerald-600"></div>
     </div>

     <!-- Weather Data -->
     <template v-else-if="weather">
        <div class="flex items-center justify-between gap-4">
          <div class="flex items-center space-x-2.5">
            <!-- Dynamic Inline Weather Conditions SVGs -->
            <div class="p-1.5 bg-emerald-50 rounded-lg text-emerald-600 shrink-0">
              <svg v-if="weather.conditions === 'clear'" class="w-6 h-6 text-amber-500 animate-pulse" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364-6.364l-.707.707M6.343 17.657l-.707.707m0-12.728l.707.707m12.728 12.728l.707-.707M12 8a4 4 0 100 8 4 4 0 000-8z" />
              </svg>
              <svg v-else-if="weather.conditions === 'cloudy'" class="w-6 h-6 text-slate-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M3 15a4 4 0 004 4h9a5 5 0 10-.1-9.999 5.002 5.002 0 10-9.78 2.096A4.001 4.001 0 003 15z" />
              </svg>
              <svg v-else-if="weather.conditions === 'rainy'" class="w-6 h-6 text-emerald-600" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M19.4 15a1.65 1.65 0 00.33 1.82l.06.06a2 2 0 11-2.83 2.83l-.06-.06a1.65 1.65 0 00-1.82-.33 1.65 1.65 0 00-1 1.51V21a2 2 0 11-4 0v-.09A1.65 1.65 0 009 19.4a1.65 1.65 0 00-1.82.33l-.06.06a2 2 0 11-2.83-2.83l.06-.06a1.65 1.65 0 00.33-1.82 1.65 1.65 0 00-1.51-1H3a2 2 0 110-4h.09A1.65 1.65 0 004.6 9a1.65 1.65 0 00-.33-1.82l-.06-.06a2 2 0 112.83-2.83l.06.06a1.65 1.65 0 001.82.33H9a1.65 1.65 0 001-1.51V3a2 2 0 114 0v.09a1.65 1.65 0 001 1.51 1.65 1.65 0 001.82-.33l.06-.06a2 2 0 112.83 2.83l-.06.06a1.65 1.65 0 00-.33 1.82V9a1.65 1.65 0 001.51 1H21a2 2 0 110 4h-.09a1.65 1.65 0 00-1.51 1z" />
              </svg>
              <svg v-else class="w-6 h-6 text-slate-600" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364-6.364l-.707.707M6.343 17.657l-.707.707m0-12.728l.707.707m12.728 12.728l.707-.707M12 8a4 4 0 100 8 4 4 0 000-8z" />
              </svg>
            </div>
            <div>
              <span class="text-xl font-extrabold text-gray-900">{{ Math.round(weather.temperature) }}°C</span>
              <span class="text-xs font-bold text-gray-500 ml-2 capitalize tracking-wide">{{ weather.conditions }}</span>
            </div>
          </div>
          <div class="flex items-center space-x-3.5 text-xs text-gray-500 font-bold">
            <!-- Humidity -->
            <div class="flex items-center" title="Humidity">
              <svg class="w-3.5 h-3.5 text-emerald-500 mr-1" fill="currentColor" viewBox="0 0 24 24">
                <path d="M12 2.69l5.66 5.66a8 8 0 1 1-11.31 0z"/>
              </svg>
              {{ weather.humidity }}%
            </div>
            <!-- Wind -->
            <div class="flex items-center" title="Wind Speed">
              <svg class="w-3.5 h-3.5 text-emerald-600 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M20 10H4m12-4H4m16 8H4" />
              </svg>
              {{ weather.wind_speed }} km/h
            </div>
          </div>
        </div>

        <!-- Farming Advice -->
        <div class="mt-2.5 p-2 bg-emerald-50 rounded border border-emerald-100 flex items-start gap-1.5 text-xs">
          <svg class="w-4 h-4 text-emerald-600 shrink-0 mt-0.5" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z" />
          </svg>
          <div :class="isFavorableForFarming ? 'text-emerald-700 font-bold' : 'text-rose-700 font-bold'">
            {{ getFarmingAdvice() }}
          </div>
        </div>
     </template>
     <!-- No Data -->
     <div v-else class="text-xs text-gray-500 text-center w-full py-2">
       No weather records available
     </div>
  </div>

  <!-- Full Mode (Stretches full width, highly detailed, beautifully highlighted) -->
  <div v-else class="bg-gradient-to-br from-emerald-50/20 to-white rounded-xl shadow-md border border-emerald-500/35 p-6 flex flex-col relative overflow-hidden">
     <!-- Top brand glow bar -->
     <div class="absolute top-0 left-0 w-full h-[4px] bg-emerald-600"></div>

     <div class="flex items-center justify-between mb-6">
      <div>
        <h3 class="text-lg font-bold text-gray-900">Today's Detailed Field Weather</h3>
        <p v-if="weather && isDataStale" class="text-xs text-rose-600 mt-1 font-bold">
          Updates delayed. Displayed data might be stale.
        </p>
      </div>
      <div class="flex items-center space-x-2">
        <span v-if="autoRefreshActive" class="text-xs text-gray-400 font-bold">
          Refresh in: {{ timeUntilRefresh }}
        </span>
        <button
          @click="refreshWeather"
          :disabled="loading"
          class="p-2 text-gray-400 hover:text-emerald-600 transition-colors"
          :title="loading ? 'Refreshing...' : 'Refresh weather data'"
        >
          <svg 
            :class="['h-5 w-5', { 'animate-spin': loading }]" 
            fill="none" 
            stroke="currentColor" 
            viewBox="0 0 24 24"
          >
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15" />
          </svg>
        </button>
      </div>
    </div>

    <!-- Loading State -->
    <div v-if="loading && !weather" class="flex items-center justify-center py-8 flex-1">
      <div class="animate-spin rounded-full h-8 w-8 border-[3px] border-emerald-100 border-t-emerald-600 mx-auto"></div>
    </div>

    <!-- Weather Data -->
    <div v-else-if="weather" class="flex-1 flex flex-col">
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 items-stretch">
        <!-- Main Temperature / Condition -->
        <div class="flex items-center space-x-4 bg-emerald-50/50 p-4 rounded-xl border border-emerald-100/50 col-span-1">
          <div class="p-3 bg-emerald-50 rounded-lg text-emerald-600 shrink-0">
            <svg v-if="weather.conditions === 'clear'" class="w-10 h-10 text-amber-500 animate-pulse" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="2">
              <path stroke-linecap="round" stroke-linejoin="round" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364-6.364l-.707.707M6.343 17.657l-.707.707m0-12.728l.707.707m12.728 12.728l.707-.707M12 8a4 4 0 100 8 4 4 0 000-8z" />
            </svg>
            <svg v-else-if="weather.conditions === 'cloudy'" class="w-10 h-10 text-slate-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="2">
              <path stroke-linecap="round" stroke-linejoin="round" d="M3 15a4 4 0 004 4h9a5 5 0 10-.1-9.999 5.002 5.002 0 10-9.78 2.096A4.001 4.001 0 003 15z" />
            </svg>
            <svg v-else-if="weather.conditions === 'rainy'" class="w-10 h-10 text-emerald-600" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="2">
              <path stroke-linecap="round" stroke-linejoin="round" d="M19.4 15a1.65 1.65 0 00.33 1.82l.06.06a2 2 0 11-2.83 2.83l-.06-.06a1.65 1.65 0 00-1.82-.33 1.65 1.65 0 00-1 1.51V21a2 2 0 11-4 0v-.09A1.65 1.65 0 009 19.4a1.65 1.65 0 00-1.82.33l-.06.06a2 2 0 11-2.83-2.83l.06-.06a1.65 1.65 0 00.33-1.82 1.65 1.65 0 00-1.51-1H3a2 2 0 110-4h.09A1.65 1.65 0 004.6 9a1.65 1.65 0 00-.33-1.82l-.06-.06a2 2 0 112.83-2.83l.06.06a1.65 1.65 0 001.82.33H9a1.65 1.65 0 001-1.51V3a2 2 0 114 0v.09a1.65 1.65 0 001 1.51 1.65 1.65 0 001.82-.33l.06-.06a2 2 0 112.83 2.83l-.06.06a1.65 1.65 0 00-.33 1.82V9a1.65 1.65 0 001.51 1H21a2 2 0 110 4h-.09a1.65 1.65 0 00-1.51 1z" />
            </svg>
            <svg v-else class="w-10 h-10 text-slate-600" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="2">
              <path stroke-linecap="round" stroke-linejoin="round" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364-6.364l-.707.707M6.343 17.657l-.707.707m0-12.728l.707.707m12.728 12.728l.707-.707M12 8a4 4 0 100 8 4 4 0 000-8z" />
            </svg>
          </div>
          <div>
            <div class="text-4xl font-black text-gray-900 tracking-tight">
              {{ Math.round(weather.temperature) }}°C
            </div>
            <div class="text-sm font-bold text-gray-500 capitalize tracking-wide mt-0.5">
              {{ weather.conditions }} Conditions
            </div>
          </div>
        </div>

        <!-- Weather Metrics Grid -->
        <div class="grid grid-cols-3 gap-4 col-span-1 lg:col-span-2 items-center">
          <div class="text-center p-3.5 bg-gray-50 rounded-xl border border-gray-100">
            <div class="text-2xl font-black text-emerald-600">
              {{ weather.humidity }}%
            </div>
            <div class="text-[10px] text-gray-400 font-bold uppercase tracking-wider mt-1">Humidity</div>
          </div>
          <div class="text-center p-3.5 bg-gray-50 rounded-xl border border-gray-100">
            <div class="text-2xl font-black text-gray-800">
              {{ weather.wind_speed }} km/h
            </div>
            <div class="text-[10px] text-gray-400 font-bold uppercase tracking-wider mt-1">Wind Speed</div>
          </div>
          <div class="text-center p-3.5 bg-gray-50 rounded-xl border border-gray-100">
            <div class="text-2xl font-black text-amber-500">
              {{ Math.round(weather.temperature * 9/5 + 32) }}°F
            </div>
            <div class="text-[10px] text-gray-400 font-bold uppercase tracking-wider mt-1">Fahrenheit</div>
          </div>
        </div>
      </div>

      <!-- Farming Conditions Card -->
      <div class="bg-emerald-50/50 rounded-xl p-4.5 mt-6 border border-emerald-100 flex items-start gap-3">
        <svg class="w-5 h-5 text-emerald-600 shrink-0 mt-0.5" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="2">
          <path stroke-linecap="round" stroke-linejoin="round" d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z" />
        </svg>
        <div>
          <h4 class="text-sm font-bold text-gray-900 mb-1 flex items-center gap-2">
            <div :class="['w-2.5 h-2.5 rounded-full shrink-0', isFavorableForFarming ? 'bg-emerald-500' : 'bg-rose-500']"></div>
            Agronomic Field Recommendation
          </h4>
          <p class="text-xs text-gray-700 font-bold leading-relaxed">
            {{ getFarmingAdvice() }}
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue';
import { useWeatherStore } from '@/stores/weather';
import { useFarmStore } from '@/stores/farm';

const props = defineProps({
  farmId: {
    type: [String, Number],
    required: true
  },
  compact: {
    type: Boolean,
    default: false
  }
});

const weatherStore = useWeatherStore();
const farmStore = useFarmStore();

const loading = ref(false);
const error = ref('');
const autoRefreshInterval = ref(null);
const refreshCountdown = ref(600); // 10 minutes
const autoRefreshActive = ref(true);

const weather = computed(() => weatherStore.currentWeather);
const alerts = computed(() => weatherStore.alerts || []);

const isDataStale = computed(() => {
  if (!weather.value || !weather.value.recorded_at) return false;
  const recordedAt = new Date(weather.value.recorded_at);
  const now = new Date();
  return ((now - recordedAt) / (1000 * 60)) > 30;
});

const timeUntilRefresh = computed(() => {
  const minutes = Math.floor(refreshCountdown.value / 60);
  const seconds = refreshCountdown.value % 60;
  return `${minutes}:${seconds.toString().padStart(2, '0')}`;
});

const isFavorableForFarming = computed(() => {
  if (!weather.value) return false;
  return weather.value.temperature >= 10 && 
         weather.value.temperature <= 35 &&
         weather.value.humidity >= 30 &&
         weather.value.humidity <= 80 &&
         weather.value.wind_speed < 20;
});

const refreshWeather = async () => {
  if (!props.farmId) {
    error.value = 'No farm ID provided';
    return;
  }
  loading.value = true;
  error.value = '';
  try {
    const results = await Promise.allSettled([
      weatherStore.fetchCurrentWeather(props.farmId),
      weatherStore.fetchWeatherAlerts(props.farmId)
    ]);
    const failures = results.filter(result => result.status === 'rejected');
    if (failures.length === results.length) {
      error.value = 'Failed to load fresh weather logs';
    } else {
      refreshCountdown.value = 600;
    }
  } catch (err) {
    error.value = err.message || 'Error compiling logs';
  } finally {
    loading.value = false;
  }
};

const startAutoRefresh = () => {
  autoRefreshInterval.value = setInterval(() => {
    if (refreshCountdown.value > 0) {
      refreshCountdown.value--;
    } else {
      refreshWeather();
      refreshCountdown.value = 600;
    }
  }, 1000);
};

const getFarmingAdvice = () => {
  if (!weather.value) return '';
  const temp = weather.value.temperature;
  const humidity = weather.value.humidity;
  const wind = weather.value.wind_speed;
  
  if (temp < 10) return 'Low temperature may retard rice growth. Delay transplanting and monitor seedlings for cold injury.';
  if (temp > 35) return 'High temperature stress. Boost water levels to 5-10cm to cool the soil and reduce spikelet sterility.';
  if (humidity < 30) return 'Low humidity accelerates transpiration. Increase water irrigation frequency to prevent soil dryness.';
  if (humidity > 80) return 'High humidity increases blast and sheath blight disease risks. Suspend pesticide spraying and monitor closely.';
  if (wind > 20) return 'Strong winds may cause crop lodging. Ensure drainage channels are clear to prevent waterlogging.';
  
  return 'Ideal mild conditions today. Excellent window for fertilizer/pesticide application, weeding, or field preparation.';
};

const formatTime = (timestamp) => {
  return new Date(timestamp).toLocaleTimeString();
};

onMounted(() => {
  refreshWeather();
  startAutoRefresh();
});

onUnmounted(() => {
  if (autoRefreshInterval.value) {
    clearInterval(autoRefreshInterval.value);
  }
});
</script>

<style scoped>
/* Standard tabular layouts */
span, div {
  font-feature-settings: "tnum";
}
</style>
