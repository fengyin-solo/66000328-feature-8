<template>
  <div class="bg-slate-800 rounded-lg p-4 border border-slate-700">
    <div class="flex items-center justify-between mb-3">
      <h3 class="text-sm font-bold text-slate-400">最近浏览记录</h3>
      <button
        v-if="store.viewHistory.length > 0"
        @click="store.clearHistory"
        class="text-xs text-slate-500 hover:text-red-400 transition"
      >
        清空记录
      </button>
    </div>
    <div v-if="store.viewHistory.length === 0" class="text-slate-500 text-sm text-center py-4">
      暂无浏览记录
    </div>
    <div v-else class="space-y-2 max-h-64 overflow-y-auto">
      <div
        v-for="(entry, index) in store.viewHistory"
        :key="entry.viewedAt"
        @click="handleRestore(entry)"
        class="cursor-pointer p-3 rounded-lg border border-slate-700 bg-slate-900 hover:border-cyan-500 hover:bg-cyan-900/20 transition-all group"
      >
        <div class="flex items-center justify-between">
          <div class="flex items-center gap-2">
            <span class="text-xs font-bold text-cyan-400 bg-cyan-900/50 px-1.5 py-0.5 rounded">
              #{{ index + 1 }}
            </span>
            <span class="text-sm font-bold text-slate-200 group-hover:text-cyan-400">
              {{ entry.molecule.name }}
            </span>
          </div>
          <span class="text-xs text-slate-500">
            {{ formatTime(entry.viewedAt) }}
          </span>
        </div>
        <div class="flex items-center gap-3 mt-2 text-xs">
          <span class="text-slate-400">{{ entry.molecule.formula }}</span>
          <span class="px-1.5 py-0.5 rounded bg-slate-700 text-slate-400">
            {{ entry.molecule.category }}
          </span>
          <span
            :class="entry.admet.ruleOfFive ? 'text-green-400' : 'text-red-400'"
          >
            {{ entry.admet.ruleOfFive ? '✓ 五规则' : '✗ 五规则' }}
          </span>
        </div>
        <div class="flex items-center gap-4 mt-1.5 text-xs">
          <span class="text-slate-500">
            LogP: <span :class="entry.admet.logP > 3 ? 'text-red-400' : 'text-green-400'">{{ entry.admet.logP }}</span>
          </span>
          <span class="text-slate-500">
            毒性: <span :class="entry.admet.toxicity.includes('高') ? 'text-red-400' : entry.admet.toxicity.includes('中') ? 'text-orange-400' : 'text-green-400'">
              {{ entry.admet.toxicity }}
            </span>
          </span>
        </div>
        <div class="mt-2 text-xs text-slate-500 opacity-0 group-hover:opacity-100 transition-opacity">
          点击恢复此视图状态
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useMoleculeStore } from '../store/molecule'
import type { HistoryEntry } from '../types'

const store = useMoleculeStore()

function formatTime(timestamp: number): string {
  const now = Date.now()
  const diff = now - timestamp
  if (diff < 60000) return '刚刚'
  if (diff < 3600000) return `${Math.floor(diff / 60000)} 分钟前`
  if (diff < 86400000) return `${Math.floor(diff / 3600000)} 小时前`
  const date = new Date(timestamp)
  return `${date.getMonth() + 1}/${date.getDate()} ${date.getHours()}:${String(date.getMinutes()).padStart(2, '0')}`
}

function handleRestore(entry: HistoryEntry) {
  store.restoreFromHistory(entry)
}
</script>
