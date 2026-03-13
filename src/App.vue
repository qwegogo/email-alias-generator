<script setup>
import { ref, computed, onMounted } from 'vue'

const username = ref('')
const domain = ref('')
const useNumbers = ref(false)
const useLetters = ref(true)
const quantity = ref(10)
const minLength = ref(3)
const output = ref('')
const copied = ref(false)

const theme = ref('auto')

const quantities = [10, 50, 500, 5000]
const lengths = [2, 3, 4, 5, 6, 7, 8, 9, 10]

onMounted(() => {
  const saved = localStorage.getItem('theme')
  if (saved) {
    theme.value = saved
    applyTheme(saved)
  } else {
    applyTheme('auto')
  }
})

function applyTheme(t) {
  const root = document.documentElement
  root.classList.remove('light', 'dark')
  if (t === 'dark') {
    root.classList.add('dark')
  } else if (t === 'light') {
    root.classList.add('light')
  }
}

function setTheme(t) {
  theme.value = t
  localStorage.setItem('theme', t)
  applyTheme(t)
}

function getCharset() {
  let chars = ''
  if (useLetters.value) chars += 'abcdefghijklmnopqrstuvwxyz'
  if (useNumbers.value) chars += '0123456789'
  return chars || 'abcdefghijklmnopqrstuvwxyz'
}

function generateRandomString(length, charset) {
  let result = ''
  for (let i = 0; i < length; i++) {
    result += charset.charAt(Math.floor(Math.random() * charset.length))
  }
  return result
}

function calculateCapacity(length, charsetLength) {
  return Math.pow(charsetLength, length)
}

function generate() {
  if (!username.value.trim() || !domain.value.trim()) {
    alert('Please enter both username and domain')
    return
  }
  
  if (!useNumbers.value && !useLetters.value) {
    alert('Please select at least one alias type')
    return
  }

  const charset = getCharset()
  const charsetLength = charset.length
  const results = new Set()
  let currentLength = minLength.value
  
  while (results.size < quantity.value) {
    const remaining = quantity.value - results.size
    const currentCapacity = calculateCapacity(currentLength, charsetLength)
    const usedWithCurrentLength = results.size
    
    let attempts = 0
    const maxAttempts = Math.min(currentCapacity * 2, 100000)
    
    while (results.size < quantity.value && attempts < maxAttempts) {
      const suffix = generateRandomString(currentLength, charset)
      const email = `${username.value}+${suffix}@${domain.value}`
      results.add(email)
      attempts++
    }
    
    if (results.size < quantity.value) {
      currentLength++
    }
  }

  output.value = Array.from(results).join('\n')
  copied.value = false
}

async function copyToClipboard() {
  if (!output.value) return
  
  try {
    await navigator.clipboard.writeText(output.value)
    copied.value = true
    setTimeout(() => {
      copied.value = false
    }, 2000)
  } catch {
    alert('Failed to copy. Please select and copy manually.')
  }
}
</script>

