<script setup lang="ts">
import { computed, ref } from 'vue'
import KanbanBoard from './components/KanbanBoard.vue'
import TaskModal from './components/TaskModal.vue'
import TaskList from './components/TaskList.vue'
import ThemeToggle from './components/ThemeToggle.vue'
import { addTask, deleteTask, taskStore, updateTask } from './stores/taskStore'
import type { Task } from './types/task'

const isTaskModalOpen = ref(false)
const isMobileMenuOpen = ref(false)
const activeView = ref<'list' | 'kanban'>('list')

const completedCount = computed(() => taskStore.tasks.filter((task) => task.status === 'done').length)

function toggleTask(updatedTask: Task) {
  updateTask({
    ...updatedTask,
    status: updatedTask.status === 'done' ? 'todo' : 'done',
  })
}

function moveTask(payload: { taskId: string; status: Task['status'] }) {
  const task = taskStore.tasks.find((item) => item.id === payload.taskId)
  if (task) updateTask({ ...task, status: payload.status })
}

function selectView(view: 'list' | 'kanban') {
  activeView.value = view
  isMobileMenuOpen.value = false
}
</script>

<template>
  <div class="min-h-screen bg-[#f7f8fc] text-left text-slate-800 transition-colors dark:bg-slate-950 dark:text-slate-100">
    <header class="border-b border-slate-200/80 bg-white dark:border-slate-800 dark:bg-slate-900">
      <div class="mx-auto max-w-6xl px-5 sm:px-8">
        <div class="flex h-16 items-center justify-between">
          <a href="#" class="flex items-center gap-3 text-slate-900 no-underline dark:text-slate-100">
            <span class="grid size-9 place-items-center rounded-xl bg-indigo-600 text-sm font-bold text-white shadow-sm shadow-indigo-200">V</span>
            <span class="text-base font-semibold">Vibe Coding Runoob</span>
          </a>
          <div class="flex items-center gap-2 sm:gap-3">
            <span class="hidden text-sm text-slate-500 dark:text-slate-400 sm:inline">个人工作区</span>
            <span class="hidden size-9 place-items-center rounded-full bg-indigo-50 text-sm font-semibold text-indigo-700 dark:bg-indigo-950 dark:text-indigo-300 sm:grid">VC</span>
            <ThemeToggle />
            <button
              type="button"
              class="grid size-11 place-items-center rounded-md text-slate-600 hover:bg-slate-100 focus-visible:outline-2 focus-visible:outline-indigo-500 dark:text-slate-200 dark:hover:bg-slate-800 md:hidden"
              :aria-expanded="isMobileMenuOpen"
              aria-controls="mobile-navigation"
              :aria-label="isMobileMenuOpen ? '关闭导航菜单' : '打开导航菜单'"
              @click="isMobileMenuOpen = !isMobileMenuOpen"
            >
              <span aria-hidden="true" class="text-2xl leading-none">{{ isMobileMenuOpen ? '×' : '☰' }}</span>
            </button>
          </div>
        </div>
        <nav
          v-if="isMobileMenuOpen"
          id="mobile-navigation"
          aria-label="任务视图导航"
          class="space-y-1 border-t border-slate-100 py-2 dark:border-slate-800 md:hidden"
        >
          <button
            type="button"
            class="min-h-11 w-full rounded-md px-3 text-left text-sm font-medium"
            :class="activeView === 'list' ? 'bg-indigo-50 text-indigo-700 dark:bg-indigo-950 dark:text-indigo-300' : 'text-slate-600 hover:bg-slate-50 dark:text-slate-300 dark:hover:bg-slate-800'"
            :aria-current="activeView === 'list' ? 'page' : undefined"
            @click="selectView('list')"
          >
            列表
          </button>
          <button
            type="button"
            class="min-h-11 w-full rounded-md px-3 text-left text-sm font-medium"
            :class="activeView === 'kanban' ? 'bg-indigo-50 text-indigo-700 dark:bg-indigo-950 dark:text-indigo-300' : 'text-slate-600 hover:bg-slate-50 dark:text-slate-300 dark:hover:bg-slate-800'"
            :aria-current="activeView === 'kanban' ? 'page' : undefined"
            @click="selectView('kanban')"
          >
            看板
          </button>
        </nav>
      </div>
    </header>

    <main class="mx-auto max-w-6xl px-5 py-10 sm:px-8 sm:py-14">
      <section class="mb-8 flex flex-col justify-between gap-5 sm:flex-row sm:items-end">
        <div>
          <p class="mb-2 text-sm font-semibold text-indigo-600 dark:text-indigo-300">任务空间</p>
          <h1 class="m-0 text-3xl font-semibold text-slate-900 dark:text-slate-100">我的任务</h1>
          <p class="mb-0 mt-2 text-sm leading-6 text-slate-500 dark:text-slate-400">把想法拆解成下一步，逐项推进。</p>
        </div>
        <div class="flex flex-wrap items-center gap-3">
          <div class="flex items-center gap-2 rounded-xl border border-slate-200 bg-white px-4 py-3 shadow-sm dark:border-slate-700 dark:bg-slate-900">
            <span class="grid size-8 place-items-center rounded-lg bg-indigo-50 text-sm font-bold text-indigo-700 dark:bg-indigo-950 dark:text-indigo-300">{{ completedCount }}</span>
            <span class="text-sm text-slate-600 dark:text-slate-300">/{{ taskStore.tasks.length }} 项已完成</span>
          </div>
          <TaskModal v-model="isTaskModalOpen" @submit="addTask" />
        </div>
      </section>

      <div class="mb-5 hidden rounded-lg bg-slate-100 p-1 dark:bg-slate-800 md:inline-flex" role="tablist" aria-label="任务视图">
        <button
          type="button"
          role="tab"
          class="min-h-11 rounded-md px-4 text-sm font-medium transition-colors focus-visible:outline-2 focus-visible:outline-indigo-500"
          :class="activeView === 'list' ? 'bg-white text-slate-900 shadow-sm dark:bg-slate-700 dark:text-slate-100' : 'text-slate-500 hover:text-slate-800 dark:text-slate-400 dark:hover:text-slate-100'"
          :aria-selected="activeView === 'list'"
          @click="selectView('list')"
        >
          列表
        </button>
        <button
          type="button"
          role="tab"
          class="min-h-11 rounded-md px-4 text-sm font-medium transition-colors focus-visible:outline-2 focus-visible:outline-indigo-500"
          :class="activeView === 'kanban' ? 'bg-white text-slate-900 shadow-sm dark:bg-slate-700 dark:text-slate-100' : 'text-slate-500 hover:text-slate-800 dark:text-slate-400 dark:hover:text-slate-100'"
          :aria-selected="activeView === 'kanban'"
          @click="selectView('kanban')"
        >
          看板
        </button>
      </div>

      <TaskList
        v-if="activeView === 'list'"
        :tasks="taskStore.tasks"
        @move="moveTask"
        @toggle="toggleTask"
        @delete="deleteTask"
      />
      <KanbanBoard
        v-else
        :tasks="taskStore.tasks"
        @move="moveTask"
        @toggle="toggleTask"
        @delete="deleteTask"
      />
    </main>
  </div>
</template>
