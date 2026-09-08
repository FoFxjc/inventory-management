<template>
  <div class="app-shell">
    <div
      v-if="sidebarOpen"
      class="sidebar-backdrop"
      @click="sidebarOpen = false"
    ></div>

    <aside class="sidebar" :class="{ 'is-open': sidebarOpen, 'is-collapsed': sidebarCollapsed }">
      <div class="sidebar-brand">
        <div class="brand-mark">
          <svg viewBox="0 0 20 20" width="22" height="22" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <path d="M10 2L17 6V14L10 18L3 14V6L10 2Z" />
            <path d="M10 10L17 6" />
            <path d="M10 10V18" />
            <path d="M10 10L3 6" />
          </svg>
        </div>
        <div class="brand-text">
          <h1>{{ t('nav.companyName') }}</h1>
          <span class="subtitle">{{ t('nav.subtitle') }}</span>
        </div>
        <button
          class="sidebar-collapse-btn"
          @click="toggleSidebarCollapsed"
          :aria-label="sidebarCollapsed ? 'Expand sidebar' : 'Collapse sidebar'"
          :title="sidebarCollapsed ? 'Expand sidebar' : 'Collapse sidebar'"
        >
          <svg
            class="sidebar-collapse-icon"
            :class="{ 'is-collapsed': sidebarCollapsed }"
            viewBox="0 0 20 20"
            width="20"
            height="20"
            fill="none"
            stroke="currentColor"
            stroke-width="1.75"
            stroke-linecap="round"
            stroke-linejoin="round"
          >
            <path d="M12.5 4L7 10L12.5 16" />
          </svg>
        </button>
      </div>

      <nav class="sidebar-nav">
        <router-link
          to="/"
          class="nav-item"
          :class="{ active: $route.path === '/' }"
          :title="t('nav.overview')"
          @click="sidebarOpen = false"
        >
          <svg class="nav-icon" viewBox="0 0 20 20" width="20" height="20" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <path d="M3.5 14.5a6.5 6.5 0 0 1 13 0" />
            <path d="M10 14.5L13 9" />
          </svg>
          <span>{{ t('nav.overview') }}</span>
        </router-link>

        <router-link
          to="/inventory"
          class="nav-item"
          :class="{ active: $route.path === '/inventory' }"
          :title="t('nav.inventory')"
          @click="sidebarOpen = false"
        >
          <svg class="nav-icon" viewBox="0 0 20 20" width="20" height="20" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <rect x="3" y="3" width="14" height="5.5" rx="1" />
            <rect x="3" y="11.5" width="14" height="5.5" rx="1" />
          </svg>
          <span>{{ t('nav.inventory') }}</span>
        </router-link>

        <router-link
          to="/orders"
          class="nav-item"
          :class="{ active: $route.path === '/orders' }"
          :title="t('nav.orders')"
          @click="sidebarOpen = false"
        >
          <svg class="nav-icon" viewBox="0 0 20 20" width="20" height="20" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <rect x="4" y="3.5" width="12" height="14" rx="1.5" />
            <path d="M7.5 3.5V3a1.5 1.5 0 0 1 1.5-1.5h2A1.5 1.5 0 0 1 12.5 3v.5" />
            <path d="M7 10.5L9 12.5L13 8" />
          </svg>
          <span>{{ t('nav.orders') }}</span>
        </router-link>

        <router-link
          to="/spending"
          class="nav-item"
          :class="{ active: $route.path === '/spending' }"
          :title="t('nav.finance')"
          @click="sidebarOpen = false"
        >
          <svg class="nav-icon" viewBox="0 0 20 20" width="20" height="20" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <ellipse cx="10" cy="5" rx="6" ry="2.25" />
            <path d="M4 5V15C4 16.2426 6.68629 17.25 10 17.25C13.3137 17.25 16 16.2426 16 15V5" />
            <path d="M4 10C4 11.2426 6.68629 12.25 10 12.25C13.3137 12.25 16 11.2426 16 10" />
          </svg>
          <span>{{ t('nav.finance') }}</span>
        </router-link>

        <router-link
          to="/demand"
          class="nav-item"
          :class="{ active: $route.path === '/demand' }"
          :title="t('nav.demandForecast')"
          @click="sidebarOpen = false"
        >
          <svg class="nav-icon" viewBox="0 0 20 20" width="20" height="20" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <polyline points="3,13 8,8 11.5,11.5 17,5" />
            <polyline points="12.5,5 17,5 17,9.5" />
          </svg>
          <span>{{ t('nav.demandForecast') }}</span>
        </router-link>

        <router-link
          to="/reports"
          class="nav-item"
          :class="{ active: $route.path === '/reports' }"
          :title="t('nav.reports')"
          @click="sidebarOpen = false"
        >
          <svg class="nav-icon" viewBox="0 0 20 20" width="20" height="20" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <path d="M3 17V3" />
            <rect x="6" y="10" width="2.75" height="7" />
            <rect x="10.5" y="6" width="2.75" height="11" />
            <rect x="15" y="12.5" width="2.75" height="4.5" />
          </svg>
          <span>{{ t('nav.reports') }}</span>
        </router-link>
      </nav>

      <div class="sidebar-footer">
        <ProfileMenu
          :collapsed="sidebarCollapsed"
          @show-profile-details="showProfileDetails = true"
          @show-tasks="showTasks = true"
        />
      </div>
    </aside>

    <div class="app-main" :class="{ 'is-collapsed': sidebarCollapsed }">
      <header class="topbar">
        <button
          class="hamburger-btn"
          @click="sidebarOpen = !sidebarOpen"
          aria-label="Toggle navigation"
        >
          <svg viewBox="0 0 20 20" width="20" height="20" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round">
            <path d="M3 5.5H17" />
            <path d="M3 10H17" />
            <path d="M3 14.5H17" />
          </svg>
        </button>

        <h2 class="topbar-title">{{ currentPageTitle }}</h2>

        <LanguageSwitcher class="topbar-language-switcher" />
      </header>

      <FilterBar />

      <main class="main-content">
        <router-view />
      </main>
    </div>

    <ProfileDetailsModal
      :is-open="showProfileDetails"
      @close="showProfileDetails = false"
    />

    <TasksModal
      :is-open="showTasks"
      :tasks="tasks"
      @close="showTasks = false"
      @add-task="addTask"
      @delete-task="deleteTask"
      @toggle-task="toggleTask"
    />
  </div>
