<template>
  <Teleport to="body">
    <Transition name="modal">
      <div v-if="isOpen && backlogItem" class="modal-overlay" @click="close">
        <div class="modal-container" @click.stop>
          <div class="modal-header">
            <h3 class="modal-title">{{ isCreateMode ? t('purchaseOrder.createTitle') : t('purchaseOrder.viewTitle') }}</h3>
            <button class="close-button" @click="close">
              <svg width="20" height="20" viewBox="0 0 20 20" fill="none">
                <path d="M15 5L5 15M5 5L15 15" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
              </svg>
            </button>
          </div>

          <div class="modal-body">
            <div class="item-summary">
              <div class="item-summary-name">{{ translateProductName(backlogItem.item_name) }}</div>
              <div class="item-summary-meta">
                <span class="item-summary-sku">{{ t('purchaseOrder.skuLabel') }}: {{ backlogItem.item_sku }}</span>
                <span class="item-summary-shortage">{{ t('purchaseOrder.shortageLabel') }}: {{ shortage }}</span>
              </div>
            </div>

            <!-- Create Mode: Form -->
            <form v-if="isCreateMode" class="po-form" @submit.prevent="handleSubmit">
              <div class="form-row">
                <div class="form-group flex-1">
                  <label for="po-supplier">{{ t('purchaseOrder.supplierName') }}</label>
                  <input
                    id="po-supplier"
                    v-model="form.supplier_name"
                    type="text"
                    :placeholder="t('purchaseOrder.supplierNamePlaceholder')"
                    class="po-input"
                  />
                </div>
              </div>

              <div class="form-row">
                <div class="form-group">
                  <label for="po-quantity">{{ t('purchaseOrder.quantity') }}</label>
                  <input
                    id="po-quantity"
                    v-model.number="form.quantity"
                    type="number"
                    min="1"
                    class="po-input"
                  />
                </div>

                <div class="form-group">
                  <label for="po-unit-cost">{{ t('purchaseOrder.unitCost') }}</label>
                  <input
                    id="po-unit-cost"
                    v-model.number="form.unit_cost"
                    type="number"
                    min="0"
                    step="0.01"
                    class="po-input"
                  />
                </div>
              </div>

              <div class="form-row">
                <div class="form-group flex-1">
                  <label for="po-delivery-date">{{ t('purchaseOrder.expectedDeliveryDate') }}</label>
                  <input
                    id="po-delivery-date"
                    v-model="form.expected_delivery_date"
                    type="date"
                    class="po-input"
                  />
                </div>
              </div>

              <div class="form-row">
                <div class="form-group flex-1">
                  <label for="po-notes">{{ t('purchaseOrder.notes') }}</label>
                  <textarea
                    id="po-notes"
                    v-model="form.notes"
                    :placeholder="t('purchaseOrder.notesPlaceholder')"
                    class="po-textarea"
                    rows="3"
                  ></textarea>
                </div>
              </div>

              <div class="total-cost-row">
                <span class="total-cost-label">{{ t('purchaseOrder.totalCost') }}</span>
                <span class="total-cost-value">{{ formatCurrency(totalCost, currentCurrency) }}</span>
              </div>

              <div v-if="submitError" class="form-error">{{ submitError }}</div>
            </form>

            <!-- View Mode: Read-only details -->
            <div v-else class="po-view">
              <div v-if="viewLoading" class="view-state">{{ t('purchaseOrder.loading') }}</div>
              <div v-else-if="viewError" class="view-state error">{{ viewError }}</div>
              <div v-else-if="viewPo" class="info-grid">
                <div class="info-item">
                  <div class="info-label">{{ t('purchaseOrder.supplierName') }}</div>
                  <div class="info-value">{{ viewPo.supplier_name }}</div>
                </div>

                <div class="info-item">
                  <div class="info-label">{{ t('purchaseOrder.status') }}</div>
                  <div class="info-value">
                    <span class="badge status">{{ translateStatus(viewPo.status) }}</span>
                  </div>
                </div>

                <div class="info-item">
                  <div class="info-label">{{ t('purchaseOrder.quantity') }}</div>
                  <div class="info-value">{{ viewPo.quantity }}</div>
                </div>

                <div class="info-item">
                  <div class="info-label">{{ t('purchaseOrder.unitCost') }}</div>
                  <div class="info-value">{{ formatCurrency(viewPo.unit_cost, currentCurrency) }}</div>
                </div>

                <div class="info-item">
                  <div class="info-label">{{ t('purchaseOrder.totalCost') }}</div>
                  <div class="info-value">{{ formatCurrency(viewPo.unit_cost * viewPo.quantity, currentCurrency) }}</div>
                </div>

                <div class="info-item">
                  <div class="info-label">{{ t('purchaseOrder.expectedDeliveryDate') }}</div>
                  <div class="info-value">{{ formatDate(viewPo.expected_delivery_date) }}</div>
                </div>

                <div class="info-item">
                  <div class="info-label">{{ t('purchaseOrder.createdDate') }}</div>
                  <div class="info-value">{{ formatDate(viewPo.created_date) }}</div>
                </div>

                <div v-if="viewPo.notes" class="info-item full-width">
                  <div class="info-label">{{ t('purchaseOrder.notes') }}</div>
                  <div class="info-value">{{ viewPo.notes }}</div>
                </div>
              </div>
            </div>
          </div>

          <div class="modal-footer">
            <button class="btn-secondary" @click="close">
              {{ isCreateMode ? t('purchaseOrder.cancel') : t('purchaseOrder.close') }}
            </button>
            <button
              v-if="isCreateMode"
              class="btn-primary"
              :disabled="!isFormValid || submitting"
              @click="handleSubmit"
            >
              {{ submitting ? t('purchaseOrder.submitting') : t('purchaseOrder.submit') }}
            </button>
          </div>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<script setup>
