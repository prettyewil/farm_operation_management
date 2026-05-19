<template>
  <div class="min-h-screen bg-[#f8fafc] font-sans text-gray-900">
    <div class="w-full mx-auto px-6 py-8">

      <!-- Header Section -->
      <div class="flex flex-col md:flex-row justify-between items-start md:items-center mb-8 gap-4">
        <div>
          <h1 class="text-3xl font-bold text-gray-800 tracking-tight">
            Data Analytics Hub
          </h1>
          <p class="text-gray-500 mt-1">Deep visual insights and predictive performance metrics</p>
        </div>

        <div class="flex flex-col sm:flex-row items-stretch sm:items-center gap-3 bg-white p-3 rounded-xl shadow-sm border border-gray-200 w-full md:w-auto">
          <div class="flex items-center gap-2">
            <label class="text-xs font-semibold text-gray-500 uppercase tracking-wider w-10 sm:w-auto">From</label>
            <input
              v-model="startDate"
              type="date"
              class="flex-1 sm:flex-none w-full sm:w-auto px-3 py-1.5 border border-gray-200 rounded-lg text-sm focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 outline-none transition-all font-medium"
            />
          </div>
          <div class="flex items-center gap-2">
            <label class="text-xs font-semibold text-gray-500 uppercase tracking-wider w-10 sm:w-auto">To</label>
            <input
              v-model="endDate"
              type="date"
              class="flex-1 sm:flex-none w-full sm:w-auto px-3 py-1.5 border border-gray-200 rounded-lg text-sm focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 outline-none transition-all font-medium"
            />
          </div>
          <button
            @click="handleUpdate"
            :disabled="isLoading"
            class="w-full sm:w-auto px-5 py-2 bg-emerald-600 text-white rounded-lg text-sm font-semibold hover:bg-emerald-700 active:bg-emerald-800 transition-all disabled:opacity-50 disabled:cursor-not-allowed shadow-sm flex justify-center items-center"
          >
            <span v-if="isLoading" class="flex items-center gap-2">
              <svg class="animate-spin h-4 w-4 text-white" fill="none" viewBox="0 0 24 24">
                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
              </svg>
              Updating...
            </span>
            <span v-else>Update</span>
          </button>
        </div>
      </div>

      <!-- Loading State -->
      <div v-if="isLoading && !analyticsData" class="flex items-center justify-center py-32">
        <div class="text-center">
          <div class="animate-spin rounded-full h-12 w-12 border-[3px] border-emerald-100 border-t-emerald-600 mx-auto"></div>
          <p class="mt-4 text-gray-500 font-medium tracking-wide animate-pulse">Gathering insights...</p>
        </div>
      </div>

      <!-- Main Analytics Tabs Container -->
      <div v-else-if="analyticsData" class="space-y-6">

        <!-- Sub-Navigation Menu Bar -->
        <div class="bg-white rounded-xl shadow-sm border border-gray-200 p-1 flex flex-wrap gap-1">
          <button
            v-for="tab in navigationTabs"
            :key="tab.id"
            @click="activeSubTab = tab.id"
            :class="[
              'flex items-center gap-2 px-5 py-2.5 text-sm font-semibold rounded-lg transition-all duration-200',
              activeSubTab === tab.id
                ? 'bg-emerald-600 text-white shadow-md'
                : 'text-gray-600 hover:text-gray-900 hover:bg-gray-50'
            ]"
          >
            <component :is="tab.icon" class="w-4 h-4 shrink-0" />
            {{ tab.name }}
          </button>
        </div>

        <div :key="activeSubTab" class="space-y-6">
          
          <!-- ── OVERVIEW TAB ── -->
          <div v-if="activeSubTab === 'overview'" class="space-y-6">
            
            <!-- Executive Summary Card -->
            <div
              v-if="analyticsData.executive_summary"
              class="bg-white rounded-xl shadow-sm border border-gray-200 overflow-hidden"
            >
              <div class="flex flex-col md:flex-row">
                <div
                  :class="[
                    'w-full md:w-1.5 h-2 md:h-auto',
                    analyticsData.executive_summary.tone === 'positive' ? 'bg-emerald-500' :
                    analyticsData.executive_summary.tone === 'concern' ? 'bg-amber-500' :
                    'bg-emerald-600'
                  ]"
                ></div>

                <div class="p-6 flex-1">
                  <div class="flex items-start gap-4">
                    <div
                      :class="[
                        'p-2.5 rounded-lg shrink-0 border',
                        analyticsData.executive_summary.tone === 'positive' ? 'bg-emerald-50 text-emerald-600 border-emerald-100' :
                        analyticsData.executive_summary.tone === 'concern' ? 'bg-amber-50 text-amber-600 border-amber-100' :
                        'bg-emerald-50 text-emerald-700 border-emerald-100'
                      ]"
                    >
                      <component :is="analyticsData.executive_summary.tone === 'positive' ? ArrowTrendingUpIcon : ChartBarIcon" class="w-6 h-6" />
                    </div>
                    <div>
                      <h2 class="text-base font-bold text-gray-900 mb-1">Executive Analysis Summary</h2>
                      <p class="text-gray-600 text-sm leading-relaxed max-w-5xl">
                        {{ analyticsData.executive_summary.text }}
                      </p>
                    </div>
                  </div>
                </div>
              </div>
            </div>

            <!-- Recommended Actions Grid -->
            <div v-if="analyticsData.action_suggestions?.length">
              <h2 class="text-lg font-bold text-gray-900 mb-4 px-1 flex items-center gap-2">
                <span class="w-1.5 h-5 bg-emerald-500 rounded-full"></span>
                Agronomic Recommendations
              </h2>
              <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
                <div
                  v-for="(suggestion, index) in analyticsData.action_suggestions"
                  :key="index"
                  @click="navigateTo(suggestion.action_url)"
                  class="group bg-white rounded-xl p-5 border border-gray-200 shadow-sm hover:shadow-md hover:border-emerald-300 transition-all cursor-pointer relative overflow-hidden flex flex-col justify-between"
                >
                  <div :class="[
                    'absolute top-0 left-0 w-full h-1',
                    suggestion.priority === 'high' || suggestion.priority === 'urgent' ? 'bg-rose-500' :
                    suggestion.priority === 'medium' ? 'bg-amber-500' :
                    'bg-emerald-500'
                  ]"></div>

                  <div>
                    <div class="flex justify-between items-start mb-3">
                      <span class="p-2 rounded-lg bg-emerald-50 text-emerald-600 group-hover:bg-emerald-600 group-hover:text-white transition-all">
                        <component :is="getIconComponent(suggestion.icon)" class="w-5 h-5 shrink-0" />
                      </span>
                      <span
                        :class="[
                          'text-[10px] font-extrabold uppercase tracking-wider px-2 py-0.5 rounded-full border',
                          suggestion.priority === 'high' || suggestion.priority === 'urgent' ? 'bg-rose-50 text-rose-700 border-rose-100' :
                          suggestion.priority === 'medium' ? 'bg-amber-50 text-amber-700 border-amber-100' :
                          'bg-emerald-50 text-emerald-700 border-emerald-100'
                        ]"
                      >
                        {{ suggestion.priority }}
                      </span>
                    </div>

                    <h3 class="text-sm font-bold text-gray-900 mb-1 line-clamp-1 capitalize">{{ suggestion.category }}</h3>
                    <p class="text-xs text-gray-500 mb-4 line-clamp-2 leading-relaxed">{{ suggestion.message }}</p>
                  </div>

                  <div class="flex items-center text-xs font-semibold text-emerald-600 group-hover:text-emerald-700 transition-colors">
                    {{ suggestion.action_label }}
                    <svg class="w-3 h-3 ml-1.5 transform group-hover:translate-x-1 transition-transform" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 8l4 4m0 0l-4 4m4-4H3"></path></svg>
                  </div>
                </div>
              </div>
            </div>

            <!-- Overview Section Grid -->
            <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
              <div class="bg-white rounded-xl p-5 shadow-sm border border-gray-200 flex items-center justify-between">
                <div>
                  <p class="text-xs font-bold text-gray-400 uppercase tracking-wider">Rice Fields</p>
                  <p class="text-xl font-extrabold text-gray-900 mt-1">{{ analyticsData.fields?.total_area ?? 0 }} ha</p>
                </div>
                <div class="text-right">
                  <p class="text-xs text-emerald-600 font-semibold bg-emerald-50 px-2 py-0.5 rounded-full border border-emerald-100 inline-block">{{ analyticsData.fields?.utilization_rate ?? 0 }}%</p>
                  <p class="text-[10px] text-gray-400 mt-1">Utilized</p>
                </div>
              </div>

              <div class="bg-white rounded-xl p-5 shadow-sm border border-gray-200 flex items-center justify-between">
                <div>
                  <p class="text-xs font-bold text-gray-400 uppercase tracking-wider">Seedbed</p>
                  <p class="text-xl font-extrabold text-gray-900 mt-1">{{ analyticsData.nursery?.active_batches ?? 0 }} batches</p>
                </div>
                <div class="text-right">
                  <p class="text-xs text-emerald-600 font-semibold bg-emerald-50 px-2 py-0.5 rounded-full border border-emerald-100 inline-block">{{ analyticsData.nursery?.ready_for_transplant ?? 0 }}</p>
                  <p class="text-[10px] text-gray-400 mt-1">Ready</p>
                </div>
              </div>

              <div class="bg-white rounded-xl p-5 shadow-sm border border-gray-200 flex items-center justify-between">
                <div>
                  <p class="text-xs font-bold text-gray-400 uppercase tracking-wider">Pest Status</p>
                  <p class="text-xl font-extrabold text-gray-900 mt-1">{{ analyticsData.pests?.active_incidents ?? 0 }} active</p>
                </div>
                <div class="text-right">
                  <p :class="['text-xs font-semibold px-2 py-0.5 rounded-full border inline-block', (analyticsData.pests?.active_incidents ?? 0) > 0 ? 'bg-rose-50 text-rose-700 border-rose-100' : 'bg-emerald-50 text-emerald-700 border-emerald-100']">
                    {{ analyticsData.pests?.total_incidents ?? 0 }} total
                  </p>
                </div>
              </div>

              <div class="bg-white rounded-xl p-5 shadow-sm border border-gray-200 flex items-center justify-between">
                <div>
                  <p class="text-xs font-bold text-gray-400 uppercase tracking-wider">Task Queue</p>
                  <p class="text-xl font-extrabold text-gray-900 mt-1">{{ analyticsData.tasks?.total_tasks - analyticsData.tasks?.completed_tasks }} open</p>
                </div>
                <div class="text-right">
                  <p :class="['text-xs font-semibold px-2 py-0.5 rounded-full border inline-block', (analyticsData.tasks?.overdue_tasks ?? 0) > 0 ? 'bg-rose-50 text-rose-700 border-rose-100' : 'bg-gray-50 text-gray-600 border-gray-150']">
                    {{ analyticsData.tasks?.overdue_tasks ?? 0 }} overdue
                  </p>
                </div>
              </div>
            </div>
            
            <!-- Quick Financial Summary Bar -->
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
              <div class="bg-white rounded-xl p-6 shadow-sm border border-gray-200 flex items-center justify-between">
                <div class="flex items-center gap-4">
                  <div class="p-3 bg-emerald-50 text-emerald-600 rounded-xl border border-emerald-100">
                    <CurrencyDollarIcon class="w-6 h-6" />
                  </div>
                  <div>
                    <p class="text-xs font-bold text-gray-400 uppercase tracking-wider">Revenue</p>
                    <h3 class="text-xl font-extrabold text-gray-900 mt-0.5">{{ formatCurrency(analyticsData.sales?.total_revenue ?? 0) }}</h3>
                  </div>
                </div>
              </div>

              <div class="bg-white rounded-xl p-6 shadow-sm border border-gray-200 flex items-center justify-between">
                <div class="flex items-center gap-4">
                  <div class="p-3 bg-rose-50 text-rose-600 rounded-xl border border-rose-100">
                    <XCircleIcon class="w-6 h-6" />
                  </div>
                  <div>
                    <p class="text-xs font-bold text-gray-400 uppercase tracking-wider">Expenses</p>
                    <h3 class="text-xl font-extrabold text-gray-900 mt-0.5">{{ formatCurrency(analyticsData.expenses?.total_expenses ?? 0) }}</h3>
                  </div>
                </div>
              </div>

              <div class="bg-white rounded-xl p-6 shadow-sm border border-gray-200 flex items-center justify-between">
                <div class="flex items-center gap-4">
                  <div :class="['p-3 rounded-xl border', netProfit >= 0 ? 'bg-emerald-50 text-emerald-600 border-emerald-100' : 'bg-rose-50 text-rose-600 border-rose-100']">
                    <ChartBarIcon class="w-6 h-6" />
                  </div>
                  <div>
                    <p class="text-xs font-bold text-gray-400 uppercase tracking-wider">Net Surplus</p>
                    <h3 class="text-xl font-extrabold text-gray-900 mt-0.5" :class="netProfit >= 0 ? 'text-emerald-600' : 'text-rose-600'">{{ formatCurrency(netProfit) }}</h3>
                  </div>
                </div>
              </div>
            </div>

          </div>

          <!-- ── CROP & PRODUCTION TAB ── -->
          <div v-if="activeSubTab === 'production'" class="space-y-6">
            
            <!-- Yield and Variety Performance Charts -->
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
              <div class="bg-white rounded-xl p-6 shadow-sm border border-gray-200">
                <h3 class="text-base font-bold text-gray-800 mb-4">Yield Over Time</h3>
                <div class="h-72">
                  <LineChart v-if="yieldChartData.labels.length > 0" :data="yieldChartData" :options="chartOptions" />
                  <NoData v-else />
                </div>
              </div>

              <div class="bg-white rounded-xl p-6 shadow-sm border border-gray-200">
                <h3 class="text-base font-bold text-gray-800 mb-4">Variety Distribution</h3>
                <div class="h-72">
                  <BarChart v-if="varietyChartData.labels.length > 0" :data="varietyChartData" :options="chartOptions" />
                  <NoData v-else />
                </div>
              </div>
            </div>

            <!-- Active Seedbed / Nursery Table -->
            <div class="bg-white rounded-xl p-6 shadow-sm border border-gray-200">
              <h3 class="text-base font-bold text-gray-900 mb-4">Active Nursery Seedbeds</h3>
              <div class="overflow-x-auto">
                <table class="min-w-full divide-y divide-gray-150">
                  <thead>
                    <tr class="bg-gray-50 text-left text-xs font-bold text-gray-500 uppercase tracking-wider">
                      <th class="px-6 py-3.5">Active Batches</th>
                      <th class="px-6 py-3.5">Ready for Transplant</th>
                      <th class="px-6 py-3.5">Nursery Status</th>
                    </tr>
                  </thead>
                  <tbody class="divide-y divide-gray-100 text-sm">
                    <tr>
                      <td class="px-6 py-4 whitespace-nowrap font-semibold text-gray-900">
                        {{ analyticsData.nursery?.active_batches ?? 0 }} Batch(es)
                      </td>
                      <td class="px-6 py-4 whitespace-nowrap text-emerald-600 font-bold">
                        {{ analyticsData.nursery?.ready_for_transplant ?? 0 }} Batch(es)
                      </td>
                      <td class="px-6 py-4 whitespace-nowrap">
                        <span class="px-3 py-1 bg-emerald-50 text-emerald-700 text-xs font-bold uppercase rounded-full border border-emerald-200">
                          Active Nurseries
                        </span>
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>
            </div>

            <!-- Crop Failure Analysis Summary -->
            <div class="bg-white rounded-xl p-6 shadow-sm border border-gray-200">
              <div class="border-b border-gray-100 pb-4 mb-6 flex justify-between items-center">
                <div>
                  <h3 class="text-base font-bold text-gray-900">Crop Loss & Failure Analysis</h3>
                  <p class="text-xs text-gray-500 mt-0.5">Summary of failed plantings and associated risk factors</p>
                </div>
                <span class="text-xs font-bold px-3 py-1 bg-rose-50 text-rose-700 border border-rose-100 rounded-full">
                  Failure Rate: {{ analyticsData.failure_analysis?.failure_rate_pct ?? 0 }}%
                </span>
              </div>

              <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-6">
                <div class="p-4 bg-gray-50 rounded-xl border border-gray-100">
                  <span class="text-xs font-bold text-gray-400 uppercase">Total Failed Plantings</span>
                  <div class="text-2xl font-extrabold text-gray-900 mt-1">{{ analyticsData.failure_analysis?.total_failed ?? 0 }}</div>
                </div>
                <div class="p-4 bg-rose-50 rounded-xl border border-rose-100">
                  <span class="text-xs font-bold text-rose-700 uppercase">Total Estimated Loss</span>
                  <div class="text-2xl font-extrabold text-rose-700 mt-1">{{ formatCurrency(analyticsData.failure_analysis?.total_crop_loss_value ?? 0) }}</div>
                </div>
                <div class="p-4 bg-gray-50 rounded-xl border border-gray-100">
                  <span class="text-xs font-bold text-gray-400 uppercase">Avg Life Before Failure</span>
                  <div class="text-2xl font-extrabold text-gray-900 mt-1">{{ analyticsData.failure_analysis?.avg_days_before_failure ?? 0 }} Days</div>
                </div>
              </div>

              <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <!-- Breakdown by Reason -->
                <div class="space-y-3">
                  <h4 class="text-xs font-bold text-gray-400 uppercase tracking-wider">Failures by Category</h4>
                  <div class="space-y-2">
                    <div v-for="(count, category) in analyticsData.failure_analysis?.by_category ?? {}" :key="category" class="flex items-center gap-3">
                      <span class="text-xs text-gray-600 w-24 truncate capitalize">{{ category }}</span>
                      <div class="flex-1 bg-gray-100 rounded-full h-2 overflow-hidden">
                        <div class="h-full rounded-full bg-rose-500" :style="{ width: `${(count / Math.max(...Object.values(analyticsData.failure_analysis.by_category || {a: 1}))) * 100}%` }"></div>
                      </div>
                      <span class="text-xs font-bold text-gray-700 w-6 text-right">{{ count }}</span>
                    </div>
                  </div>
                </div>

                <!-- Breakdown by Variety -->
                <div class="space-y-3">
                  <h4 class="text-xs font-bold text-gray-400 uppercase tracking-wider">Failures by Variety</h4>
                  <div class="space-y-2">
                    <div v-for="(count, variety) in analyticsData.failure_analysis?.by_variety ?? {}" :key="variety" class="flex items-center gap-3">
                      <span class="text-xs text-gray-600 w-24 truncate">{{ variety }}</span>
                      <div class="flex-1 bg-gray-100 rounded-full h-2 overflow-hidden">
                        <div class="h-full rounded-full bg-amber-500" :style="{ width: `${(count / Math.max(...Object.values(analyticsData.failure_analysis.by_variety || {a: 1}))) * 100}%` }"></div>
                      </div>
                      <span class="text-xs font-bold text-gray-700 w-6 text-right">{{ count }}</span>
                    </div>
                  </div>
                </div>
              </div>

            </div>

          </div>

          <!-- ── FINANCIALS TAB ── -->
          <div v-if="activeSubTab === 'financial'" class="space-y-6">
            
            <!-- Revenue vs Expenses Flow line chart -->
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
              <div class="bg-white rounded-xl p-6 shadow-sm border border-gray-200">
                <h3 class="text-base font-bold text-gray-800 mb-4">Revenue vs Expenses Flow</h3>
                <div class="h-72">
                  <LineChart v-if="financialChartData.labels.length > 0" :data="financialChartData" :options="chartOptions" />
                  <NoData v-else />
                </div>
              </div>

              <div class="bg-white rounded-xl p-6 shadow-sm border border-gray-200">
                <h3 class="text-base font-bold text-gray-800 mb-4">Expense Categories</h3>
                <div class="h-72">
                  <PieChart v-if="expenseChartData.labels.length > 0" :data="expenseChartData" :options="pieChartOptions" />
                  <NoData v-else />
                </div>
              </div>
            </div>

            <!-- Financial Comparison Bar chart & Categories breakdown -->
            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
              
              <!-- Financial Overview Card -->
              <div class="lg:col-span-2 bg-white rounded-xl p-6 shadow-sm border border-gray-200 flex flex-col justify-between">
                <div class="mb-6 flex justify-between items-center">
                  <h3 class="text-base font-bold text-gray-900">Period Summary Comparison</h3>
                  <div class="flex gap-4 text-xs font-semibold text-gray-500">
                    <div class="flex items-center gap-1.5"><span class="w-2.5 h-2.5 rounded-sm bg-emerald-500"></span> Revenue</div>
                    <div class="flex items-center gap-1.5"><span class="w-2.5 h-2.5 rounded-sm bg-rose-500"></span> Expenses</div>
                  </div>
                </div>

                <div class="flex-1 flex items-end justify-center gap-8 md:gap-16 min-h-[220px] border-b border-gray-100 pb-2 relative">
                  <div class="absolute inset-0 flex flex-col justify-between pointer-events-none">
                     <div class="w-full border-t border-dashed border-gray-100 h-px"></div>
                     <div class="w-full border-t border-dashed border-gray-100 h-px"></div>
                     <div class="w-full border-t border-dashed border-gray-100 h-px"></div>
                  </div>

                  <div class="relative z-10 flex flex-col items-center gap-2.5 group">
                    <span class="text-[10px] font-bold text-emerald-700 opacity-0 group-hover:opacity-100 transition-opacity absolute -top-5">{{ formatCurrency(analyticsData.sales?.total_revenue ?? 0) }}</span>
                    <div
                      class="w-16 bg-emerald-500 rounded-t-sm transition-all duration-500 hover:bg-emerald-600"
                      :style="{ height: `${Math.min((analyticsData.sales?.total_revenue ?? 0) / Math.max((analyticsData.sales?.total_revenue ?? 1), (analyticsData.expenses?.total_expenses ?? 1)) * 180, 180)}px` }"
                    ></div>
                    <span class="text-xs font-bold text-gray-500 uppercase tracking-wider">Revenue</span>
                  </div>

                  <div class="relative z-10 flex flex-col items-center gap-2.5 group">
                    <span class="text-[10px] font-bold text-rose-700 opacity-0 group-hover:opacity-100 transition-opacity absolute -top-5">{{ formatCurrency(analyticsData.expenses?.total_expenses ?? 0) }}</span>
                    <div
                      class="w-16 bg-rose-500 rounded-t-sm transition-all duration-500 hover:bg-rose-600"
                      :style="{ height: `${Math.min((analyticsData.expenses?.total_expenses ?? 0) / Math.max((analyticsData.sales?.total_revenue ?? 1), (analyticsData.expenses?.total_expenses ?? 1)) * 180, 180)}px` }"
                    ></div>
                    <span class="text-xs font-bold text-gray-500 uppercase tracking-wider">Expenses</span>
                  </div>

                   <div class="relative z-10 flex flex-col items-center gap-2.5 group">
                     <span class="text-[10px] font-bold opacity-0 group-hover:opacity-100 transition-opacity absolute -top-5 text-emerald-600">{{ formatCurrency(netProfit) }}</span>
                    <div
                      class="w-16 rounded-t-sm transition-all duration-500 opacity-80"
                      :class="netProfit >= 0 ? 'bg-emerald-600 hover:bg-emerald-700' : 'bg-rose-600 hover:bg-rose-700'"
                      :style="{ height: `${Math.min(Math.abs(netProfit) / Math.max((analyticsData.sales?.total_revenue ?? 1), (analyticsData.expenses?.total_expenses ?? 1)) * 180, 180)}px` }"
                    ></div>
                    <span class="text-xs font-bold text-gray-500 uppercase tracking-wider">Net Surplus</span>
                  </div>
                </div>
              </div>

              <!-- Top Expenses Card -->
              <div class="bg-white rounded-xl p-6 shadow-sm border border-gray-200">
                <h3 class="text-base font-bold text-gray-900 mb-4">Expenses Category Breakdowns</h3>
                <div class="space-y-4">
                  <div v-for="(data, category) in analyticsData.expenses?.by_category ?? {}" :key="category">
                    <div class="flex justify-between text-xs mb-1">
                      <span class="font-bold text-gray-700 capitalize">{{ category }}</span>
                      <span class="text-gray-400 font-semibold">{{ data.percentage }}%</span>
                    </div>
                    <div class="w-full bg-gray-100 rounded-full h-1.5 overflow-hidden">
                      <div class="bg-emerald-600 h-1.5 rounded-full" :style="{ width: `${data.percentage}%` }"></div>
                    </div>
                  </div>
                  <div v-if="!Object.keys(analyticsData.expenses?.by_category ?? {}).length" class="text-center text-sm text-gray-400 py-4">
                    No expense items logged.
                  </div>
                </div>
              </div>

            </div>

            <!-- Projected Cash Flow Column Chart -->
            <div v-if="analyticsData.financial_forecast" class="bg-white rounded-xl shadow-sm border border-gray-200 p-6">
              <div class="mb-8 flex justify-between items-center">
                <div>
                  <h3 class="text-base font-bold text-gray-900">Projected Operational Cash Flow</h3>
                  <p class="text-xs text-gray-500 mt-0.5">Estimated revenue and expense forecast based on cyclical trends</p>
                </div>
                <div class="flex gap-4 text-xs font-semibold text-gray-500">
                  <div class="flex items-center gap-1.5"><span class="w-3 h-3 rounded bg-emerald-400"></span> Projected Revenue</div>
                  <div class="flex items-center gap-1.5"><span class="w-3 h-3 rounded bg-rose-400"></span> Projected Expense</div>
                </div>
              </div>

              <div class="h-64 flex items-end justify-between gap-2 md:gap-4 overflow-x-auto pb-2 border-b border-gray-100">
                <div v-for="(month, index) in analyticsData.financial_forecast.months" :key="index" class="flex flex-col items-center gap-2 flex-1 min-w-[65px]">
                  <div class="w-full flex items-end justify-center gap-1 h-48 relative">
                    <div class="w-3 md:w-5 bg-emerald-400 rounded-t-sm hover:bg-emerald-500 transition-all relative group"
                      :style="{ height: `${Math.min((analyticsData.financial_forecast.projected_revenue[index] / (Math.max(...analyticsData.financial_forecast.projected_revenue, ...analyticsData.financial_forecast.projected_expenses) || 1)) * 100, 100)}%` }">
                      <div class="opacity-0 group-hover:opacity-100 absolute bottom-full left-1/2 -translate-x-1/2 mb-1 bg-gray-950 text-white text-[10px] px-2 py-1 rounded whitespace-nowrap z-20 shadow-lg font-bold">
                        Rev: {{ formatCurrency(analyticsData.financial_forecast.projected_revenue[index]) }}
                      </div>
                    </div>
                    <div class="w-3 md:w-5 bg-rose-400 rounded-t-sm hover:bg-rose-500 transition-all relative group"
                      :style="{ height: `${Math.min((analyticsData.financial_forecast.projected_expenses[index] / (Math.max(...analyticsData.financial_forecast.projected_revenue, ...analyticsData.financial_forecast.projected_expenses) || 1)) * 100, 100)}%` }">
                       <div class="opacity-0 group-hover:opacity-100 absolute bottom-full left-1/2 -translate-x-1/2 mb-1 bg-gray-950 text-white text-[10px] px-2 py-1 rounded whitespace-nowrap z-20 shadow-lg font-bold">
                        Exp: {{ formatCurrency(analyticsData.financial_forecast.projected_expenses[index]) }}
                      </div>
                    </div>
                  </div>
                  <span class="text-xs font-semibold text-gray-500 truncate w-full text-center mt-1">{{ month }}</span>
                </div>
              </div>
            </div>

          </div>

          <!-- ── OPERATIONS TAB ── -->
          <div v-if="activeSubTab === 'operations'" class="space-y-6">
            
            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
              
              <!-- Task Distribution donut chart -->
              <div class="bg-white rounded-xl p-6 shadow-sm border border-gray-200 lg:col-span-2">
                 <h3 class="text-base font-bold text-gray-900 mb-6">Task Distribution Share</h3>
                 <div class="flex items-center">
                    <div class="relative w-32 h-32 mr-8 flex-shrink-0">
                      <svg viewBox="0 0 100 100" class="w-full h-full transform -rotate-90">
                        <circle cx="50" cy="50" r="40" fill="transparent" stroke="#e5e7eb" stroke-width="12" />
                        <circle cx="50" cy="50" r="40" fill="transparent" stroke="#10b981" stroke-width="12"
                          :stroke-dasharray="`${completedPercent * 2.51} 251.2`" class="transition-all duration-1000 ease-out" />
                        <circle cx="50" cy="50" r="40" fill="transparent" stroke="#f59e0b" stroke-width="12"
                          :stroke-dasharray="`${pendingPercent * 2.51} 251.2`" :stroke-dashoffset="`${-completedPercent * 2.51}`" class="transition-all duration-1000 ease-out" />
                        <circle cx="50" cy="50" r="40" fill="transparent" stroke="#ef4444" stroke-width="12"
                          :stroke-dasharray="`${overduePercent * 2.51} 251.2`" :stroke-dashoffset="`${-(completedPercent + pendingPercent) * 2.51}`" class="transition-all duration-1000 ease-out" />
                      </svg>
                      <div class="absolute inset-0 flex flex-col items-center justify-center">
                         <span class="text-2xl font-bold text-gray-900">{{ analyticsData.tasks?.total_tasks ?? 0 }}</span>
                         <span class="text-[9px] text-gray-400 uppercase font-extrabold tracking-wider">Total</span>
                      </div>
                    </div>

                    <div class="flex-1 space-y-3">
                      <div class="flex justify-between items-center p-2.5 rounded-lg hover:bg-gray-50 border border-transparent hover:border-gray-100 transition-all">
                         <div class="flex items-center gap-2">
                            <span class="w-2.5 h-2.5 rounded-full bg-emerald-500"></span>
                            <span class="text-sm font-semibold text-gray-600">Completed</span>
                         </div>
                         <span class="font-bold text-sm text-gray-900">{{ analyticsData.tasks?.completed_tasks ?? 0 }}</span>
                      </div>
                       <div class="flex justify-between items-center p-2.5 rounded-lg hover:bg-gray-50 border border-transparent hover:border-gray-100 transition-all">
                         <div class="flex items-center gap-2">
                            <span class="w-2.5 h-2.5 rounded-full bg-amber-500"></span>
                            <span class="text-sm font-semibold text-gray-600">Pending</span>
                         </div>
                         <span class="font-bold text-sm text-gray-900">{{ analyticsData.tasks?.pending_tasks ?? 0 }}</span>
                      </div>
                       <div class="flex justify-between items-center p-2.5 rounded-lg hover:bg-gray-50 border border-transparent hover:border-gray-100 transition-all">
                         <div class="flex items-center gap-2">
                            <span class="w-2.5 h-2.5 rounded-full bg-rose-500"></span>
                            <span class="text-sm font-semibold text-gray-600">Overdue</span>
                         </div>
                         <span class="font-bold text-sm text-gray-900">{{ analyticsData.tasks?.overdue_tasks ?? 0 }}</span>
                      </div>
                    </div>
                 </div>
              </div>

              <!-- Labor Workforce Summary -->
              <div class="bg-white rounded-xl p-6 shadow-sm border border-gray-200">
                <h3 class="text-base font-bold text-gray-900 mb-6">Workforce Overview</h3>
                <div class="space-y-4">
                  <div class="flex justify-between items-center py-2 border-b border-gray-50">
                    <span class="text-xs font-bold text-gray-400 uppercase tracking-wider">Total Laborers</span>
                    <span class="text-lg font-bold text-gray-900">{{ analyticsData.laborers?.total_laborers ?? 0 }}</span>
                  </div>
                  <div class="flex justify-between items-center py-2 border-b border-gray-50">
                    <span class="text-xs font-bold text-gray-400 uppercase tracking-wider">Active Workers</span>
                    <span class="text-lg font-bold text-emerald-600">{{ analyticsData.laborers?.active_laborers ?? 0 }}</span>
                  </div>
                  <div class="flex justify-between items-center py-2 border-b border-gray-50">
                    <span class="text-xs font-bold text-gray-400 uppercase tracking-wider">Est. Payroll Cost</span>
                    <span class="text-lg font-bold text-gray-900">{{ formatCurrency(analyticsData.laborers?.total_labor_cost ?? 0) }}</span>
                  </div>
                  <div class="flex justify-between items-center py-2">
                    <span class="text-xs font-bold text-gray-400 uppercase tracking-wider">Task Efficiency</span>
                    <span class="text-lg font-bold text-amber-600">{{ analyticsData.laborers?.completion_rate ?? 0 }}%</span>
                  </div>
                </div>
              </div>

            </div>

            <!-- Post-Harvest Recovery Analytics -->
            <div v-if="analyticsData.post_harvest?.total_processes > 0" class="bg-white rounded-xl shadow-sm border border-gray-200 overflow-hidden">
               <div class="p-6 border-b border-gray-100 flex justify-between items-center">
                  <h3 class="text-base font-bold text-gray-900">Post-Harvest Recovery Rates</h3>
                  <span class="text-xs font-bold text-gray-500 bg-gray-150 px-3 py-1 rounded-full">{{ analyticsData.post_harvest.total_processes }} processes logged</span>
               </div>
               <div class="p-6">
                  <div class="grid grid-cols-2 md:grid-cols-4 gap-4 mb-6">
                     <div class="text-center p-4 bg-emerald-50 rounded-xl border border-emerald-100">
                        <div class="text-2xl font-extrabold text-emerald-700">{{ analyticsData.post_harvest.average_recovery_rate }}%</div>
                        <div class="text-xs font-semibold text-gray-500 mt-1 uppercase tracking-wider">Avg Recovery Rate</div>
                     </div>
                     <div class="text-center p-4 bg-amber-50 rounded-xl border border-amber-100">
                        <div class="text-2xl font-extrabold text-amber-700">{{ formatCurrency(analyticsData.post_harvest.cost_optimization?.self_avg_cost ?? 0) }}</div>
                        <div class="text-xs font-semibold text-gray-500 mt-1 uppercase tracking-wider">Avg Self Cost/Unit</div>
                     </div>
                     <div class="text-center p-4 bg-blue-50 rounded-xl border border-blue-100">
                        <div class="text-2xl font-extrabold text-blue-700">{{ formatCurrency(analyticsData.post_harvest.cost_optimization?.provider_avg_cost ?? 0) }}</div>
                        <div class="text-xs font-semibold text-gray-500 mt-1 uppercase tracking-wider">Avg Provider Cost/Unit</div>
                     </div>
                     <div class="text-center p-4 bg-purple-50 rounded-xl border border-purple-100">
                        <div class="text-2xl font-extrabold text-purple-700">{{ analyticsData.post_harvest.total_processes }}</div>
                        <div class="text-xs font-semibold text-gray-500 mt-1 uppercase tracking-wider">Completed Batches</div>
                     </div>
                  </div>
                  <div v-if="analyticsData.post_harvest.variety_performance?.length" class="space-y-3">
                     <h4 class="text-xs font-bold text-gray-400 uppercase tracking-wider">Recovery Index by Variety</h4>
                     <div v-for="v in analyticsData.post_harvest.variety_performance" :key="v.variety" class="flex items-center gap-3">
                        <span class="text-sm font-semibold text-gray-600 w-28 truncate" :title="v.variety">{{ v.variety }}</span>
                        <div class="flex-1 bg-gray-100 rounded-full h-2 overflow-hidden">
                           <div class="h-full rounded-full bg-emerald-500 transition-all" :style="{ width: `${Math.min(v.average_recovery_rate, 100)}%` }"></div>
                        </div>
                        <span class="text-xs font-bold text-gray-700 w-12 text-right">{{ v.average_recovery_rate }}%</span>
                     </div>
                  </div>
                  <div v-if="analyticsData.post_harvest.historical_trends?.length" class="mt-6 space-y-3">
                     <h4 class="text-xs font-bold text-gray-400 uppercase tracking-wider">Monthly Recovery Trend</h4>
                     <div class="flex items-end gap-1 h-24">
                        <div
                          v-for="t in analyticsData.post_harvest.historical_trends"
                          :key="t.month"
                          class="flex-1 bg-emerald-400 rounded-t-sm hover:bg-emerald-500 transition-all relative group"
                          :style="{ height: `${Math.min(t.average_recovery_rate, 100)}%` }"
                        >
                           <div class="opacity-0 group-hover:opacity-100 absolute bottom-full left-1/2 -translate-x-1/2 mb-1 bg-gray-900 text-white text-[10px] px-2 py-1 rounded whitespace-nowrap z-20 shadow-lg font-bold">
                              {{ t.month }}: {{ t.average_recovery_rate }}%
                           </div>
                        </div>
                     </div>
                     <div class="flex gap-1 text-[9px] text-gray-400 font-bold uppercase tracking-wider">
                        <span v-for="t in analyticsData.post_harvest.historical_trends" :key="'l-'+t.month" class="flex-1 text-center truncate">{{ t.month.substring(5) }}</span>
                     </div>
                  </div>
               </div>
            </div>

          </div>

          <!-- ── WEATHER & FORECASTS TAB ── -->
          <div v-if="activeSubTab === 'weather'" class="space-y-6">
            
            <!-- Weather Correlation Line Chart -->
            <div class="bg-white rounded-xl p-6 shadow-sm border border-gray-200">
              <h3 class="text-base font-bold text-gray-800 mb-4">Yield & Rainfall Correlation</h3>
              <div class="h-80">
                <LineChart v-if="weatherCorrelationData.labels.length > 0" :data="weatherCorrelationData" :options="weatherChartOptions" />
                <NoData v-else />
              </div>
            </div>

            <!-- Current Climate metrics & impact indices -->
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
              <div class="bg-white rounded-xl p-6 shadow-sm border border-gray-200 flex items-center justify-between">
                <div>
                  <p class="text-xs font-bold text-gray-400 uppercase tracking-wider">Average Daily Rainfall</p>
                  <h3 class="text-2xl font-extrabold text-gray-900 mt-1">{{ averageRainfall }} mm</h3>
                </div>
                <div class="p-3 bg-blue-50 text-blue-600 rounded-xl border border-blue-100">
                  <CloudIcon class="w-6 h-6" />
                </div>
              </div>

              <div class="bg-white rounded-xl p-6 shadow-sm border border-gray-200 flex items-center justify-between">
                <div>
                  <p class="text-xs font-bold text-gray-400 uppercase tracking-wider">Average Temperature</p>
                  <h3 class="text-2xl font-extrabold text-gray-900 mt-1">{{ averageTemperature }} °C</h3>
                </div>
                <div class="p-3 bg-amber-50 text-amber-600 rounded-xl border border-amber-100">
                  <SparklesIcon class="w-6 h-6" />
                </div>
              </div>

              <div class="bg-white rounded-xl p-6 shadow-sm border border-gray-200 flex items-center justify-between">
                <div>
                  <p class="text-xs font-bold text-gray-400 uppercase tracking-wider">Climate Agronomic Impact</p>
                  <h3 class="text-2xl font-extrabold text-emerald-600 mt-1">{{ weatherImpact }}%</h3>
                  <p class="text-[10px] text-gray-400 mt-0.5">Favorable growth days</p>
                </div>
                <div class="p-3 bg-emerald-50 text-emerald-600 rounded-xl border border-emerald-100">
                  <ChartBarIcon class="w-6 h-6" />
                </div>
              </div>
            </div>

            <!-- 7-Day Disease & Pest Outlook by Field -->
            <div v-if="analyticsData.pests?.forecasts?.length" class="bg-white rounded-xl shadow-sm border border-gray-200 overflow-hidden">
               <div class="p-6 border-b border-gray-100 flex justify-between items-center">
                  <h3 class="text-base font-bold text-gray-900">7-Day Pest & Disease Predictive Outlook</h3>
                  <span class="text-xs font-bold text-gray-500 bg-gray-150 px-3 py-1 rounded-full">Agronomic AI Prediction</span>
               </div>
               <div class="p-6 grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                  <div v-for="(field, index) in analyticsData.pests.forecasts" :key="index" class="space-y-3">
                     <div>
                       <div class="text-sm font-bold text-gray-900 flex items-center gap-2">
                          <span class="w-1.5 h-1.5 rounded-full bg-emerald-500"></span>
                          {{ field.field_name }}
                       </div>
                       <div v-if="field.crop_info" class="mt-1 ml-3.5 flex items-center gap-1.5 text-xs text-gray-500">
                         <span class="font-bold text-emerald-700 bg-emerald-50 px-2 py-0.5 rounded border border-emerald-100">{{ field.crop_info.variety }}</span>
                         <span>•</span>
                         <span class="font-medium">{{ field.crop_info.growth_stage }}</span>
                         <span class="text-gray-400">(Day {{ field.crop_info.days_planted }})</span>
                       </div>
                     </div>
                     <div class="space-y-2">
                        <div v-for="(pred, pIndex) in field.predictions.slice(0, 3)" :key="pIndex"
                             class="flex items-start gap-3 p-3 rounded-lg border border-gray-100 bg-gray-50/50 hover:border-gray-200 transition-colors">
                            <div class="mt-1">
                               <span v-if="pred.risks[0].risk_level === 'High'" class="block w-2.5 h-2.5 rounded-full bg-rose-500 shadow-sm shadow-rose-200"></span>
                               <span v-else-if="pred.risks[0].risk_level === 'Moderate'" class="block w-2.5 h-2.5 rounded-full bg-amber-500 shadow-sm shadow-amber-200"></span>
                               <span v-else class="block w-2.5 h-2.5 rounded-full bg-emerald-500 shadow-sm shadow-emerald-200"></span>
                            </div>
                            <div>
                               <div class="flex justify-between items-center w-full gap-2">
                                  <span class="text-xs font-bold text-gray-700">{{ pred.day_name }}</span>
                                  <span class="text-[10px] text-gray-400 font-semibold">{{ pred.date }}</span>
                               </div>
                               <p class="text-sm font-bold text-gray-900 mt-0.5">{{ pred.risks[0].pest_name }}</p>
                               <p class="text-xs text-gray-500 mt-1 leading-relaxed">{{ pred.risks[0].description }}</p>
                               <p v-if="pred.risks[0].stage_note" class="text-[11px] text-amber-600 mt-1 italic font-semibold">
                                 {{ pred.risks[0].stage_note }}
                               </p>
                            </div>
                        </div>
                     </div>
                  </div>
               </div>
            </div>

          </div>

        </div>

      </div>

      <div v-else class="text-center py-32 bg-white rounded-xl border border-gray-200 border-dashed">
        <div class="w-16 h-16 bg-gray-50 rounded-full flex items-center justify-center mx-auto mb-4 text-gray-400">
           <svg class="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"></path></svg>
        </div>
        <h3 class="text-lg font-bold text-gray-900 mb-1">No Analytics Data Available</h3>
        <p class="text-gray-500 text-sm mb-6">We couldn't retrieve the cyclical logs for the selected timeframe.</p>
        <button
          @click="fetchAnalytics"
          class="px-5 py-2 bg-white border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-50 font-bold text-sm transition-colors shadow-sm"
        >
          Try Again
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';
import {
  ClockIcon,
  SparklesIcon,
  CloudIcon,
  ExclamationTriangleIcon,
  BugAntIcon,
  CurrencyDollarIcon,
  XCircleIcon,
  CubeIcon,
  ClipboardDocumentListIcon,
  ChartBarIcon,
  ArrowTrendingUpIcon,
  ShoppingBagIcon
} from '@heroicons/vue/24/outline';
import { useRouter } from 'vue-router';
import api from '@/services/api';
import { formatCurrency, formatNumber } from '@/utils/format';

