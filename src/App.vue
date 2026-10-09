<script setup lang="ts">
import type { Task } from './types/task'

const tasks: Task[] = [
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
]

const statusLabels = {
  todo: '待处理',
  'in-progress': '进行中',
  done: '已完成',
}

const priorityLabels = {
  low: '低优先级',
  medium: '中优先级',
  high: '高优先级',
}

const completedCount = tasks.filter((task) => task.status === 'done').length
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
        <div class="flex items-center gap-2 rounded-xl border border-slate-200 bg-white px-4 py-3 shadow-sm">
          <span class="grid size-8 place-items-center rounded-lg bg-indigo-50 text-sm font-bold text-indigo-700">{{ completedCount }}</span>
          <span class="text-sm text-slate-600">/{{ tasks.length }} 项已完成</span>
        </div>
      </section>

      <section aria-label="任务列表" class="overflow-hidden rounded-2xl border border-slate-200 bg-white shadow-sm shadow-slate-200/50">
        <div class="flex items-center justify-between border-b border-slate-100 px-5 py-4 sm:px-6">
          <div>
            <h2 class="m-0 text-base font-semibold text-slate-900">全部任务</h2>
            <p class="mb-0 mt-1 text-xs text-slate-500">{{ tasks.length }} 个任务</p>
          </div>
          <span class="rounded-full bg-indigo-50 px-3 py-1 text-xs font-medium text-indigo-700">最近更新</span>
        </div>

        <ul class="m-0 list-none divide-y divide-slate-100 p-0">
          <li v-for="task in tasks" :key="task.id" class="grid gap-4 px-5 py-5 transition-colors hover:bg-slate-50/70 sm:grid-cols-[minmax(0,1fr)_auto] sm:items-center sm:px-6">
            <div class="flex min-w-0 gap-3">
              <span
                class="mt-1 grid size-5 shrink-0 place-items-center rounded-full border text-[10px] font-bold"
                :class="task.status === 'done' ? 'border-emerald-500 bg-emerald-500 text-white' : 'border-slate-300 bg-white text-transparent'"
                aria-hidden="true"
              >✓</span>
              <div class="min-w-0">
                <h3 class="m-0 text-sm font-semibold text-slate-800" :class="task.status === 'done' ? 'text-slate-400 line-through' : ''">{{ task.title }}</h3>
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
            <div class="pl-8 text-xs text-slate-500 sm:pl-0 sm:text-right">
              <span class="block text-[11px] text-slate-400">截止日期</span>
              <time class="mt-1 block font-medium text-slate-600">{{ task.dueDate }}</time>
            </div>
          </li>
        </ul>
      </section>

      <p class="mb-0 mt-5 text-center text-xs text-slate-400">当前展示示例任务，后续可接入真实数据。</p>
    </main>
  </div>
</template>