</template>

<script>
import { ref, onMounted, computed } from 'vue'
import { useRoute } from 'vue-router'
import { api } from './api'
import { useAuth } from './composables/useAuth'
import { useI18n } from './composables/useI18n'
import FilterBar from './components/FilterBar.vue'
import ProfileMenu from './components/ProfileMenu.vue'
import ProfileDetailsModal from './components/ProfileDetailsModal.vue'
import TasksModal from './components/TasksModal.vue'
import LanguageSwitcher from './components/LanguageSwitcher.vue'

export default {
  name: 'App',
  components: {
    FilterBar,
    ProfileMenu,
    ProfileDetailsModal,
    TasksModal,
    LanguageSwitcher
  },
  setup() {
    const { currentUser } = useAuth()
    const { t } = useI18n()
    const route = useRoute()
    const showProfileDetails = ref(false)
    const showTasks = ref(false)
    const sidebarOpen = ref(false)
    const apiTasks = ref([])

    const getInitialSidebarCollapsed = () => {
      try {
        return localStorage.getItem('sidebarCollapsed') === 'true'
      } catch (err) {
        return false
      }
    }
    const sidebarCollapsed = ref(getInitialSidebarCollapsed())

    const toggleSidebarCollapsed = () => {
      sidebarCollapsed.value = !sidebarCollapsed.value
      try {
        localStorage.setItem('sidebarCollapsed', String(sidebarCollapsed.value))
      } catch (err) {
        // non-fatal if localStorage is unavailable
      }
    }

    const pageTitles = {
      '/': () => t('nav.overview'),
      '/inventory': () => t('nav.inventory'),
      '/orders': () => t('nav.orders'),
      '/spending': () => t('nav.finance'),
      '/demand': () => t('nav.demandForecast'),
      '/reports': () => t('nav.reports')
    }

    const currentPageTitle = computed(() => {
      const getTitle = pageTitles[route.path]
      return getTitle ? getTitle() : ''
    })

    // Merge mock tasks from currentUser with API tasks
    const tasks = computed(() => {
      return [...currentUser.value.tasks, ...apiTasks.value]
    })

    const loadTasks = async () => {
      try {
        apiTasks.value = await api.getTasks()
      } catch (err) {
        console.error('Failed to load tasks:', err)
      }
    }

    const addTask = async (taskData) => {
      try {
        const newTask = await api.createTask(taskData)
        // Add new task to the beginning of the array
        apiTasks.value.unshift(newTask)
      } catch (err) {
        console.error('Failed to add task:', err)
      }
    }

    const deleteTask = async (taskId) => {
      try {
        // Check if it's a mock task (from currentUser)
        const isMockTask = currentUser.value.tasks.some(t => t.id === taskId)

        if (isMockTask) {
          // Remove from mock tasks
          const index = currentUser.value.tasks.findIndex(t => t.id === taskId)
          if (index !== -1) {
            currentUser.value.tasks.splice(index, 1)
          }
        } else {
          // Remove from API tasks
          await api.deleteTask(taskId)
          apiTasks.value = apiTasks.value.filter(t => t.id !== taskId)
        }
      } catch (err) {
        console.error('Failed to delete task:', err)
      }
    }

    const toggleTask = async (taskId) => {
      try {
        // Check if it's a mock task (from currentUser)
        const mockTask = currentUser.value.tasks.find(t => t.id === taskId)

        if (mockTask) {
          // Toggle mock task status
          mockTask.status = mockTask.status === 'pending' ? 'completed' : 'pending'
        } else {
          // Toggle API task
          const updatedTask = await api.toggleTask(taskId)
          const index = apiTasks.value.findIndex(t => t.id === taskId)
          if (index !== -1) {
            apiTasks.value[index] = updatedTask
          }
        }
      } catch (err) {
        console.error('Failed to toggle task:', err)
      }
    }

    onMounted(loadTasks)

    return {
      t,
      showProfileDetails,
      showTasks,
      sidebarOpen,
      sidebarCollapsed,
      toggleSidebarCollapsed,
      currentPageTitle,
      tasks,
      addTask,
      deleteTask,
      toggleTask
    }
  }
}
</script>