// Pinia stores for historical time series charts
import { useFarmStore } from '@/stores/farm';
import { useWeatherStore } from '@/stores/weather';

// Chart components
import LineChart from '@/Components/Charts/LineChart.vue';
import BarChart from '@/Components/Charts/BarChart.vue';
import PieChart from '@/Components/Charts/PieChart.vue';
import NoData from '@/Components/NoData.vue';

const router = useRouter();
const farmStore = useFarmStore();
const weatherStore = useWeatherStore();

// Sub-navigation tabs list
const activeSubTab = ref('overview');
const navigationTabs = [
  { id: 'overview', name: 'Overview', icon: ChartBarIcon },
  { id: 'production', name: 'Crop & Production', icon: SparklesIcon },
  { id: 'financial', name: 'Financials', icon: CurrencyDollarIcon },
  { id: 'operations', name: 'Operations & Tasks', icon: ClipboardDocumentListIcon },
  { id: 'weather', name: 'Weather & Forecasts', icon: CloudIcon }
];

// State variables
const isLoading = ref(true);
const analyticsData = ref(null);
const startDate = ref(new Date(Date.now() - 90 * 24 * 60 * 60 * 1000).toISOString().split('T')[0]); // 90 days ago
const endDate = ref(new Date().toISOString().split('T')[0]); // Today

