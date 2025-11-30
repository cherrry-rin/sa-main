<template>
  <div class="subnet-calculator">
    <h1 class="calculator-title">Калькулятор подсетей</h1>
    
    <div class="input-group">
      <div class="input-field">
        <label for="ip-address" class="input-label">IP адрес:</label>
        <input
          id="ip-address"
          v-model="ipAddress"
          type="text"
          placeholder="192.168.1.150"
          class="input-control"
          :class="{ 'input-error': !isIpValid }"
          @keyup.enter="calculate"
        />
        <div v-if="!isIpValid && ipAddress" class="error-message">
          Неверный формат IP адреса
        </div>
      </div>

      <div class="input-field">
        <label for="subnet-mask" class="input-label">Маска подсети:</label>
        <select
          id="subnet-mask"
          v-model="selectedMask"
          class="select-control"
        >
          <option
            v-for="mask in SUBNET_MASKS"
            :key="mask.value"
            :value="mask.value"
          >
            {{ mask.label }}
          </option>
        </select>
      </div>
    </div>

    <button
      class="calculate-button"
      :disabled="!canCalculate"
      @click="calculate"
    >
      Рассчитать
    </button>

    <div v-if="showResults" class="results">
      <h2 class="results-title">Результаты расчета:</h2>
      
      <div class="result-item">
        <span class="result-label">IP адрес:</span>
        <span class="result-value">{{ ipAddress }}</span>
      </div>
      
      <div class="result-item">
        <span class="result-label">Маска подсети:</span>
        <span class="result-value">{{ selectedMask }}</span>
      </div>
      
      <div class="result-item">
        <span class="result-label">Адрес сети:</span>
        <span class="result-value">{{ networkAddress }}</span>
      </div>
      
      <div class="result-item">
        <span class="result-label">Количество адресов:</span>
        <span class="result-value">{{ addressesCount }}</span>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { SUBNET_MASKS } from '../constants/masks'
import { isIpValid } from '../utils/ipValidation'
import { getNetworkAddress, getAddressesCount } from '../utils/networkCalculations'

const ipAddress = ref('192.168.1.150')
const selectedMask = ref('255.255.255.0')
const networkAddress = ref('')
const addressesCount = ref(0)
const showResults = ref(false)

const isIpValid = computed(() => isIpValid(ipAddress.value))

const canCalculate = computed(() => {
  return isIpValid.value && selectedMask.value
})

function calculate() {
  if (!canCalculate.value) return

  networkAddress.value = getNetworkAddress(ipAddress.value, selectedMask.value)
  addressesCount.value = getAddressesCount(selectedMask.value)
  showResults.value = true
}
</script>

<style scoped>
.subnet-calculator {
  max-width: 600px;
  margin: 0 auto;
  padding: 2rem;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.calculator-title {
  text-align: center;
  color: var(--text-primary);
  margin-bottom: 2rem;
  font-size: 1.5rem;
}

.input-group {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
  margin-bottom: 2rem;
}

.input-field {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.input-label {
  font-weight: 600;
  color: var(--text-primary);
  margin-bottom: 0.25rem;
}

.input-control {
  padding: 0.75rem;
  border: 2px solid var(--border-color);
  border-radius: 8px;
  font-size: 1rem;
  transition: border-color 0.3s ease;
}

.input-control:focus {
  outline: none;
  border-color: var(--primary-color);
}

.input-error {
  border-color: var(--error-color);
}

.select-control {
  padding: 0.75rem;
  border: 2px solid var(--border-color);
  border-radius: 8px;
  font-size: 1rem;
  background-color: white;
  cursor: pointer;
  transition: border-color 0.3s ease;
}

.select-control:focus {
  outline: none;
  border-color: var(--primary-color);
}

.calculate-button {
  width: 100%;
  padding: 0.875rem;
  background-color: var(--primary-color);
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.calculate-button:hover:not(:disabled) {
  background-color: var(--primary-dark);
}

.calculate-button:disabled {
  background-color: var(--disabled-color);
  cursor: not-allowed;
}

.error-message {
  color: var(--error-color);
  font-size: 0.875rem;
  margin-top: 0.25rem;
}

.results {
  margin-top: 2rem;
  padding: 1.5rem;
  background-color: var(--background-light);
  border-radius: 8px;
  border-left: 4px solid var(--primary-color);
}

.results-title {
  color: var(--text-primary);
  margin-bottom: 1rem;
  font-size: 1.25rem;
}

.result-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem 0;
  border-bottom: 1px solid var(--border-light);
}

.result-item:last-child {
  border-bottom: none;
}

.result-label {
  font-weight: 600;
  color: var(--text-secondary);
}

.result-value {
  color: var(--text-primary);
  font-family: 'Courier New', monospace;
  font-weight: 600;
}
</style>