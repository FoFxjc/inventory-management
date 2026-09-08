<template>
  <div class="reports">
    <div class="page-header">
      <h2>Performance Reports</h2>
      <p>View quarterly performance metrics and monthly trends</p>
    </div>

    <div v-if="loading" class="loading">Loading reports...</div>
    <div v-else-if="error" class="error">{{ error }}</div>
    <div v-else>
      <!-- Quarterly Performance -->
      <div class="card">
        <div class="card-header">
          <h3 class="card-title">Quarterly Performance</h3>
        </div>
        <div class="table-container">
          <table>
            <thead>
              <tr>
                <th>Quarter</th>
                <th>Total Orders</th>
                <th>Total Revenue</th>
                <th>Avg Order Value</th>
                <th>Fulfillment Rate</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(q, index) in quarterlyData" :key="index">
                <td><strong>{{ q.quarter }}</strong></td>
                <td>{{ q.total_orders }}</td>
                <td>${{ formatNumber(q.total_revenue) }}</td>
                <td>${{ formatNumber(q.avg_order_value) }}</td>
                <td>
                  <span :class="getFulfillmentClass(q.fulfillment_rate)">
                    {{ q.fulfillment_rate }}%
                  </span>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <!-- Monthly Trends Chart -->
      <div class="card">
        <div class="card-header">
          <h3 class="card-title">Monthly Revenue Trend</h3>
        </div>
        <div class="chart-container">
          <div class="bar-chart">
            <div v-for="(month, index) in monthlyData" :key="index" class="bar-wrapper">
              <div class="bar-container">
                <div
                  class="bar"
                  :style="{ height: getBarHeight(month.revenue) + 'px' }"
                  :title="'$' + formatNumber(month.revenue)"
                ></div>
              </div>
              <div class="bar-label">{{ formatMonth(month.month) }}</div>
            </div>
          </div>
        </div>
      </div>

      <!-- Month-over-Month Comparison -->
      <div class="card">
        <div class="card-header">
          <h3 class="card-title">Month-over-Month Analysis</h3>
        </div>
        <div class="table-container">
          <table>
            <thead>
              <tr>
                <th>Month</th>
                <th>Orders</th>
                <th>Revenue</th>
                <th>Change</th>
                <th>Growth Rate</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(month, index) in monthlyData" :key="index">
                <td><strong>{{ formatMonth(month.month) }}</strong></td>
                <td>{{ month.order_count }}</td>
                <td>${{ formatNumber(month.revenue) }}</td>
                <td>
                  <span v-if="index > 0" :class="getChangeClass(month.revenue, monthlyData[index - 1].revenue)">
                    {{ getChangeValue(month.revenue, monthlyData[index - 1].revenue) }}
                  </span>
                  <span v-else>-</span>
                </td>
                <td>
                  <span v-if="index > 0" :class="getChangeClass(month.revenue, monthlyData[index - 1].revenue)">
                    {{ getGrowthRate(month.revenue, monthlyData[index - 1].revenue) }}
                  </span>
                  <span v-else>-</span>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <!-- Summary Stats -->
      <div class="stats-grid">
        <div class="stat-card">
          <div class="stat-label">Total Revenue (YTD)</div>
          <div class="stat-value">${{ formatNumber(totalRevenue) }}</div>
        </div>
        <div class="stat-card">
          <div class="stat-label">Avg Monthly Revenue</div>
          <div class="stat-value">${{ formatNumber(avgMonthlyRevenue) }}</div>
        </div>
        <div class="stat-card">
          <div class="stat-label">Total Orders (YTD)</div>
          <div class="stat-value">{{ totalOrders }}</div>
        </div>
        <div class="stat-card">
          <div class="stat-label">Best Performing Quarter</div>
          <div class="stat-value">{{ bestQuarter }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'
import { api } from '../api'

export default {
  name: 'Reports',
  setup() {
    const loading = ref(true)
    const error = ref(null)
    const quarterlyData = ref([])
    const monthlyData = ref([])
    const totalRevenue = ref(0)
    const avgMonthlyRevenue = ref(0)
    const totalOrders = ref(0)
    const bestQuarter = ref('')

    const calculateSummaryStats = () => {
      // Calculate total revenue
      var total = 0
      for (var i = 0; i < monthlyData.value.length; i++) {
        total = total + monthlyData.value[i].revenue
      }
      totalRevenue.value = total

      // Calculate average monthly revenue
      if (monthlyData.value.length > 0) {
        avgMonthlyRevenue.value = total / monthlyData.value.length
      } else {
        avgMonthlyRevenue.value = 0
      }

      // Calculate total orders
      var orders = 0
      for (var i = 0; i < monthlyData.value.length; i++) {
        orders = orders + monthlyData.value[i].order_count
      }
      totalOrders.value = orders

      // Find best quarter
      var bestQ = ''
      var bestRevenue = 0
      for (var i = 0; i < quarterlyData.value.length; i++) {
        if (quarterlyData.value[i].total_revenue > bestRevenue) {
          bestRevenue = quarterlyData.value[i].total_revenue
          bestQ = quarterlyData.value[i].quarter
        }
      }
      bestQuarter.value = bestQ
    }

    const loadData = async () => {
      console.log('Loading reports data...')
      try {
        loading.value = true

        // Fetch quarterly data
        console.log('Fetching quarterly data...')
        quarterlyData.value = await api.getQuarterlyReports()
        console.log('Quarterly data:', quarterlyData.value)

        // Fetch monthly data
        console.log('Fetching monthly data...')
        monthlyData.value = await api.getMonthlyTrends()
        console.log('Monthly data:', monthlyData.value)

        // Calculate summary stats
        console.log('Calculating summary stats...')
        calculateSummaryStats()
        console.log('Summary stats calculated')

      } catch (err) {
        console.log('Error loading reports:', err)
        error.value = 'Failed to load reports: ' + err.message
      } finally {
        loading.value = false
        console.log('Loading complete')
      }
    }

    const formatNumber = (num) => {
      console.log('Formatting number:', num)
      // Format number with commas
      var str = num.toString()
      var parts = str.split('.')
      var intPart = parts[0]
      var decPart = parts.length > 1 ? parts[1] : '00'

      var formatted = ''
      var count = 0
      for (var i = intPart.length - 1; i >= 0; i--) {
        if (count > 0 && count % 3 === 0) {
          formatted = ',' + formatted
        }
        formatted = intPart[i] + formatted
        count++
      }

      if (decPart.length === 1) {
        decPart = decPart + '0'
      }
      if (decPart.length > 2) {
        decPart = decPart.substring(0, 2)
      }

      return formatted + '.' + decPart
    }

    const formatMonth = (monthStr) => {
      console.log('Formatting month:', monthStr)
      // Convert YYYY-MM to readable format
      var parts = monthStr.split('-')
      var year = parts[0]
      var month = parts[1]

      var monthNames = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
      var monthIndex = parseInt(month) - 1

      return monthNames[monthIndex] + ' ' + year
    }

    const getBarHeight = (revenue) => {
      console.log('Calculating bar height for revenue:', revenue)
      // Calculate bar height (max height 200px)
      var maxRevenue = 0
      for (var i = 0; i < monthlyData.value.length; i++) {
        if (monthlyData.value[i].revenue > maxRevenue) {
          maxRevenue = monthlyData.value[i].revenue
        }
      }

      if (maxRevenue === 0) {
        return 0
      }

      var height = (revenue / maxRevenue) * 200
      return height
    }

    const getFulfillmentClass = (rate) => {
      if (rate >= 90) {
        return 'badge success'
      } else if (rate >= 75) {
        return 'badge warning'
      } else {
        return 'badge danger'
      }
    }

    const getChangeValue = (current, previous) => {
      var change = current - previous
      if (change > 0) {
        return '+$' + formatNumber(change)
      } else if (change < 0) {
        return '-$' + formatNumber(Math.abs(change))
      } else {
        return '$0.00'
      }
    }

    const getChangeClass = (current, previous) => {
      var change = current - previous
      if (change > 0) {
        return 'positive-change'
      } else if (change < 0) {
        return 'negative-change'
      } else {
        return ''
      }
    }

    const getGrowthRate = (current, previous) => {
      if (previous === 0) {
        return 'N/A'
      }

      var rate = ((current - previous) / previous) * 100
      var sign = rate > 0 ? '+' : ''

      return sign + rate.toFixed(1) + '%'
    }

    onMounted(() => {
      console.log('Reports component mounted')
      loadData()
    })

    return {
      loading,
      error,
      quarterlyData,
      monthlyData,
      totalRevenue,
      avgMonthlyRevenue,
      totalOrders,
      bestQuarter,
      formatNumber,
      formatMonth,
      getBarHeight,
      getFulfillmentClass,
      getChangeValue,
      getChangeClass,
      getGrowthRate
    }
  }
}
</script>

<style scoped>
.reports {
  padding: 0;
}

.chart-container {
  padding: var(--space-8) var(--space-4);
  min-height: 300px;
}

.bar-chart {
  display: flex;
  align-items: flex-end;
  justify-content: space-around;
  height: 250px;
  gap: var(--space-2);
  border-bottom: 1px solid var(--border);
}

.bar-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  flex: 1;
  max-width: 80px;
}

.bar-container {
  height: 200px;
  display: flex;
  align-items: flex-end;
  width: 100%;
}

.bar {
  width: 100%;
  background: var(--viz-1);
  border-radius: var(--radius-sm) var(--radius-sm) 0 0;
  transition: all 0.3s;
  cursor: pointer;
}

.bar:hover {
  background: var(--accent-hover);
}

.bar-label {
  margin-top: var(--space-6);
  font-size: 0.75rem;
  color: var(--muted);
  text-align: center;
  transform: rotate(-45deg);
  white-space: nowrap;
}

.positive-change {
  color: var(--success);
  font-weight: 600;
}

.negative-change {
  color: var(--danger);
  font-weight: 600;
}
</style>