// Map HeroIcon names from API response to actual component icons
const iconMap = {
  ClockIcon,
  SparklesIcon,
  CloudIcon,
  ExclamationTriangleIcon,
  BugAntIcon,
  CurrencyDollarIcon,
  XCircleIcon,
  CubeIcon,
  ClipboardDocumentListIcon,
  ChartBarIcon,
  ArrowTrendingUpIcon,
  ShoppingBagIcon
};
const getIconComponent = (name) => {
  return iconMap[name] || SparklesIcon;
};

// Colors for charts
const chartColors = [
  'rgba(16, 185, 129, 0.6)',
  'rgba(59, 130, 246, 0.6)',
  'rgba(245, 158, 11, 0.6)',
  'rgba(139, 92, 246, 0.6)',
  'rgba(239, 68, 68, 0.6)',
  'rgba(6, 182, 212, 0.6)'
];
const getColor = (index) => chartColors[index % chartColors.length];

// Mappings of store elements
const ensureArray = (value) => (Array.isArray(value) ? value : []);
const harvests = computed(() => ensureArray(farmStore.harvests));
const fields = computed(() => ensureArray(farmStore.fields));
const sales = computed(() => ensureArray(farmStore.sales));
const expensesList = computed(() => ensureArray(farmStore.expenses));
const weatherHistoryRecords = computed(() => ensureArray(weatherStore.weatherHistory));