<template>
  <div class="min-h-screen transition-colors duration-300" 
       :style="{ backgroundColor: 'var(--bg-primary)', color: 'var(--text-primary)' }">
    
    <header class="flex justify-between items-center px-6 py-4 border-b"
            :style="{ borderColor: 'var(--border-color)' }">
      <h1 class="text-xl font-bold flex items-center gap-2">
        <span>📧</span> Email Alias Generator
        <a href="https://kd05.com" 
           target="_blank" 
           rel="noopener"
           class="text-xs font-normal opacity-50 hover:opacity-100 transition-opacity no-underline"
           :style="{ color: 'var(--text-secondary)' }">
          by kd05.com
        </a>
      </h1>
      
      <div class="flex gap-1 p-1 rounded-lg" 
           :style="{ backgroundColor: 'var(--bg-secondary)' }">
        <button @click="setTheme('auto')" 
                :class="['px-3 py-1 rounded text-sm', theme === 'auto' ? 'bg-blue-500 text-white' : '']">
          Auto
        </button>
        <button @click="setTheme('light')" 
                :class="['px-3 py-1 rounded text-sm', theme === 'light' ? 'bg-blue-500 text-white' : '']">
          Light
        </button>
        <button @click="setTheme('dark')" 
                :class="['px-3 py-1 rounded text-sm', theme === 'dark' ? 'bg-blue-500 text-white' : '']">
          Dark
        </button>
      </div>
    </header>

    <main class="max-w-3xl mx-auto px-6 py-8">
      
      <section class="mb-8">
        <p class="text-lg" :style="{ color: 'var(--text-secondary)' }">
          Generate unlimited random email aliases for <strong>Gmail</strong>, 
          <strong>Outlook</strong>, <strong>iCloud</strong>, <strong>ProtonMail</strong> and more.
          Perfect for testing, spam prevention, and privacy protection.
        </p>
      </section>

      <section class="space-y-6 mb-8">
        
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <div>
            <label class="block mb-2 font-medium">Email Username</label>
            <input 
              v-model="username"
              type="text" 
              placeholder="e.g. bigcat"
              class="w-full px-4 py-3 rounded-lg border text-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
              :style="{ 
                backgroundColor: 'var(--bg-secondary)', 
                borderColor: 'var(--border-color)',
                color: 'var(--text-primary)'
              }"
            >
          </div>
          
          <div>
            <label class="block mb-2 font-medium">Domain</label>
            <input 
              v-model="domain"
              type="text" 
              placeholder="e.g. gmail.com"
              class="w-full px-4 py-3 rounded-lg border text-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
              :style="{ 
                backgroundColor: 'var(--bg-secondary)', 
                borderColor: 'var(--border-color)',
                color: 'var(--text-primary)'
              }"
            >
          </div>
        </div>

        <div>
          <label class="block mb-2 font-medium">Alias Type</label>
          <div class="flex gap-6">
            <label class="flex items-center gap-2 cursor-pointer">
              <input 
                type="checkbox" 
                v-model="useNumbers"
                class="w-5 h-5 rounded"
              >
              <span>Numbers</span>
            </label>
            <label class="flex items-center gap-2 cursor-pointer">
              <input 
                type="checkbox" 
                v-model="useLetters"
                class="w-5 h-5 rounded"
              >
              <span>Letters</span>
            </label>
          </div>
          <p class="text-sm mt-1" :style="{ color: 'var(--text-secondary)' }">
            At least one type is required
          </p>
        </div>

        <div class="flex flex-wrap gap-4 items-end">
          <div class="flex-1 min-w-[150px]">
            <label class="block mb-2 font-medium">Quantity</label>
            <select 
              v-model="quantity"
              class="w-full px-4 py-3 rounded-lg border text-lg focus:outline-none focus:ring-2 focus:ring-blue-500 cursor-pointer"
              :style="{ 
                backgroundColor: 'var(--bg-secondary)', 
                borderColor: 'var(--border-color)',
                color: 'var(--text-primary)'
              }"
            >
              <option v-for="q in quantities" :key="q" :value="q">{{ q }}</option>
            </select>
          </div>
          
          <div class="flex-1 min-w-[150px]">
            <label class="block mb-2 font-medium">Min Length</label>
            <select 
              v-model="minLength"
              class="w-full px-4 py-3 rounded-lg border text-lg focus:outline-none focus:ring-2 focus:ring-blue-500 cursor-pointer"
              :style="{ 
                backgroundColor: 'var(--bg-secondary)', 
                borderColor: 'var(--border-color)',
                color: 'var(--text-primary)'
              }"
            >
              <option v-for="l in lengths" :key="l" :value="l">{{ l }}</option>
            </select>
          </div>
          
          <button 
            @click="generate"
            class="px-8 py-3 bg-blue-500 hover:bg-blue-600 text-white font-medium rounded-lg text-lg transition-colors"
          >
            🎲 Generate
          </button>
        </div>
      </section>

      <section class="mb-8">
        <label class="block mb-2 font-medium">Generated Aliases</label>
        <div class="relative">
          <textarea 
            v-model="output"
            readonly
            rows="12"
            placeholder="Generated email aliases will appear here..."
            class="w-full px-4 py-3 rounded-lg border text-lg font-mono focus:outline-none focus:ring-2 focus:ring-blue-500 resize-y"
            :style="{ 
              backgroundColor: 'var(--bg-secondary)', 
              borderColor: 'var(--border-color)',
              color: 'var(--text-primary)'
            }"
          ></textarea>
          <button 
            v-if="output"
            @click="copyToClipboard"
            class="absolute top-3 right-3 px-4 py-2 rounded-lg font-medium transition-colors"
            :class="copied ? 'bg-green-500 text-white' : 'bg-gray-200 hover:bg-gray-300 dark:bg-gray-700 dark:hover:bg-gray-600'"
          >
            {{ copied ? '✓ Copied!' : '📋 Copy All' }}
          </button>
        </div>
      </section>

      <section class="p-4 rounded-lg mb-8 flex items-center gap-3"
               :style="{ backgroundColor: 'var(--bg-secondary)' }">
        <span class="text-2xl">🔒</span>
        <p :style="{ color: 'var(--text-secondary)' }">
          <strong>100% Private</strong> — No data is collected, stored, or transmitted. 
          Everything runs locally in your browser.
        </p>
      </section>

      <section class="border-t pt-8" :style="{ borderColor: 'var(--border-color)' }">
        <h2 class="text-xl font-bold mb-4">📌 Use Cases</h2>
        <ul class="space-y-3">
          <li>
            <strong>Email Alias Generation</strong> — 
            <span :style="{ color: 'var(--text-secondary)' }">
              Create unique aliases for Gmail, Outlook, iCloud, and ProtonMail to organize your inbox and filter unwanted emails.
            </span>
          </li>
          <li>
            <strong>Software Testing</strong> — 
            <span :style="{ color: 'var(--text-secondary)' }">
              Generate bulk test email addresses for QA, unit testing, and development environments.
            </span>
          </li>
          <li>
            <strong>Form Testing</strong> — 
            <span :style="{ color: 'var(--text-secondary)' }">
              Quickly populate email fields during website and app testing without using real addresses.
            </span>
          </li>
          <li>
            <strong>Spam Prevention</strong> — 
            <span :style="{ color: 'var(--text-secondary)' }">
              Use unique aliases when signing up for services to track and block spam sources.
            </span>
          </li>
          <li>
            <strong>Privacy Protection</strong> — 
            <span :style="{ color: 'var(--text-secondary)' }">
              Keep your primary email address private while still receiving messages.
            </span>
          </li>
        </ul>
      </section>

    </main>

    <footer class="text-center py-6 border-t"
            :style="{ borderColor: 'var(--border-color)', color: 'var(--text-secondary)' }">
      <p>
        Email Alias Generator — Free & Open Source
        <br>
        <a href="https://kd05.com" 
           target="_blank" 
           rel="noopener"
           class="hover:underline">
          Developed by kd05.com
        </a>
      </p>
    </footer>
    
  </div>
</template>