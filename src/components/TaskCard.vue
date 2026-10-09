<script setup lang="ts">
import type { Task } from '../types/task'

defineProps<{
  task: Task
}>()

const emit = defineEmits<{
  toggle: [task: Task]
  delete: [task: Task]
}>()

const priorityLabels = {
  low: '低优先级',
  medium: '中优先级',
  high: '高优先级',
}

const statusLabels = {
  todo: '待办',
  'in-progress': '进行中',
  done: '完成',
}
</script>

<template>
  <article
    class="grid gap-4 rounded-md border border-slate-200 border-l-4 bg-white px-4 py-4 shadow-sm transition-transform duration-200 hover:scale-[1.02] sm:grid-cols-[minmax(0,1fr)_auto] sm:items-center"
    :class="{
      'border-l-rose-500': task.priority === 'high',
      'border-l-amber-400': task.priority === 'medium',
      'border-l-emerald-500': task.priority === 'low',
    }"
  >
    <div class="flex min-w-0 gap-3">
      <input
        type="checkbox"
        :checked="task.status === 'done'"
        :aria-label="task.status === 'done' ? '标记为待办' : '标记为完成'"
        class="mt-0.5 size-4 shrink-0 cursor-pointer accent-emerald-600"
        @change="emit('toggle', task)"
      />
      <div class="min-w-0">
        <h3
          class="m-0 text-sm font-semibold text-slate-800"
          :class="task.status === 'done' ? 'text-slate-400 line-through' : ''"
        >
          {{ task.title }}
        </h3>
        <p class="mb-0 mt-1.5 text-sm leading-5 text-slate-500">{{ task.description }}</p>
        <div class="mt-3 flex flex-wrap items-center gap-2">
          <span
            class="rounded-md px-2 py-1 text-xs font-medium"
            :class="{
              'bg-slate-100 text-slate-600': task.status === 'todo',
              'bg-indigo-50 text-indigo-700': task.status === 'in-progress',
              'bg-emerald-50 text-emerald-700': task.status === 'done',
            }"
          >{{ statusLabels[task.status] }}</span>
          <span
            class="rounded-md px-2 py-1 text-xs font-medium"
            :class="{
              'bg-slate-100 text-slate-600': task.priority === 'low',
              'bg-amber-50 text-amber-700': task.priority === 'medium',
              'bg-rose-50 text-rose-700': task.priority === 'high',
            }"
          >{{ priorityLabels[task.priority] }}</span>
        </div>
      </div>
    </div>

    <div class="flex items-center justify-between gap-4 pl-7 sm:justify-end sm:pl-0">
      <div class="text-xs text-slate-500 sm:text-right">
        <span class="block text-[11px] text-slate-400">截止日期</span>
        <time class="mt-1 block font-medium text-slate-600">{{ task.dueDate }}</time>
      </div>
      <button
        type="button"
        aria-label="删除任务"
        class="grid size-8 shrink-0 place-items-center rounded-md text-xl leading-none text-slate-400 transition-colors hover:bg-rose-50 hover:text-rose-600 focus-visible:outline-2 focus-visible:outline-rose-500"
        @click="emit('delete', task)"
      >
        ×
      </button>
    </div>
  </article>
</template>