const predominantUnit = computed(() => {
  if (!harvests.value.length) return 'kg';
  const unitCounts = harvests.value.reduce((acc, h) => {
    const unit = h?.unit || 'kg';
    acc[unit] = (acc[unit] || 0) + 1;
    return acc;
  }, {});
  const entries = Object.entries(unitCounts);
  if (!entries.length) return 'kg';
  return entries.reduce((best, current) => (current[1] > best[1] ? current : best))[0];
});

// Chart data properties
const yieldChartData = computed(() => {
  const ordered = harvests.value
    .filter(harvest => harvest?.harvest_date && !Number.isNaN(new Date(harvest.harvest_date).getTime()))
    .sort((a, b) => new Date(a.harvest_date) - new Date(b.harvest_date))
    .slice(-12);

  if (!ordered.length) return { labels: [], datasets: [] };

  return {
    labels: ordered.map((harvest) => {
      const parsed = new Date(harvest.harvest_date);
      return parsed.toLocaleDateString(undefined, { month: 'short', day: 'numeric' });
    }),
    datasets: [{
      label: `Yield (${predominantUnit.value})`,
      data: ordered.map((harvest) => Number(harvest?.yield) || 0),
      borderColor: 'rgb(5, 150, 105)',
      backgroundColor: 'rgba(5, 150, 105, 0.1)',
      tension: 0.1
    }]
  };
});

