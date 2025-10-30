<script setup>
import { ref, computed } from 'vue'

const a = ref('')
const b = ref('')
const c = ref('')
const signB = ref('+')
const signC = ref('+')
const solution = ref(null)
const error = ref('')

const solveQuadratic = () => {
  error.value = ''
  solution.value = null

  try {
    if (a.value === "" || b.value === "" || c.value === "") {
      error.value = 'Все коэффициенты должны быть заполнены'
      return
    }

    const numA = parseFloat(a.value)
    const numB = parseFloat(b.value)
    const numC = parseFloat(c.value)

    if (isNaN(numA) || isNaN(numB) || isNaN(numC)) {
      error.value = 'Все коэффициенты должны быть числами'
      return
    }

    if (numA === 0) {
      solution.value = {
        type: 'a=0',
        x: -c.value / b.value,
      }
      return
    }

    const signedA = numA
    const signedB = signB.value === '+' ? numB : -numB
    const signedC = signC.value === '+' ? numC : -numC

    const discriminant = signedB * signedB - 4 * signedA * signedC

    if (discriminant > 0) {
      const x1 = (-signedB + Math.sqrt(discriminant)) / (2 * signedA)
      const x2 = (-signedB - Math.sqrt(discriminant)) / (2 * signedA)
      solution.value = {
        type: 'two-roots',
        x1: x1,
        x2: x2,
        discriminant: discriminant
      }
    } else if (discriminant === 0) {
      const x = -signedB / (2 * signedA)
      solution.value = {
        type: 'one-root',
        x: x,
        discriminant: discriminant
      }
    } else {
      const realPart = -signedB / (2 * signedA)
      const imaginaryPart = Math.sqrt(-discriminant) / (2 * signedA)
      solution.value = {
        type: 'complex-roots',
        real: realPart,
        imaginary: imaginaryPart,
        discriminant: discriminant
      }
    }

  } catch (err) {
    error.value = 'Произошла ошибка при решении уравнения'
    console.error(err)
  }
}

const clearForm = () => {
  a.value = ''
  b.value = ''
  c.value = ''
  signB.value = '+'
  signC.value = '+'
  solution.value = null
  error.value = ''
}

const equationString = computed(() => {
  const aVal = a.value !== "" ? a.value : "a"
  const bVal = b.value !== "" ? b.value : "b"
  const cVal = c.value !== "" ? c.value : "c"
  
  return `${aVal}x² ${signB.value} ${bVal}x ${signC.value} ${cVal} = 0`
})
</script>

