<script setup lang="ts">
import { nextTick, ref } from 'vue'
import type { Task, TaskStatus } from '../types/task'

defineProps<{
  task: Task
}>()

const emit = defineEmits<{
  toggle: [task: Task]
  delete: [task: Task]
  move: [payload: { taskId: string; status: TaskStatus }]
  edit: [task: Task]
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

const statusOptions: { label: string; value: TaskStatus }[] = [
  { label: '待办', value: 'todo' },
  { label: '进行中', value: 'in-progress' },
  { label: '已完成', value: 'done' },
]

const isStatusPickerOpen = ref(false)
const statusPickerTrigger = ref<HTMLButtonElement | null>(null)
const firstStatusOption = ref<HTMLButtonElement | null>(null)
const isDeleteConfirmOpen = ref(false)
const deleteTrigger = ref<HTMLButtonElement | null>(null)
const cancelDeleteButton = ref<HTMLButtonElement | null>(null)

function openStatusPicker() {
  isStatusPickerOpen.value = true
  nextTick(() => firstStatusOption.value?.focus())
}

function closeStatusPicker() {
  isStatusPickerOpen.value = false
  nextTick(() => statusPickerTrigger.value?.focus())
}

function changeStatus(status: TaskStatus, task: Task) {
  emit('move', { taskId: task.id, status })
  closeStatusPicker()
}

function openDeleteConfirm() {
  isDeleteConfirmOpen.value = true
  nextTick(() => cancelDeleteButton.value?.focus())
}

function closeDeleteConfirm() {
  isDeleteConfirmOpen.value = false
  nextTick(() => deleteTrigger.value?.focus())
}

function confirmDelete(task: Task) {
  isDeleteConfirmOpen.value = false
  emit('delete', task)
}
</script>

<template>
  <article
    class="grid gap-4 rounded-md border border-slate-200 border-l-4 bg-white px-4 py-4 shadow-sm transition-transform duration-200 hover:scale-[1.02] dark:border-slate-700 dark:bg-slate-900 dark:shadow-black/20 sm:grid-cols-[minmax(0,1fr)_auto] sm:items-center"
    :class="{
      'border-l-rose-500 dark:border-l-rose-400': task.priority === 'high',
      'border-l-amber-400 dark:border-l-amber-300': task.priority === 'medium',
      'border-l-emerald-500 dark:border-l-emerald-400': task.priority === 'low',
    }"
  >
    <div class="flex min-w-0 gap-3">
      <label class="grid size-11 shrink-0 cursor-pointer place-items-center" :aria-label="task.status === 'done' ? '标记为待办' : '标记为完成'">
        <input
          type="checkbox"
          :checked="task.status === 'done'"
          :aria-label="task.status === 'done' ? '标记为待办' : '标记为完成'"
          class="size-5 cursor-pointer accent-emerald-600"
          @change="emit('toggle', task)"
        />
      </label>
      <div class="min-w-0">
        <h3
          class="m-0 text-sm font-semibold text-slate-800 dark:text-slate-100"
          :class="task.status === 'done' ? 'text-slate-400 line-through dark:text-slate-500' : ''"
        >
          {{ task.title }}
        </h3>
        <p class="mb-0 mt-1.5 text-sm leading-5 text-slate-500 dark:text-slate-400">{{ task.description }}</p>
        <div class="mt-3 flex flex-wrap items-center gap-2">
          <button
            ref="statusPickerTrigger"
            type="button"
            class="inline-flex min-h-11 min-w-11 items-center justify-center rounded-md p-0 focus-visible:outline-2 focus-visible:outline-indigo-500 sm:hidden"
            :aria-label="`更改“${task.title}”的状态，当前为${statusLabels[task.status]}`"
            aria-haspopup="dialog"
            @click="openStatusPicker"
          >
            <span
              class="rounded-md px-2 py-1 text-xs font-medium"
              :class="{
                'bg-slate-100 text-slate-600 dark:bg-slate-800 dark:text-slate-300': task.status === 'todo',
                'bg-indigo-50 text-indigo-700 dark:bg-indigo-950 dark:text-indigo-300': task.status === 'in-progress',
                'bg-emerald-50 text-emerald-700 dark:bg-emerald-950 dark:text-emerald-300': task.status === 'done',
              }"
            >{{ statusLabels[task.status] }}</span>
          </button>
          <span
            class="hidden rounded-md px-2 py-1 text-xs font-medium sm:inline-flex"
            :class="{
              'bg-slate-100 text-slate-600 dark:bg-slate-800 dark:text-slate-300': task.status === 'todo',
              'bg-indigo-50 text-indigo-700 dark:bg-indigo-950 dark:text-indigo-300': task.status === 'in-progress',
              'bg-emerald-50 text-emerald-700 dark:bg-emerald-950 dark:text-emerald-300': task.status === 'done',
            }"
          >{{ statusLabels[task.status] }}</span>
          <span
            class="rounded-md px-2 py-1 text-xs font-medium"
            :class="{
              'bg-slate-100 text-slate-600 dark:bg-slate-800 dark:text-slate-300': task.priority === 'low',
              'bg-amber-50 text-amber-700 dark:bg-amber-950 dark:text-amber-300': task.priority === 'medium',
              'bg-rose-50 text-rose-700 dark:bg-rose-950 dark:text-rose-300': task.priority === 'high',
            }"
          >{{ priorityLabels[task.priority] }}</span>
        </div>
      </div>
    </div>

    <div class="flex items-center justify-between gap-4 pl-7 sm:justify-end sm:pl-0">
      <div class="text-xs text-slate-500 dark:text-slate-400 sm:text-right">
        <span class="block text-[11px] text-slate-400 dark:text-slate-500">截止日期</span>
        <time class="mt-1 block font-medium text-slate-600 dark:text-slate-300">{{ task.dueDate }}</time>
      </div>
      <div class="flex shrink-0 items-center gap-1">
        <button
          type="button"
          aria-label="编辑任务"
          title="编辑任务"
          class="grid size-11 place-items-center rounded-md text-xs font-medium text-slate-500 transition-colors hover:bg-slate-100 hover:text-slate-800 focus-visible:outline-2 focus-visible:outline-indigo-500 dark:text-slate-400 dark:hover:bg-slate-800 dark:hover:text-slate-100"
          @click="emit('edit', task)"
        >
          编辑
        </button>
        <button
          ref="deleteTrigger"
          type="button"
          aria-label="删除任务"
          class="grid size-11 place-items-center rounded-md text-xl leading-none text-slate-400 transition-colors hover:bg-rose-50 hover:text-rose-600 focus-visible:outline-2 focus-visible:outline-rose-500 dark:text-slate-500 dark:hover:bg-rose-950 dark:hover:text-rose-300"
          @click="openDeleteConfirm"
        >
          ×
        </button>
      </div>
    </div>
  </article>

  <Teleport to="body">
    <Transition
      enter-active-class="transition-opacity duration-200"
      enter-from-class="opacity-0"
      enter-to-class="opacity-100"
      leave-active-class="transition-opacity duration-200"
      leave-from-class="opacity-100"
      leave-to-class="opacity-0"
    >
      <div
        v-if="isStatusPickerOpen"
        class="fixed inset-0 z-50 grid items-end bg-slate-950/45 sm:hidden"
        @click.self="closeStatusPicker"
        @keydown.esc.stop.prevent="closeStatusPicker"
      >
        <section
          role="dialog"
          aria-modal="true"
          :aria-label="`更改“${task.title}”的状态`"
          class="rounded-t-xl bg-white p-5 pb-[max(1.25rem,env(safe-area-inset-bottom))] shadow-2xl dark:bg-slate-900"
        >
          <div class="mb-4 flex items-center justify-between">
            <h2 class="m-0 text-base font-semibold text-slate-900 dark:text-slate-100">更改任务状态</h2>
            <button
              type="button"
              class="grid size-11 place-items-center rounded-md text-xl text-slate-400 hover:bg-slate-100 dark:hover:bg-slate-800"
              aria-label="关闭状态选项"
              @click="closeStatusPicker"
            >
              ×
            </button>
          </div>
          <div class="grid grid-cols-1 gap-2">
            <button
              v-for="(option, index) in statusOptions"
              :key="option.value"
              :ref="index === 0 ? (element) => { firstStatusOption = element as HTMLButtonElement | null } : undefined"
              type="button"
              class="flex min-h-11 items-center justify-between rounded-md border px-4 text-left text-sm font-medium transition-colors"
              :class="task.status === option.value
                ? 'border-indigo-200 bg-indigo-50 text-indigo-700 dark:border-indigo-900 dark:bg-indigo-950 dark:text-indigo-300'
                : 'border-slate-200 text-slate-700 hover:bg-slate-50 dark:border-slate-700 dark:text-slate-200 dark:hover:bg-slate-800'"
              :aria-pressed="task.status === option.value"
              @click="changeStatus(option.value, task)"
            >
              {{ option.label }}
              <span v-if="task.status === option.value" aria-hidden="true">当前</span>
            </button>
          </div>
        </section>
      </div>
    </Transition>
  </Teleport>

  <Teleport to="body">
    <Transition
      enter-active-class="transition-opacity duration-150"
      enter-from-class="opacity-0"
      enter-to-class="opacity-100"
      leave-active-class="transition-opacity duration-150"
      leave-from-class="opacity-100"
      leave-to-class="opacity-0"
    >
      <div
        v-if="isDeleteConfirmOpen"
        class="fixed inset-0 z-[60] grid place-items-center bg-slate-950/45 p-4"
        @click.self="closeDeleteConfirm"
        @keydown.esc.stop.prevent="closeDeleteConfirm"
      >
        <section
          role="alertdialog"
          aria-modal="true"
          :aria-labelledby="`delete-title-${task.id}`"
          :aria-describedby="`delete-description-${task.id}`"
          class="w-full max-w-sm rounded-lg border border-slate-200 bg-white p-5 shadow-2xl dark:border-slate-700 dark:bg-slate-900"
        >
          <h2 :id="`delete-title-${task.id}`" class="m-0 text-base font-semibold text-slate-900 dark:text-slate-100">确定删除任务？</h2>
          <p :id="`delete-description-${task.id}`" class="mb-0 mt-2 break-words text-sm leading-6 text-slate-600 dark:text-slate-300">
            “{{ task.title }}”删除后无法恢复。
          </p>
          <div class="mt-5 flex justify-end gap-2">
            <button
              ref="cancelDeleteButton"
              type="button"
              class="min-h-11 rounded-md border border-slate-300 px-4 text-sm font-medium text-slate-700 hover:bg-slate-50 focus-visible:outline-2 focus-visible:outline-indigo-500 dark:border-slate-700 dark:text-slate-300 dark:hover:bg-slate-800"
              @click="closeDeleteConfirm"
            >
              取消
            </button>
            <button
              type="button"
              class="min-h-11 rounded-md bg-rose-600 px-4 text-sm font-semibold text-white hover:bg-rose-700 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-rose-600"
              @click="confirmDelete(task)"
            >
              确认删除
            </button>
          </div>
        </section>
      </div>
    </Transition>
  </Teleport>
</template>