const varietyChartData = computed(() => {
  const varietyTotals = harvests.value.reduce((acc, harvest) => {
    const variety = harvest?.planting?.crop_type || 'Unknown Variety';
    const yieldValue = Number(harvest?.yield) || 0;
    acc[variety] = (acc[variety] || 0) + yieldValue;
    return acc;
  }, {});

  const entries = Object.entries(varietyTotals).sort((a, b) => b[1] - a[1]);
  if (!entries.length) return { labels: [], datasets: [] };

  const labels = entries.map(([variety]) => variety);
  const data = entries.map(([, total]) => total);

  return {
    labels,
    datasets: [{
      label: `Yield (${predominantUnit.value})`,
      data,
      backgroundColor: labels.map((_, index) => getColor(index))
    }]
  };
});

const financialChartData = computed(() => {
  // Aggregate sales
  const salesMap = new Map();
  sales.value.forEach(sale => {
    const date = sale.sale_date;
    if (!date) return;
    const key = date.slice(0, 7); // YYYY-MM
    salesMap.set(key, (salesMap.get(key) || 0) + (Number(sale.total_amount) || 0));
  });

  // Aggregate expenses
  const expensesMap = new Map();
  expensesList.value.forEach(exp => {
    const date = exp.date;
    if (!date) return;
    const key = date.slice(0, 7); // YYYY-MM
    expensesMap.set(key, (expensesMap.get(key) || 0) + (Number(exp.amount) || 0));
  });

  const keys = new Set([...salesMap.keys(), ...expensesMap.keys()]);
  const orderedKeys = Array.from(keys).sort();
  if (!orderedKeys.length) return { labels: [], datasets: [] };

  const labels = orderedKeys.map(k => {
    const [year, month] = k.split('-').map(Number);
    const d = new Date(year, month - 1, 1);
    return d.toLocaleDateString(undefined, { month: 'short', year: 'numeric' });
  });

  const revData = orderedKeys.map(k => parseFloat((salesMap.get(k) || 0).toFixed(2)));
  const expData = orderedKeys.map(k => parseFloat((expensesMap.get(k) || 0).toFixed(2)));

  return {
    labels,
    datasets: [
      {
        label: 'Revenue',
        data: revData,
        borderColor: 'rgb(16, 185, 129)',
        backgroundColor: 'rgba(16, 185, 129, 0.15)',
        tension: 0.2,
        fill: true
      },
      {
        label: 'Expenses',
        data: expData,
        borderColor: 'rgb(239, 68, 68)',
        backgroundColor: 'rgba(239, 68, 68, 0.15)',
        tension: 0.2,
        fill: true
      }
    ]
  };
});

