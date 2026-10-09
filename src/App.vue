<script setup lang="ts">
import { computed, ref } from 'vue'
import TaskModal from './components/TaskModal.vue'
import TaskList from './components/TaskList.vue'
import type { Task } from './types/task'

const tasks = ref<Task[]>([
  {
    id: 'task-1',
    title: '梳理产品需求与用户流程',
    description: '整理核心使用场景，明确任务列表的第一版功能范围。',
    status: 'in-progress',
    priority: 'high',
    dueDate: '2026-10-10',
    createdAt: '2026-10-06',
  },
  {
    id: 'task-2',
    title: '搭建 Vue 项目基础结构',
    description: '完成页面入口、组件组织方式和 Tailwind CSS 配置。',
    status: 'done',
    priority: 'medium',
    dueDate: '2026-10-08',
    createdAt: '2026-10-05',
  },
  {
    id: 'task-3',
    title: '设计任务卡片样式',
    description: '为不同状态与优先级提供清晰、易扫读的视觉标记。',
    status: 'todo',
    priority: 'low',
    dueDate: '2026-10-13',
    createdAt: '2026-10-07',
  },
])

const completedCount = computed(() => tasks.value.filter((task) => task.status === 'done').length)

function toggleTask(updatedTask: Task) {
  const task = tasks.value.find((item) => item.id === updatedTask.id)
  if (task) task.status = task.status === 'done' ? 'todo' : 'done'
}

function deleteTask(deletedTask: Task) {
  tasks.value = tasks.value.filter((task) => task.id !== deletedTask.id)
}

function addTask(task: Task) {
  tasks.value.push(task)
}
</script>

<template>
  <div class="min-h-screen bg-[#f7f8fc] text-left text-slate-800">
    <header class="border-b border-slate-200/80 bg-white">
      <div class="mx-auto flex h-16 max-w-6xl items-center justify-between px-5 sm:px-8">
        <a href="#" class="flex items-center gap-3 text-slate-900 no-underline">
          <span class="grid size-9 place-items-center rounded-xl bg-indigo-600 text-sm font-bold text-white shadow-sm shadow-indigo-200">V</span>
          <span class="text-base font-semibold">Vibe Coding Runoob</span>
        </a>
        <div class="flex items-center gap-3">
          <span class="hidden text-sm text-slate-500 sm:inline">个人工作区</span>
          <span class="grid size-9 place-items-center rounded-full bg-indigo-50 text-sm font-semibold text-indigo-700">VC</span>
        </div>
      </div>
    </header>

    <main class="mx-auto max-w-6xl px-5 py-10 sm:px-8 sm:py-14">
      <section class="mb-8 flex flex-col justify-between gap-5 sm:flex-row sm:items-end">
        <div>
          <p class="mb-2 text-sm font-semibold text-indigo-600">任务空间</p>
          <h1 class="m-0 text-3xl font-semibold text-slate-900">我的任务</h1>
          <p class="mb-0 mt-2 text-sm leading-6 text-slate-500">把想法拆解成下一步，逐项推进。</p>
        </div>
        <div class="flex flex-wrap items-center gap-3">
          <div class="flex items-center gap-2 rounded-xl border border-slate-200 bg-white px-4 py-3 shadow-sm">
            <span class="grid size-8 place-items-center rounded-lg bg-indigo-50 text-sm font-bold text-indigo-700">{{ completedCount }}</span>
            <span class="text-sm text-slate-600">/{{ tasks.length }} 项已完成</span>
          </div>
          <TaskModal @submit="addTask" />
        </div>
      </section>

      <TaskList :tasks="tasks" @toggle="toggleTask" @delete="deleteTask" />
    </main>
  </div>
</template>
