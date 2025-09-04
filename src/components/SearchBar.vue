<template>
    <section class="search-section">
      <div class="search-container">
        <input 
          type="text" 
          class="search-input" 
          placeholder="Введите название города..."
          v-model="searchQuery"
          @keyup.enter="handleSearch"
          @input="clearError"
          :disabled="loading"
          ref="searchInput"
        >
        <button 
          class="search-button"
          @click="handleSearch"
          :disabled="loading || !searchQuery.trim()"
        >
          <span v-if="loading" class="search-spinner">⏳</span>
          <span v-else class="search-icon">🔍</span>
        </button>
      </div>
      
      <!-- Быстрые варианты городов -->
      <div class="quick-cities" v-if="!loading">
        <p class="quick-cities-label">Популярные города:</p>
        <div class="cities-buttons">
          <button 
            v-for="city in popularCities" 
            :key="city"
            @click="selectCity(city)"
            class="city-button"
          >
            {{ city }}
          </button>
        </div>
      </div>
  
      <!-- История поиска -->
      <div class="search-history" v-if="searchHistory.length > 0 && !loading">
        <p class="history-label">Недавние поиски:</p>
        <div class="history-buttons">
          <button 
            v-for="city in searchHistory" 
            :key="city"
            @click="selectCity(city)"
            class="history-button"
          >
            <span class="history-icon">🕐</span>
            {{ city }}
            <button 
              @click.stop="removeFromHistory(city)"
              class="remove-history"
              title="Удалить из истории"
            >
              ×
            </button>
          </button>
        </div>
      </div>
    </section>
  </template>
  
  <script>
  export default {
    name: 'SearchBar',
    props: {
      loading: {
        type: Boolean,
        default: false
      }
    },
    data() {
      return {
        searchQuery: '',
        popularCities: [
          'Москва', 'Санкт-Петербург', 'Новосибирск', 'Екатеринбург', 
          'Казань', 'Нижний Новгород', 'Самара', 'Омск', 'Ростов-на-Дону', 'Уфа'
        ],
        searchHistory: []
      }
    },
    mounted() {
      // Загружаем историю поиска из localStorage
      const savedHistory = localStorage.getItem('weatherSearchHistory')
      if (savedHistory) {
        this.searchHistory = JSON.parse(savedHistory)
      }
  
      // Фокусируемся на поле поиска
      this.$refs.searchInput.focus()
    },
    methods: {
      handleSearch() {
        const city = this.searchQuery.trim()
        if (city && !this.loading) {
          // Добавляем в историю
          this.addToHistory(city)
          
          // Отправляем событие родительскому компоненту
          this.$emit('search', city)
          
          // Очищаем поле поиска
          this.searchQuery = ''
        }
      },
  
      selectCity(city) {
        if (!this.loading) {
          this.addToHistory(city)
          this.$emit('search', city)
        }
      },
  
      addToHistory(city) {
        // Убираем дубликаты и добавляем город в начало
        const filteredHistory = this.searchHistory.filter(
          item => item.toLowerCase() !== city.toLowerCase()
        )
        this.searchHistory = [city, ...filteredHistory].slice(0, 5) // Максимум 5 элементов
        
        // Сохраняем в localStorage
        localStorage.setItem('weatherSearchHistory', JSON.stringify(this.searchHistory))
      },
  
      removeFromHistory(city) {
        this.searchHistory = this.searchHistory.filter(
          item => item.toLowerCase() !== city.toLowerCase()
        )
        
        // Обновляем localStorage
        localStorage.setItem('weatherSearchHistory', JSON.stringify(this.searchHistory))
      },
  
      clearError() {
        // Можно использовать для очистки ошибок в режиме реального времени
        this.$emit('clear-error')
      }
    }
  }
  </script>
  
  <style lang="scss" scoped>
  // Переменные (должны совпадать с App.vue)
  $primary-color: #2563eb;
  $secondary-color: #64748b;
  $success-color: #10b981;
  $warning-color: #f59e0b;
  $danger-color: #ef4444;
  $background-color: #f8fafc;
  $card-background: #ffffff;
  $text-primary: #1e293b;
  $text-secondary: #64748b;
  $border-color: #e2e8f0;
  $shadow-light: 0 1px 3px 0 rgba(0, 0, 0, 0.1), 0 1px 2px 0 rgba(0, 0, 0, 0.06);
  $shadow-medium: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
  
  .search-section {
    margin-bottom: 2rem;
  
    .search-container {
      display: flex;
      max-width: 500px;
      margin: 0 auto 1.5rem;
      box-shadow: $shadow-medium;
      border-radius: 0.75rem;
      overflow: hidden;
      transition: box-shadow 0.3s ease;
  
      &:focus-within {
        box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.1), $shadow-medium;
      }
  
      .search-input {
        flex: 1;
        padding: 1rem 1.25rem;
        border: none;
        font-size: 1rem;
        outline: none;
        background: $card-background;
        transition: background-color 0.2s ease;
  
        &::placeholder {
          color: $text-secondary;
        }
  
        &:disabled {
          background-color: $background-color;
          cursor: not-allowed;
        }
      }
  
      .search-button {
        background: $primary-color;
        border: none;
        color: white;
        padding: 1rem 1.25rem;
        cursor: pointer;
        transition: all 0.2s ease;
        min-width: 60px;
  
        &:hover:not(:disabled) {
          background: #1d4ed8;
          transform: scale(1.05);
        }
  
        &:disabled {
          background: $secondary-color;
          cursor: not-allowed;
        }
  
        .search-icon, .search-spinner {
          font-size: 1.2rem;
          display: inline-block;
        }
  
        .search-spinner {
          animation: spin 1s linear infinite;
        }
  
        @keyframes spin {
          from { transform: rotate(0deg); }
          to { transform: rotate(360deg); }
        }
      }
    }
  
    // Популярные города
    .quick-cities {
      text-align: center;
      margin-bottom: 1.5rem;
  
      .quick-cities-label {
        font-size: 0.875rem;
        color: $text-secondary;
        margin-bottom: 0.75rem;
        font-weight: 500;
      }
  
      .cities-buttons {
        display: flex;
        flex-wrap: wrap;
        gap: 0.5rem;
        justify-content: center;
        max-width: 600px;
        margin: 0 auto;
  
        .city-button {
          background: $card-background;
          border: 1px solid $border-color;
          color: $text-primary;
          padding: 0.5rem 1rem;
          border-radius: 1.5rem;
          cursor: pointer;
          transition: all 0.2s ease;
          font-size: 0.875rem;
          font-weight: 500;
  
          &:hover {
            background: $primary-color;
            color: white;
            border-color: $primary-color;
            transform: translateY(-1px);
          }
        }
      }
    }
  
    // История поиска
    .search-history {
      text-align: center;
  
      .history-label {
        font-size: 0.875rem;
        color: $text-secondary;
        margin-bottom: 0.75rem;
        font-weight: 500;
      }
  
      .history-buttons {
        display: flex;
        flex-wrap: wrap;
        gap: 0.5rem;
        justify-content: center;
        max-width: 600px;
        margin: 0 auto;
  
        .history-button {
          background: rgba(37, 99, 235, 0.1);
          border: 1px solid rgba(37, 99, 235, 0.2);
          color: $text-primary;
          padding: 0.5rem 0.75rem;
          border-radius: 1.5rem;
          cursor: pointer;
          transition: all 0.2s ease;
          font-size: 0.875rem;
          display: flex;
          align-items: center;
          gap: 0.5rem;
          position: relative;
  
          &:hover {
            background: rgba(37, 99, 235, 0.2);
            transform: translateY(-1px);
  
            .remove-history {
              opacity: 1;
            }
          }
  
          .history-icon {
            font-size: 0.75rem;
          }
  
          .remove-history {
            background: rgba(239, 68, 68, 0.1);
            border: none;
            color: $danger-color;
            width: 18px;
            height: 18px;
            border-radius: 50%;
            cursor: pointer;
            font-size: 12px;
            line-height: 1;
            opacity: 0;
            transition: all 0.2s ease;
            margin-left: 0.25rem;
  
            &:hover {
              background: rgba(239, 68, 68, 0.2);
              transform: scale(1.1);
            }
          }
        }
      }
    }
  }
  
  // Адаптивность
  @media (max-width: 768px) {
    .search-section {
      .search-container {
        margin: 0 1rem 1rem;
      }
  
      .quick-cities .cities-buttons,
      .search-history .history-buttons {
        gap: 0.375rem;
  
        .city-button,
        .history-button {
          font-size: 0.8125rem;
          padding: 0.375rem 0.75rem;
        }
      }
    }
  }
  
  @media (max-width: 480px) {
    .search-section {
      .quick-cities .cities-buttons,
      .search-history .history-buttons {
        .city-button,
        .history-button {
          font-size: 0.75rem;
          padding: 0.25rem 0.5rem;
        }
      }
    }
  }
  </style>