const expenseChartData = computed(() => {
  if (!expensesList.value.length) return { labels: [], datasets: [] };

  const categoryTotals = expensesList.value.reduce((acc, exp) => {
    const cat = exp.category || 'Uncategorized';
    acc[cat] = (acc[cat] || 0) + (Number(exp.amount) || 0);
    return acc;
  }, {});

  const entries = Object.entries(categoryTotals).sort((a, b) => b[1] - a[1]);
  const labels = entries.map(([cat]) => cat);
  const data = entries.map(([, total]) => Number(total.toFixed(2)));

  return {
    labels,
    datasets: [{
      labels,
      data,
      backgroundColor: labels.map((_, index) => getColor(index))
    }]
  };
});

// Weather correlation computed mappings
const weatherByMonth = computed(() => {
  const map = new Map();
  weatherHistoryRecords.value.forEach((record) => {
    const date = record?.recorded_at || record?.date;
    if (!date) return;
    const key = date.slice(0, 7); // YYYY-MM
    const rainfall = Number(record?.rainfall) || 0;
    const temperature = Number(record?.temperature) || 0;
    const entry = map.get(key) || { rainfall: 0, temperature: 0, count: 0 };
    entry.rainfall += rainfall;
    entry.temperature += temperature;
    entry.count += 1;
    map.set(key, entry);
  });
  return map;
});

