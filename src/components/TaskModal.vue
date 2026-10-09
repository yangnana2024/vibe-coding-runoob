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
    class="inline-flex items-center gap-2 rounded-lg bg-indigo-600 px-4 py-2.5 text-sm font-semibold text-white shadow-sm transition-colors hover:bg-indigo-700 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
    @click="emit('update:modelValue', true)"
  >
    <span aria-hidden="true" class="text-lg leading-none">+</span>
    新建任务
  </button>

  <Teleport to="body">
      <div
        v-if="modelValue"
        class="fixed inset-0 z-50 grid place-items-center overflow-y-auto bg-slate-950/45 p-4"
        @click.self="closeModal"
      >
        <section
          role="dialog"
          aria-modal="true"
          aria-labelledby="task-modal-title"
          class="my-auto w-full max-w-lg rounded-xl bg-white p-6 shadow-2xl"
        >
          <div class="mb-6 flex items-start justify-between gap-4">
            <div>
              <h2 id="task-modal-title" class="m-0 text-xl font-semibold text-slate-900">新建任务</h2>
              <p class="mb-0 mt-1 text-sm text-slate-500">填写任务信息，稍后也可以继续调整。</p>
            </div>
            <button
              type="button"
              aria-label="关闭弹窗"
              class="grid size-8 shrink-0 place-items-center rounded-md text-xl leading-none text-slate-400 hover:bg-slate-100 hover:text-slate-700 focus-visible:outline-2 focus-visible:outline-indigo-500"
              @click="closeModal"
            >
              ×
            </button>
          </div>

          <form class="space-y-5" @submit.prevent="submitTask">
            <div>
              <label for="task-title" class="mb-1.5 block text-sm font-medium text-slate-700">标题</label>
              <input
                id="task-title"
                v-model="form.title"
                type="text"
                aria-required="true"
                :aria-invalid="titleError"
                aria-describedby="task-title-error"
                class="w-full rounded-md border px-3 py-2 text-sm text-slate-900 outline-none transition focus:border-indigo-500 focus:ring-2 focus:ring-indigo-100"
                :class="titleError ? 'border-rose-500' : 'border-slate-300'"
                placeholder="输入任务标题"
                @input="titleError = false"
              />
              <p v-if="titleError" id="task-title-error" class="mb-0 mt-1.5 text-sm text-rose-600">
                标题不能为空
              </p>
            </div>

            <div>
              <label for="task-description" class="mb-1.5 block text-sm font-medium text-slate-700">描述（选填）</label>
              <textarea
                id="task-description"
                v-model="form.description"
                rows="3"
                class="w-full resize-y rounded-md border border-slate-300 px-3 py-2 text-sm text-slate-900 outline-none transition focus:border-indigo-500 focus:ring-2 focus:ring-indigo-100"
                placeholder="补充任务细节"
              />
            </div>

            <div>
              <label for="task-priority" class="mb-1.5 block text-sm font-medium text-slate-700">优先级</label>
              <select
                id="task-priority"
                v-model="form.priority"
                class="w-full rounded-md border border-slate-300 bg-white px-3 py-2 text-sm text-slate-900 outline-none transition focus:border-indigo-500 focus:ring-2 focus:ring-indigo-100"
              >
                <option value="high">高优先级</option>
                <option value="medium">中优先级</option>
                <option value="low">低优先级</option>
              </select>
            </div>

            <div class="flex justify-end gap-2 border-t border-slate-100 pt-4">
              <button
                type="button"
                class="rounded-md border border-slate-300 px-4 py-2 text-sm font-medium text-slate-700 hover:bg-slate-50 focus-visible:outline-2 focus-visible:outline-indigo-500"
                @click="closeModal"
              >
                取消
              </button>
              <button
                type="submit"
                class="rounded-md bg-indigo-600 px-4 py-2 text-sm font-semibold text-white hover:bg-indigo-700 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
              >
                创建任务
              </button>
            </div>
          </form>
        </section>
      </div>
  </Teleport>
</template>