<style>
:root {
  --bg: #f8fafc;
  --surface: #ffffff;
  --surface-sunken: #f1f5f9;
  --border: #e2e8f0;
  --border-strong: #cbd5e1;

  --ink: #0f172a;
  --ink-soft: #334155;
  --muted: #64748b;
  --muted-soft: #94a3b8;

  --accent: #2563eb;
  --accent-hover: #1e40af;
  --accent-soft: #eff6ff;
  --accent-soft-strong: #dbeafe;

  --success: #059669; --success-soft: #d1fae5; --success-ink: #065f46;
  --warning: #ea580c; --warning-soft: #fed7aa; --warning-ink: #92400e;
  --danger:  #dc2626; --danger-soft:  #fecaca; --danger-ink:  #991b1b;
  --info:    #2563eb; --info-soft:    #dbeafe; --info-ink:    #1e40af;

  --viz-1: #2563eb;
  --viz-2: #059669;
  --viz-3: #ea580c;
  --viz-4: #dc2626;
  --viz-5: #7c3aed;

  --space-1: 0.25rem; --space-2: 0.5rem; --space-3: 0.75rem;
  --space-4: 1rem; --space-5: 1.25rem; --space-6: 1.5rem; --space-8: 2rem;

  --radius: 10px;
  --radius-sm: 6px;
  --shadow-sm: 0 1px 3px 0 rgba(15, 23, 42, 0.05);
  --shadow-md: 0 4px 12px rgba(15, 23, 42, 0.08);
  --shadow-lg: 0 10px 25px rgba(15, 23, 42, 0.12);

  --sidebar-width: 260px;
  --sidebar-width-collapsed: 72px;
  --topbar-height: 56px;
  --content-max-width: 1440px;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  background: var(--bg);
  color: #1e293b;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.app-shell {
  display: flex;
  min-height: 100vh;
}

/* ---- Sidebar ---- */

.sidebar-backdrop {
  position: fixed;
  inset: 0;
  background: rgba(15, 23, 42, 0.4);
  z-index: 99;
}