const weatherByDay = computed(() => {
  const map = new Map();
  weatherHistoryRecords.value.forEach((record) => {
    const date = record?.recorded_at || record?.date;
    if (!date) return;
    const key = date.slice(0, 10); // YYYY-MM-DD
    const rainfall = Number(record?.rainfall) || 0;
    const temperature = Number(record?.temperature) || 0;
    const entry = map.get(key) || { rainfall: 0, temperature: 0, count: 0 };
    entry.rainfall += rainfall;
    entry.temperature += temperature;
    entry.count += 1;
    map.set(key, entry);
  });
  return map;
});

const averageRainfall = computed(() => {
  if (!weatherHistoryRecords.value.length) return '0.0';
  const uniqueDays = weatherByDay.value.size || 1;
  const total = Array.from(weatherByDay.value.values()).reduce((sum, day) => sum + day.rainfall, 0);
  return (total / uniqueDays).toFixed(1);
});

const averageTemperature = computed(() => {
  if (!weatherHistoryRecords.value.length) return '0.0';
  const total = weatherHistoryRecords.value.reduce((sum, record) => sum + (Number(record?.temperature) || 0), 0);
  return (total / weatherHistoryRecords.value.length).toFixed(1);
});

const weatherImpact = computed(() => {
  if (!weatherByDay.value.size) return '0';
  const favorable = Array.from(weatherByDay.value.values()).filter((day) => {
    const dailyRain = day.rainfall;
    const avgTemp = day.count > 0 ? day.temperature / day.count : 0;
    return dailyRain >= 2 && dailyRain <= 20 && avgTemp >= 20 && avgTemp <= 35;
  }).length;
  return ((favorable / weatherByDay.value.size) * 100).toFixed(0);
});