import { ref, computed, watch } from 'vue'
import { useI18n } from '../composables/useI18n'
import { api } from '../api'
import { formatCurrency } from '../utils/currency'

const { t, currentLocale, currentCurrency, translateProductName } = useI18n()

const props = defineProps({
  isOpen: {
    type: Boolean,
    default: false
  },
  backlogItem: {
    type: Object,
    default: null
  },
  mode: {
    type: String,
    default: 'create'
  }
})

const emit = defineEmits(['close', 'po-created'])

const isCreateMode = computed(() => props.mode === 'create')

const shortage = computed(() => {
  if (!props.backlogItem) return 0
  return Math.max(props.backlogItem.quantity_needed - props.backlogItem.quantity_available, 0)
})

const defaultForm = () => ({
  supplier_name: '',
  quantity: shortage.value || 1,
  unit_cost: null,
  expected_delivery_date: '',
  notes: ''
})

const form = ref(defaultForm())
const submitting = ref(false)
const submitError = ref(null)

const viewPo = ref(null)
const viewLoading = ref(false)
const viewError = ref(null)

const totalCost = computed(() => {
  const qty = Number(form.value.quantity) || 0
  const cost = Number(form.value.unit_cost) || 0
  return qty * cost
})

const isFormValid = computed(() => {
  return (
    form.value.supplier_name.trim().length > 0 &&
    Number(form.value.quantity) > 0 &&
    Number(form.value.unit_cost) > 0 &&
    !!form.value.expected_delivery_date
  )
})

const loadPurchaseOrder = async () => {
  if (!props.backlogItem) return
  viewLoading.value = true
  viewError.value = null
  viewPo.value = null
  try {
    viewPo.value = await api.getPurchaseOrderByBacklogItem(props.backlogItem.id)
  } catch (err) {
    viewError.value = t('purchaseOrder.loadError')
    console.error(err)
  } finally {
    viewLoading.value = false
  }
}

watch(
  () => [props.isOpen, props.mode, props.backlogItem],
  () => {
    if (!props.isOpen || !props.backlogItem) return

    if (props.mode === 'view') {
      loadPurchaseOrder()
    } else {
      form.value = defaultForm()
      submitError.value = null
    }
  },
  { immediate: true }
)

const close = () => {
  emit('close')
}

const handleSubmit = async () => {
  if (!props.backlogItem || !isFormValid.value) return
  submitting.value = true
  submitError.value = null
  try {
    const createdPo = await api.createPurchaseOrder({
      backlog_item_id: props.backlogItem.id,
      supplier_name: form.value.supplier_name.trim(),
      quantity: Number(form.value.quantity),
      unit_cost: Number(form.value.unit_cost),
      expected_delivery_date: form.value.expected_delivery_date,
      notes: form.value.notes.trim() || undefined
    })
    emit('po-created', createdPo)
  } catch (err) {
    submitError.value = t('purchaseOrder.createError')
    console.error(err)
  } finally {
    submitting.value = false
  }
}

const translateStatus = (status) => {
  const statusMap = {
    pending: t('purchaseOrder.statusPending')
  }
  return statusMap[(status || '').toLowerCase()] || status
}

