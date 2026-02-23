<template>
  <section class="calc-card">
    <h2>Gold & Silver Price Calculator</h2>

    <div class="form-grid">
      <label>Metal
        <select v-model="metal">
          <option value="Gold">Gold</option>
          <option value="Silver">Silver</option>
        </select>
      </label>

      <label>Unit
        <select v-model="unit">
          <option value="Gram">Gram</option>
          <option value="Ounce">Ounce</option>
        </select>
      </label>

      <label>Override price per troy ounce (P) — optional
        <input v-model="overrideOunce" placeholder="Leave empty to use internal price" />
      </label>

      <label>Override price per gram (P) — optional
        <input v-model="overrideGram" placeholder="Leave empty to derive from ounce price" />
      </label>

      <label>Weight
        <input v-model="weight" placeholder="Enter weight in selected unit" />
      </label>

      <label>Karat (Gold only)
        <select v-model.number="karat" :disabled="metal !== 'Gold'">
          <option :value="24">24K</option>
          <option :value="22">22K</option>
          <option :value="18">18K</option>
          <option :value="14">14K</option>
          <option :value="10">10K</option>
        </select>
      </label>
    </div>

    <div class="breakdown">
      <h3>Computation breakdown (all amounts in P):</h3>

      <div class="lines">
        <div>1 troy oz = {{ troyGram }} g</div>
        <div>Market price per troy ounce (internal): {{ formatCurrency(internalOuncePrice) }} (source: internal-default)</div>
        <div>Effective price per troy ounce used: {{ formatCurrency(effectiveOuncePrice) }}</div>
        <div>Converted price per gram: {{ formatCurrency(effectiveGramPrice) }}</div>
        <div>Weight in grams: {{ formatNumber(weightGrams) }} g</div>
        <div>Base price (before purity): {{ formatCurrency(basePrice) }}</div>
        <div v-if="metal === 'Gold'">Purity factor ({{ karat }}/24): {{ purityFactor.toFixed(4) }}</div>
        <div>Purity-adjusted price: {{ formatCurrency(purityAdjustedPrice) }}</div>
        <div>Tax ({{ (taxRate*100).toFixed(0) }}% applied after purity): {{ formatCurrency(taxAmount) }}</div>
        <div class="final">Final total: {{ formatCurrency(finalTotal) }}</div>
      </div>

      <div v-if="error" class="error">{{ error }}</div>
    </div>
  </section>
</template>

<script setup>
import { ref, computed } from 'vue'

const troyGram = 31.1035

const metal = ref('Gold')
const unit = ref('Gram')
const overrideOunce = ref('')
const overrideGram = ref('')
const weight = ref('')
const karat = ref(24)

const taxRate = 0.12

// Internal prices (example values from design screenshots)
const INTERNAL_PRICES = {
  Gold: 280000.00,
  Silver: 2800.00
}

const parseNumber = (v) => {
  if (v === null || v === undefined || v === '') return NaN
  const n = Number(String(v).replace(/,/g, '').trim())
  return Number.isFinite(n) ? n : NaN
}

const internalOuncePrice = computed(() => INTERNAL_PRICES[metal.value])

const effectiveOuncePrice = computed(() => {
  const p = parseNumber(overrideOunce.value)
  return Number.isFinite(p) ? p : internalOuncePrice.value
})

const effectiveGramPrice = computed(() => {
  const pGram = parseNumber(overrideGram.value)
  if (Number.isFinite(pGram)) return pGram
  return effectiveOuncePrice.value / troyGram
})

const weightGrams = computed(() => {
  const w = parseNumber(weight.value)
  if (!Number.isFinite(w)) return NaN
  return unit.value === 'Gram' ? w : w * troyGram
})

const basePrice = computed(() => {
  const gp = effectiveGramPrice.value
  const wg = weightGrams.value
  if (!Number.isFinite(gp) || !Number.isFinite(wg)) return NaN
  return gp * wg
})

const purityFactor = computed(() => (metal.value === 'Gold' ? (karat.value / 24) : 1))

const purityAdjustedPrice = computed(() => {
  const bp = basePrice.value
  if (!Number.isFinite(bp)) return NaN
  return bp * purityFactor.value
})

const taxAmount = computed(() => {
  const pap = purityAdjustedPrice.value
  if (!Number.isFinite(pap)) return 0
  return pap * taxRate
})

const finalTotal = computed(() => {
  const pap = purityAdjustedPrice.value
  if (!Number.isFinite(pap)) return NaN
  return pap + taxAmount.value
})

const error = computed(() => {
  if (!Number.isFinite(weightGrams.value) || weightGrams.value <= 0) return 'Provide valid numeric price and weight to compute'
  return ''
})

const formatNumber = (v) => {
  if (!Number.isFinite(v)) return '—'
  return Number(v).toLocaleString(undefined, {minimumFractionDigits:2, maximumFractionDigits:2})
}

const formatCurrency = (v) => {
  if (!Number.isFinite(v)) return '—'
  return `P${Number(v).toLocaleString(undefined, {minimumFractionDigits:2, maximumFractionDigits:2})}`
}
</script>

<style scoped>
.calc-card {
  background: #fff;
  border-radius: 10px;
  padding: 1.25rem;
  box-shadow: 0 6px 20px rgba(10,20,30,0.06);
}

.calc-card h2 {
  margin: 0 0 1rem 0;
  font-size: 1.05rem;
  font-weight: 700;
}

.form-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 0.8rem;
  margin-bottom: 1rem;
}

.form-grid label {
  display: flex;
  flex-direction: column;
  font-size: 0.85rem;
}

.form-grid input,
.form-grid select {
  margin-top: 0.4rem;
  padding: 8px 10px;
  border: 1px solid #e6e6f0;
  border-radius: 6px;
}

.breakdown {
  background: #fbfdff;
  border-radius: 6px;
  padding: 0.9rem;
  font-size: 0.86rem;
}

.breakdown .lines div { margin: 0.2rem 0 }
.breakdown .final { font-weight: 700; margin-top: 0.5rem }
.error { color: #b00020; margin-top: 0.5rem }
</style>