const weatherCorrelationData = computed(() => {
  // Map monthly yield
  const yieldByMonth = new Map();
  harvests.value.forEach(h => {
    const date = h.harvest_date;
    if (!date) return;
    const key = date.slice(0, 7);
    yieldByMonth.set(key, (yieldByMonth.get(key) || 0) + (Number(h.yield) || 0));
  });

  const keys = new Set([
    ...weatherByMonth.value.keys(),
    ...yieldByMonth.keys()
  ]);
  const orderedKeys = Array.from(keys).sort();
  if (!orderedKeys.length) return { labels: [], datasets: [] };

  const labels = orderedKeys.map(k => {
    const [year, month] = k.split('-').map(Number);
    const date = new Date(year, month - 1, 1);
    return date.toLocaleDateString(undefined, { month: 'short', year: 'numeric' });
  });

  const rainfallData = orderedKeys.map((key) => {
    const entry = weatherByMonth.value.get(key);
    return entry ? Number(entry.rainfall.toFixed(1)) : 0;
  });
  const yieldData = orderedKeys.map((key) => {
    return Number((yieldByMonth.get(key) || 0).toFixed(1));
  });

  return {
    labels,
    datasets: [
      {
        label: 'Monthly Rainfall (mm)',
        data: rainfallData,
        borderColor: 'rgb(59, 130, 246)',
        backgroundColor: 'rgba(59, 130, 246, 0.15)',
        tension: 0.2,
        yAxisID: 'y',
        fill: true
      },
      {
        label: `Yield (${predominantUnit.value})`,
        data: yieldData,
        borderColor: 'rgb(16, 185, 129)',
        backgroundColor: 'rgba(16, 185, 129, 0.1)',
        tension: 0.2,
        yAxisID: 'y1'
      }
    ]
  };
});

// Common Options
const chartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: { display: true }
  },
  scales: {
    y: { beginAtZero: true }
  }
};

const pieChartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: { position: 'bottom' }
  }
};

const weatherChartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: { display: true, position: 'bottom' }
  },
  scales: {
    y: { beginAtZero: true, position: 'left' },
    y1: {
      beginAtZero: true,
      position: 'right',
      grid: { drawOnChartArea: false }
    }
  }
};

// Computed stats properties from API data
const netProfit = computed(() => {
  const revenue = analyticsData.value?.sales?.total_revenue ?? 0;
  const expenses = analyticsData.value?.expenses?.total_expenses ?? 0;
  return revenue - expenses;
});

const completedPercent = computed(() => {
  const total = analyticsData.value?.tasks?.total_tasks ?? 0;
  const completed = analyticsData.value?.tasks?.completed_tasks ?? 0;
  return total > 0 ? (completed / total) * 100 : 0;
});

const pendingPercent = computed(() => {
  const total = analyticsData.value?.tasks?.total_tasks ?? 0;
  const pending = analyticsData.value?.tasks?.pending_tasks ?? 0;
  return total > 0 ? (pending / total) * 100 : 0;
});

const overduePercent = computed(() => {
  const total = analyticsData.value?.tasks?.total_tasks ?? 0;
  const overdue = analyticsData.value?.tasks?.overdue_tasks ?? 0;
  return total > 0 ? (overdue / total) * 100 : 0;
});

// Methods
const navigateTo = (path) => {
  router.push(path);
};

const handleUpdate = () => {
  fetchAnalytics();
};

const fetchAnalytics = async () => {
  isLoading.value = true;
  try {
    const filters = {
      date_from: startDate.value,
      date_to: endDate.value
    };

    // Load store records in parallel with the analytics endpoint
    const storeLoads = [
      farmStore.fetchFields(),
      farmStore.fetchHarvests(filters),
      farmStore.fetchSales(filters),
      farmStore.fetchExpenses(filters)
    ];

    const responses = await Promise.all([
      api.get('/analytics/data-analysis', {
        params: {
          start_date: startDate.value,
          end_date: endDate.value,
        },
      }),
      ...storeLoads
    ]);

    analyticsData.value = responses[0].data;

    // Fetch weather history if farm profile or fields exist
    const farmId = farmStore.farmProfile?.id || (farmStore.fields && farmStore.fields[0]?.farm_id);
    if (farmId) {
      await weatherStore.fetchWeatherHistory(farmId, 365);
    }
  } catch (error) {
    console.error('Failed to fetch analytics:', error);
    analyticsData.value = null;
  } finally {
    isLoading.value = false;
  }
};

// Lifecyclehook
onMounted(() => {
  fetchAnalytics();
});
</script>

<style scoped>
/* Numerical typography styling */
h3, .text-2xl, .text-3xl, .text-lg {
  font-feature-settings: "tnum";
  font-variant-numeric: tabular-nums;
}

.line-clamp-1 {
  display: -webkit-box;
  -webkit-line-clamp: 1;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
.line-clamp-2 {
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.15s ease, transform 0.15s ease;
}
.fade-enter-from {
  opacity: 0;
  transform: translateY(5px);
}
.fade-leave-to {
  opacity: 0;
  transform: translateY(-5px);
}
</style>