.sidebar {
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  width: var(--sidebar-width);
  z-index: 100;
  background: var(--surface);
  border-right: 1px solid var(--border);
  display: flex;
  flex-direction: column;
}

.sidebar-brand {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 1.25rem 1.25rem;
  border-bottom: 1px solid var(--border);
}

.brand-mark {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 36px;
  height: 36px;
  flex-shrink: 0;
  border-radius: var(--radius-sm);
  background: var(--accent-soft);
  color: var(--accent);
}

.brand-text {
  display: flex;
  flex-direction: column;
  min-width: 0;
}

.sidebar-brand h1 {
  font-size: 1rem;
  font-weight: 700;
  color: var(--ink);
  letter-spacing: -0.025em;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.sidebar-brand .subtitle {
  font-size: 0.75rem;
  color: var(--muted);
  font-weight: 400;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.sidebar-collapse-btn {
  display: none;
  align-items: center;
  justify-content: center;
  width: 28px;
  height: 28px;
  padding: 0;
  margin-left: auto;
  flex-shrink: 0;
  background: transparent;
  border: 1px solid var(--border);
  border-radius: var(--radius-sm);
  color: var(--muted);
  cursor: pointer;
}

.sidebar-collapse-btn:hover {
  background: var(--surface-sunken);
  color: var(--ink);
}

.sidebar-collapse-icon {
  transition: transform 0.2s ease;
}

.sidebar-collapse-icon.is-collapsed {
  transform: rotate(180deg);
}

.sidebar-nav {
  display: flex;
  flex-direction: column;
  gap: 0.125rem;
  padding: 0.75rem;
  flex: 1;
  overflow-y: auto;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.625rem 0.75rem;
  color: var(--muted);
  text-decoration: none;
  font-weight: 500;
  font-size: 0.875rem;
  border-radius: var(--radius-sm);
  border-left: 3px solid transparent;
  transition: all 0.2s ease;
}

.nav-icon {
  flex-shrink: 0;
}

.nav-item:hover {
  background: var(--surface-sunken);
  color: var(--ink);
}

.nav-item.active {
  color: var(--accent);
  background: var(--accent-soft);
  border-left-color: var(--accent);
}

.nav-item.active .nav-icon {
  color: var(--accent);
}

.sidebar-footer {
  padding: 1rem 0.75rem;
  border-top: 1px solid var(--border);
  background: var(--surface-sunken);
}

.sidebar-footer :deep(.profile-menu) {
  width: 100%;
}

.sidebar-footer :deep(.profile-button) {
  width: 100%;
}

/* ---- Main area ---- */

.app-main {
  flex: 1;
  min-width: 0;
  margin-left: var(--sidebar-width);
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.topbar {
  height: var(--topbar-height);
  background: var(--surface);
  border-bottom: 1px solid var(--border);
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 0 1.5rem;
  position: sticky;
  top: 0;
  z-index: 90;
}

.hamburger-btn {
  display: none;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  padding: 0;
  background: transparent;
  border: 1px solid var(--border);
  border-radius: var(--radius-sm);
  color: var(--ink-soft);
  cursor: pointer;
}

.hamburger-btn:hover {
  background: var(--surface-sunken);
}

.topbar-title {
  font-size: 1rem;
  font-weight: 600;
  color: var(--ink);
  letter-spacing: -0.01em;
}

.topbar-language-switcher {
  margin-left: auto;
}

.main-content {
  flex: 1;
  max-width: var(--content-max-width);
  width: 100%;
  margin: 0 auto;
  padding: 1.5rem 2rem;
}

.page-header {
  margin-bottom: 1.5rem;
}

.page-header h2 {
  font-size: 1.875rem;
  font-weight: 700;
  color: var(--ink);
  margin-bottom: 0.375rem;
  letter-spacing: -0.025em;
}

.page-header p {
  color: var(--muted);
  font-size: 0.938rem;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.25rem;
  margin-bottom: 1.5rem;
}

.stat-card {
  background: var(--surface);
  padding: 1.25rem;
  border-radius: var(--radius);
  border: 1px solid var(--border);
  transition: all 0.2s ease;
}

.stat-card:hover {
  border-color: var(--border-strong);
  box-shadow: var(--shadow-md);
}

.stat-label {
  color: var(--muted);
  font-size: 0.875rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 0.625rem;
}

.stat-value {
  font-size: 2.25rem;
  font-weight: 700;
  color: var(--ink);
  letter-spacing: -0.025em;
  font-variant-numeric: tabular-nums;
}

.stat-card.warning .stat-value {
  color: var(--warning);
}

.stat-card.success .stat-value {
  color: var(--success);
}

.stat-card.danger .stat-value {
  color: var(--danger);
}

.stat-card.info .stat-value {
  color: var(--info);
}

.card {
  background: var(--surface);
  border-radius: var(--radius);
  padding: 1.25rem;
  border: 1px solid var(--border);
  margin-bottom: 1.25rem;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  padding-bottom: 0.875rem;
  border-bottom: 1px solid var(--border);
}

.card-title {
  font-size: 1.125rem;
  font-weight: 600;
  color: var(--ink);
  letter-spacing: -0.025em;
}

.table-container {
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background: var(--bg);
  border-top: 1px solid var(--border);
  border-bottom: 1px solid var(--border);
}

th {
  text-align: left;
  padding: 0.5rem 0.75rem;
  font-weight: 600;
  color: #475569;
  font-size: 0.75rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

td {
  padding: 0.5rem 0.75rem;
  border-top: 1px solid var(--surface-sunken);
  color: var(--ink-soft);
  font-size: 0.875rem;
}

tbody tr {
  transition: background-color 0.15s ease;
}

tbody tr:hover {
  background: var(--bg);
}

.badge {
  display: inline-block;
  padding: 0.313rem 0.75rem;
  border-radius: var(--radius-sm);
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.025em;
}

.badge.success {
  background: var(--success-soft);
  color: var(--success-ink);
}

.badge.warning {
  background: var(--warning-soft);
  color: var(--warning-ink);
}

.badge.danger {
  background: var(--danger-soft);
  color: var(--danger-ink);
}

.badge.info {
  background: var(--info-soft);
  color: var(--info-ink);
}

.badge.increasing {
  background: var(--success-soft);
  color: var(--success-ink);
}

.badge.decreasing {
  background: var(--danger-soft);
  color: var(--danger-ink);
}

.badge.stable {
  background: #e0e7ff;
  color: #3730a3;
}

.badge.high {
  background: var(--danger-soft);
  color: var(--danger-ink);
}

.badge.medium {
  background: var(--warning-soft);
  color: var(--warning-ink);
}

.badge.low {
  background: var(--info-soft);
  color: var(--info-ink);
}

.loading {
  text-align: center;
  padding: 3rem;
  color: var(--muted);
  font-size: 0.938rem;
}

.error {
  background: #fef2f2;
  border: 1px solid var(--danger-soft);
  color: var(--danger-ink);
  padding: 1rem;
  border-radius: var(--radius-sm);
  margin: 1rem 0;
  font-size: 0.938rem;
}

@media (max-width: 1024px) {
  .sidebar {
    transform: translateX(-100%);
    transition: transform 0.25s ease;
  }

  .sidebar.is-open {
    transform: translateX(0);
  }

  .app-main {
    margin-left: 0;
  }

  .topbar .hamburger-btn {
    display: inline-flex;
  }

  .sidebar-collapse-btn {
    display: none !important;
  }
}

@media (min-width: 1025px) {
  .sidebar-collapse-btn {
    display: inline-flex;
  }

  .sidebar {
    transition: width 0.2s ease;
  }

  .sidebar.is-collapsed {
    width: var(--sidebar-width-collapsed);
  }

  .sidebar.is-collapsed .sidebar-brand {
    flex-direction: column;
    justify-content: center;
    gap: 0.625rem;
    padding: 1rem 0.75rem;
  }

  .sidebar.is-collapsed .brand-text {
    display: none;
  }

  .sidebar.is-collapsed .sidebar-collapse-btn {
    margin-left: 0;
  }

  .sidebar.is-collapsed .nav-item {
    justify-content: center;
    gap: 0;
    padding-left: 0.75rem;
    padding-right: 0.75rem;
  }

  .sidebar.is-collapsed .nav-item span {
    display: none;
  }

  .app-main {
    transition: margin-left 0.2s ease;
  }

  .app-main.is-collapsed {
    margin-left: var(--sidebar-width-collapsed);
  }
}
</style>
