<script setup lang="ts">
import { onMounted, onUnmounted, reactive, ref } from 'vue'
import type { Task, TaskPriority } from '../types/task'

const props = defineProps<{
  modelValue: boolean
}>()

const emit = defineEmits<{
  'update:modelValue': [isOpen: boolean]
  submit: [task: Task]
}>()

const titleError = ref(false)
const form = reactive({
  title: '',
  description: '',
  priority: 'medium' as TaskPriority,
})

function closeModal() {
  emit('update:modelValue', false)
  titleError.value = false
}

function handleKeydown(event: KeyboardEvent) {
  if (event.key === 'Escape' && props.modelValue) closeModal()
}

function submitTask() {
  const title = form.title.trim()
  if (!title) {
    titleError.value = true
    return
  }

  const now = new Date()
  const localDate = new Date(now.getTime() - now.getTimezoneOffset() * 60_000)
    .toISOString()
    .slice(0, 10)

  emit('submit', {
    id: crypto.randomUUID(),
    title,
    description: form.description.trim(),
    status: 'todo',
    priority: form.priority,
    dueDate: localDate,
    createdAt: now.toISOString(),
  })

  form.title = ''
  form.description = ''
  form.priority = 'medium'
  closeModal()
}

onMounted(() => window.addEventListener('keydown', handleKeydown))
onUnmounted(() => window.removeEventListener('keydown', handleKeydown))
</script>

<template>
  <button
    type="button"
    class="inline-flex min-h-11 items-center gap-2 rounded-lg bg-indigo-600 px-4 text-sm font-semibold text-white shadow-sm transition-colors hover:bg-indigo-700 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
    @click="emit('update:modelValue', true)"
  >
    <span aria-hidden="true" class="text-lg leading-none">+</span>
    新建任务
  </button>

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
        v-if="modelValue"
        class="fixed inset-0 z-50 grid items-end overflow-y-auto bg-slate-950/45 p-0 sm:place-items-center sm:p-4"
        @click.self="closeModal"
      >
        <Transition
          appear
          enter-active-class="transition-transform duration-300 ease-out"
          enter-from-class="translate-y-full sm:translate-y-3"
          enter-to-class="translate-y-0"
          leave-active-class="transition-transform duration-200 ease-in"
          leave-from-class="translate-y-0"
          leave-to-class="translate-y-full sm:translate-y-3"
        >
        <section
          role="dialog"
          aria-modal="true"
          aria-labelledby="task-modal-title"
          class="max-h-[90dvh] w-full overflow-y-auto rounded-t-xl rounded-b-none bg-white p-5 shadow-2xl dark:bg-slate-900 sm:my-auto sm:max-w-lg sm:rounded-xl sm:p-6"
        >
          <div class="mb-6 flex items-start justify-between gap-4">
            <div>
              <h2 id="task-modal-title" class="m-0 text-xl font-semibold text-slate-900 dark:text-slate-100">新建任务</h2>
              <p class="mb-0 mt-1 text-sm text-slate-500 dark:text-slate-400">填写任务信息，稍后也可以继续调整。</p>
            </div>
            <button
              type="button"
              aria-label="关闭弹窗"
              class="grid size-11 shrink-0 place-items-center rounded-md text-xl leading-none text-slate-400 hover:bg-slate-100 hover:text-slate-700 focus-visible:outline-2 focus-visible:outline-indigo-500 dark:text-slate-500 dark:hover:bg-slate-800 dark:hover:text-slate-200"
              @click="closeModal"
            >
              ×
            </button>
          </div>

          <form class="space-y-5" @submit.prevent="submitTask">
            <div>
              <label for="task-title" class="mb-1.5 block text-sm font-medium text-slate-700 dark:text-slate-300">标题</label>
              <input
                id="task-title"
                v-model="form.title"
                type="text"
                aria-required="true"
                :aria-invalid="titleError"
                aria-describedby="task-title-error"
                class="w-full rounded-md border bg-white px-3 py-2 text-base text-slate-900 outline-none transition placeholder:text-slate-400 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-100 dark:bg-slate-950 dark:text-slate-100 dark:placeholder:text-slate-500"
                :class="titleError ? 'border-rose-500 dark:border-rose-400' : 'border-slate-300 dark:border-slate-700'"
                placeholder="输入任务标题"
                @input="titleError = false"
              />
              <p v-if="titleError" id="task-title-error" class="mb-0 mt-1.5 text-sm text-rose-600">
                标题不能为空
              </p>
            </div>

            <div>
              <label for="task-description" class="mb-1.5 block text-sm font-medium text-slate-700 dark:text-slate-300">描述（选填）</label>
              <textarea
                id="task-description"
                v-model="form.description"
                rows="3"
                class="w-full resize-y rounded-md border border-slate-300 bg-white px-3 py-2 text-base text-slate-900 outline-none transition placeholder:text-slate-400 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-100 dark:border-slate-700 dark:bg-slate-950 dark:text-slate-100 dark:placeholder:text-slate-500"
                placeholder="补充任务细节"
              />
            </div>

            <div>
              <label for="task-priority" class="mb-1.5 block text-sm font-medium text-slate-700 dark:text-slate-300">优先级</label>
              <select
                id="task-priority"
                v-model="form.priority"
                class="w-full rounded-md border border-slate-300 bg-white px-3 py-2 text-base text-slate-900 outline-none transition focus:border-indigo-500 focus:ring-2 focus:ring-indigo-100 dark:border-slate-700 dark:bg-slate-950 dark:text-slate-100"
              >
                <option value="high">高优先级</option>
                <option value="medium">中优先级</option>
                <option value="low">低优先级</option>
              </select>
            </div>

            <div class="flex justify-end gap-2 border-t border-slate-100 pt-4 dark:border-slate-800">
              <button
                type="button"
                class="min-h-11 rounded-md border border-slate-300 px-4 text-sm font-medium text-slate-700 hover:bg-slate-50 focus-visible:outline-2 focus-visible:outline-indigo-500 dark:border-slate-700 dark:text-slate-300 dark:hover:bg-slate-800"
                @click="closeModal"
              >
                取消
              </button>
              <button
                type="submit"
                class="min-h-11 rounded-md bg-indigo-600 px-4 text-sm font-semibold text-white hover:bg-indigo-700 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
              >
                创建任务
              </button>
            </div>
          </form>
        </section>
        </Transition>
      </div>
    </Transition>
  </Teleport>
</template>