const formatDate = (dateString) => {
  if (!dateString) return '-'
  const date = new Date(dateString)
  if (isNaN(date.getTime())) return '-'
  const locale = currentLocale.value === 'ja' ? 'ja-JP' : 'en-US'
  return date.toLocaleDateString(locale, {
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  })
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(15, 23, 42, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2000;
  padding: var(--space-4);
}

.modal-container {
  background: var(--surface);
  border-radius: var(--radius);
  box-shadow: var(--shadow-lg);
  max-width: 600px;
  width: 100%;
  max-height: 90vh;
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

.modal-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: var(--space-6);
  border-bottom: 1px solid var(--border);
}

.modal-title {
  font-size: 1.25rem;
  font-weight: 700;
  color: var(--ink);
  letter-spacing: -0.025em;
}

.close-button {
  background: none;
  border: none;
  color: var(--muted);
  cursor: pointer;
  padding: var(--space-2);
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: var(--radius-sm);
  transition: all 0.15s ease;
}

.close-button:hover {
  background: var(--surface-sunken);
  color: var(--ink);
}

.modal-body {
  flex: 1;
  overflow-y: auto;
  padding: var(--space-8);
}

.item-summary {
  padding-bottom: var(--space-6);
  border-bottom: 1px solid var(--border);
  margin-bottom: var(--space-6);
}

.item-summary-name {
  font-size: 1.125rem;
  font-weight: 700;
  color: var(--ink);
  margin-bottom: var(--space-2);
}

.item-summary-meta {
  display: flex;
  gap: var(--space-5);
  font-size: 0.875rem;
  color: var(--muted);
}

.item-summary-sku {
  font-family: 'Monaco', 'Courier New', monospace;
}

/* Form */
.po-form {
  display: flex;
  flex-direction: column;
  gap: var(--space-4);
}

.form-row {
  display: flex;
  gap: var(--space-4);
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: var(--space-2);
  flex: 1;
}

.form-group.flex-1 {
  flex: 1;
}

label {
  font-size: 0.875rem;
  font-weight: 600;
  color: var(--ink-soft);
}

.po-input,
.po-textarea {
  padding: var(--space-3);
  border: 2px solid var(--border);
  border-radius: var(--radius-sm);
  font-size: 0.95rem;
  transition: border-color 0.2s ease, box-shadow 0.2s ease;
  font-family: inherit;
  width: 100%;
}

.po-textarea {
  resize: vertical;
}

.po-input:focus,
.po-textarea:focus {
  outline: none;
  border-color: var(--accent);
  box-shadow: 0 0 0 3px var(--accent-soft-strong);
}

.total-cost-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: var(--space-4);
  background: var(--surface-sunken);
  border-radius: var(--radius-sm);
}

.total-cost-label {
  font-size: 0.875rem;
  font-weight: 600;
  color: var(--ink-soft);
  text-transform: uppercase;
  letter-spacing: 0.025em;
}

.total-cost-value {
  font-size: 1.25rem;
  font-weight: 700;
  color: var(--ink);
}

.form-error {
  padding: var(--space-3);
  background: var(--danger-soft);
  color: var(--danger-ink);
  border-radius: var(--radius-sm);
  font-size: 0.875rem;
}

/* View mode */
.view-state {
  padding: 2.5rem var(--space-4);
  text-align: center;
  color: var(--muted);
  font-size: 0.95rem;
}

.view-state.error {
  color: var(--danger-ink);
}

.info-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
  gap: var(--space-6);
}

.info-item {
  display: flex;
  flex-direction: column;
  gap: var(--space-2);
}

.info-item.full-width {
  grid-column: 1 / -1;
}

.info-label {
  font-size: 0.813rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  color: var(--muted);
}

.info-value {
  font-size: 0.938rem;
  color: var(--ink);
  font-weight: 500;
}

.badge.status {
  display: inline-block;
  padding: var(--space-1) var(--space-3);
  border-radius: var(--radius-sm);
  font-size: 0.75rem;
  font-weight: 600;
  background: var(--warning-soft);
  color: var(--warning-ink);
  text-transform: uppercase;
  letter-spacing: 0.025em;
}

.modal-footer {
  padding: var(--space-6);
  border-top: 1px solid var(--border);
  display: flex;
  justify-content: flex-end;
  gap: var(--space-3);
}

.btn-secondary {
  padding: 0.625rem var(--space-5);
  background: var(--surface-sunken);
  border: 1px solid var(--border);
  border-radius: var(--radius-sm);
  font-weight: 500;
  font-size: 0.875rem;
  color: var(--ink-soft);
  cursor: pointer;
  transition: all 0.15s ease;
  font-family: inherit;
}

.btn-secondary:hover {
  background: var(--border);
  border-color: var(--border-strong);
}

.btn-primary {
  padding: 0.625rem var(--space-5);
  background: var(--accent);
  border: none;
  border-radius: var(--radius-sm);
  font-weight: 600;
  font-size: 0.875rem;
  color: white;
  cursor: pointer;
  transition: all 0.15s ease;
  font-family: inherit;
}

.btn-primary:hover:not(:disabled) {
  background: var(--accent-hover);
}

.btn-primary:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* Modal transition animations */
.modal-enter-active,
.modal-leave-active {
  transition: opacity 0.2s ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}

.modal-enter-active .modal-container,
.modal-leave-active .modal-container {
  transition: transform 0.2s ease;
}

.modal-enter-from .modal-container,
.modal-leave-to .modal-container {
  transform: scale(0.95);
}
</style>