<template>
  <div class="quadratic-solver">
    <div class="container">
      <h1>Дискриминантор-22102025</h1>
      
      <div class="equation-display">
        {{ equationString }}
      </div>

      <div class="input-group">
        <div class="input-row">
          <input
            v-model="a"
            type="number"
            placeholder="a"
            class="coefficient-input"
            step="any"
          />
          <span class="equation-part">x²</span>
          
          <select v-model="signB" class="sign-select">
            <option value="+">+</option>
            <option value="-">-</option>
          </select>
          
          <input
            v-model="b"
            type="number"
            placeholder="b"
            class="coefficient-input"
            step="any"
          />
          <span class="equation-part">x</span>
          
          <select v-model="signC" class="sign-select">
            <option value="+">+</option>
            <option value="-">-</option>
          </select>
          
          <input
            v-model="c"
            type="number"
            placeholder="c"
            class="coefficient-input"
            step="any"
          />
          <span class="equation-part">= 0</span>
        </div>
      </div>

      <div class="controls">
        <button @click="solveQuadratic" class="btn solve-btn">
          Решить уравнение
        </button>
        <button @click="clearForm" class="btn clear-btn">
          Очистить
        </button>
      </div>

      <div v-if="error" class="error-message">
        {{ error }}
      </div>

      <div v-if="solution" class="solution">
        <h2>Решение:</h2>
        
        <div v-if="solution.type !== 'a=0'" class="solution-step">
          <strong>Дискриминант:</strong> D = b² - 4ac = {{ solution.discriminant }}
        </div>

        <div v-if="solution.type === 'two-roots'" class="solution-step">
          <strong>Два действительных корня:</strong><br>
          x₁ = {{ solution.x1.toFixed(4) }}<br>
          x₂ = {{ solution.x2.toFixed(4) }}
        </div>

        <div v-else-if="solution.type === 'one-root'" class="solution-step">
          <strong>Один действительный корень:</strong><br>
          x = {{ solution.x.toFixed(4) }}
        </div>

        <div v-else-if="solution.type === 'complex-roots'" class="solution-step">
          <strong>Два комплексных корня:</strong><br>
          x₁ = {{ solution.real.toFixed(4) }} + {{ solution.imaginary.toFixed(4) }}i<br>
          x₂ = {{ solution.real.toFixed(4) }} - {{ solution.imaginary.toFixed(4) }}i
        </div>

        <div v-else-if="solution.type === 'a=0'" class="solution-step">
          <strong>Один действительный корень:</strong><br>
          x = {{ solution.x.toFixed(4) }}
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.quadratic-solver {
  min-height: 100vh;
  background: linear-gradient(135deg, #000c65 0%, #570000 50%, #010057 100%);
  padding: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: 'Arial';
}

.container {
  background: white;
  border-radius: 20px;
  padding: 30px;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
  max-width: 800px;
  width: 100%;
}

h1 {
  text-align: center;
  color: #333;
  margin-bottom: 30px;
  font-size: 2rem;
}

.equation-display {
  text-align: center;
  font-size: 1.5rem;
  margin-bottom: 30px;
  padding: 20px;
  background: #f8f9fa;
  border-radius: 10px;
  font-family: 'Courier New', monospace;
}

.input-group {
  margin-bottom: 30px;
}

.input-row {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
  gap: 10px;
}

.coefficient-input {
  width: 80px;
  padding: 12px;
  border: 2px solid #e1e5e9;
  border-radius: 8px;
  font-size: 1.1rem;
  text-align: center;
  transition: border-color 0.3s ease;
}

.coefficient-input:focus {
  outline: none;
  border-color: #ea6666;
}

.sign-select {
  padding: 12px;
  border: 2px solid #e1e5e9;
  border-radius: 8px;
  font-size: 1.1rem;
  background: white;
  cursor: pointer;
}

.equation-part {
  font-size: 1.3rem;
  font-weight: bold;
  color: #333;
  margin: 0 5px;
}

.controls {
  display: flex;
  gap: 15px;
  justify-content: center;
  margin-bottom: 30px;
  flex-wrap: wrap;
}

.btn {
  padding: 15px 30px;
  border: none;
  border-radius: 8px;
  font-size: 1.1rem;
  cursor: pointer;
  transition: all 0.3s ease;
  font-weight: 600;
}

.solve-btn {
  background: #ea6666;
  color: white;
}

.solve-btn:hover {
  background: #d85a5a;
  transform: translateY(-2px);
}

.clear-btn {
  background: #6c757d;
  color: white;
}

.clear-btn:hover {
  background: #5a6268;
  transform: translateY(-2px);
}

.error-message {
  background: #f8d7da;
  color: #721c24;
  padding: 15px;
  border-radius: 8px;
  text-align: center;
  margin-bottom: 20px;
  border: 1px solid #f5c6cb;
}

.solution {
  background: #f8f9fa;
  padding: 25px;
  border-radius: 10px;
  border-left: 5px solid #ea6666;
}

.solution h2 {
  color: #333;
  margin-bottom: 20px;
  text-align: center;
}

.solution-step {
  margin-bottom: 15px;
  padding: 10px;
  background: white;
  border-radius: 8px;
  border-left: 3px solid #a72828;
}

@media (max-width: 768px) {
  .quadratic-solver {
    padding: 10px;
  }

  .container {
    padding: 20px;
    margin: 10px;
  }

  h1 {
    font-size: 1.5rem;
  }

  .equation-display {
    font-size: 1.1rem;
    padding: 15px;
  }

  .input-row {
    gap: 5px;
  }

  .coefficient-input {
    width: 60px;
    padding: 10px;
    font-size: 1rem;
  }

  .sign-select {
    padding: 10px;
    font-size: 1rem;
  }

  .equation-part {
    font-size: 1.1rem;
  }

  .btn {
    padding: 12px 20px;
    font-size: 1rem;
    width: 100%;
    max-width: 200px;
  }

  .controls {
    flex-direction: column;
    align-items: center;
  }
}

@media (max-width: 480px) {
  .input-row {
    flex-direction: column;
    align-items: stretch;
  }

  .coefficient-input {
    width: 100%;
    max-width: 120px;
    margin: 0 auto;
  }

  .sign-select {
    width: 100%;
    max-width: 80px;
    margin: 0 auto;
  }
}
</style>