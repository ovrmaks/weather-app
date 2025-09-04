<template>
  <div id="app">
    <header class="app-header">
      </header>

    <main class="app-main">
      <SearchBar :loading="loading" @search="handleSearch" />

      <div v-if="loading" class="loading-container">
        <div class="loading-spinner"></div>
        <p class="loading-text">Загрузка данных о погоде...</p>
      </div>

      <div v-else-if="error" class="error-container">
        <div class="error-icon">😢</div>
        <h2 class="error-title">Произошла ошибка</h2>
        <p class="error-message">{{ error }}</p>
        <button @click="retryLastSearch" class="retry-button">Попробовать снова</button>
      </div>
      
      <WeatherDisplay v-else :weather-data="weatherData" />
      
      </main>

    <footer class="app-footer">
      </footer>
  </div>
</template>

<script>
// Импортируем axios и компоненты
import axios from 'axios';
import WeatherDisplay from './components/WeatherDisplay.vue';
import SearchBar from './components/SearchBar.vue'; // Убедись, что путь верный

export default {
  name: 'App',
  components: {
    WeatherDisplay,
    SearchBar
  },
  data() {
    return {
      weatherData: null,
      loading: true,
      error: null,
      apiKey: '0d53ae18071b793e22d65fe3e98ffa21', // ВАЖНО: Вставь сюда ключ от OpenWeatherMap
      apiUrl: 'https://api.openweathermap.org/data/2.5/weather',
      lastCity: 'Москва' // Сохраняем последний город для кнопки "повторить"
    }
  },
  // Хук created() вызывается при создании компонента
  created() {
    this.fetchWeather(this.lastCity);
  },
  methods: {
    // Асинхронный метод для получения погоды
    async fetchWeather(city) {
      this.loading = true;
      this.error = null;
      this.lastCity = city;

      try {
        const response = await axios.get(this.apiUrl, {
          params: {
            q: city,
            appid: this.apiKey,
            units: 'metric', // Получаем градусы в Цельсиях
            lang: 'ru'      // Получаем описание на русском
          }
        });
        this.weatherData = response.data;
      } catch (err) {
        if (err.response && err.response.status === 404) {
          this.error = `Город "${city}" не найден. Проверьте правильность написания.`;
        } else {
          this.error = 'Не удалось загрузить данные. Проверьте соединение с интернетом.';
        }
        this.weatherData = null; // Очищаем старые данные при ошибке
      } finally {
        this.loading = false; // В любом случае убираем индикатор загрузки
      }
    },

    // Метод, который вызывается при событии 'search' от SearchBar
    handleSearch(city) {
      this.fetchWeather(city);
    },

    // Метод для кнопки "Попробовать снова"
    retryLastSearch() {
      this.fetchWeather(this.lastCity);
    }
  }
}
</script>

<style lang="scss">
// Переменные
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
$shadow-large: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);

// Общие стили
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  background-color: $background-color;
  color: $text-primary;
  line-height: 1.6;
}

#app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

// Хедер
.app-header {
  background: linear-gradient(135deg, $primary-color 0%, #3b82f6 100%);
  color: white;
  padding: 1.5rem 2rem;
  box-shadow: $shadow-medium;

  .app-title {
    font-size: 1.875rem;
    font-weight: 700;
    margin-bottom: 1rem;
    display: flex;
    align-items: center;
    gap: 0.75rem;

    .weather-icon {
      font-size: 2rem;
    }
  }

  .app-nav {
    display: flex;
    gap: 1rem;

    .nav-button {
      background: rgba(255, 255, 255, 0.2);
      border: 1px solid rgba(255, 255, 255, 0.3);
      color: white;
      padding: 0.5rem 1rem;
      border-radius: 0.5rem;
      cursor: pointer;
      transition: all 0.2s ease;
      font-weight: 500;

      &:hover {
        background: rgba(255, 255, 255, 0.3);
        transform: translateY(-1px);
      }

      &.active {
        background: rgba(255, 255, 255, 0.9);
        color: $primary-color;
      }
    }
  }
}

// Основной контент
.app-main {
  flex: 1;
  padding: 2rem;
  max-width: 1200px;
  margin: 0 auto;
  width: 100%;
}

// Секция поиска
.search-section {
  margin-bottom: 2rem;

  .search-container {
    display: flex;
    max-width: 500px;
    margin: 0 auto;
    box-shadow: $shadow-medium;
    border-radius: 0.75rem;
    overflow: hidden;

    .search-input {
      flex: 1;
      padding: 1rem 1.25rem;
      border: none;
      font-size: 1rem;
      outline: none;
      background: $card-background;

      &::placeholder {
        color: $text-secondary;
      }
    }

    .search-button {
      background: $primary-color;
      border: none;
      color: white;
      padding: 1rem 1.25rem;
      cursor: pointer;
      transition: background-color 0.2s ease;

      &:hover {
        background: #1d4ed8;
      }

      .search-icon {
        font-size: 1.2rem;
      }
    }
  }
}

// Загрузка и ошибки
.loading-container, .error-container {
  text-align: center;
  padding: 3rem 2rem;
  background: $card-background;
  border-radius: 1rem;
  box-shadow: $shadow-light;
  border: 1px solid $border-color;
  margin-bottom: 2rem;
}

.loading-container {
  .loading-spinner {
    width: 40px;
    height: 40px;
    border: 4px solid $border-color;
    border-top: 4px solid $primary-color;
    border-radius: 50%;
    animation: spin 1s linear infinite;
    margin: 0 auto 1rem;
  }

  .loading-text {
    color: $text-secondary;
    font-size: 1.125rem;
  }

  @keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
  }
}

.error-container {
  .error-icon {
    font-size: 3rem;
    margin-bottom: 1rem;
  }

  .error-title {
    color: $danger-color;
    font-size: 1.5rem;
    margin-bottom: 1rem;
  }

  .error-message {
    color: $text-secondary;
    margin-bottom: 1.5rem;
    line-height: 1.6;
  }

  .retry-button {
    background: $primary-color;
    color: white;
    border: none;
    padding: 0.75rem 1.5rem;
    border-radius: 0.5rem;
    cursor: pointer;
    font-weight: 500;
    transition: background-color 0.2s ease;

    &:hover {
      background: #1d4ed8;
    }
  }
}

// Обновленные стили для дополнительной информации
.additional-info {
  margin-top: 2rem;

  .info-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 1.5rem;

    .info-card {
      background: $card-background;
      border-radius: 1rem;
      padding: 1.5rem;
      box-shadow: $shadow-light;
      border: 1px solid $border-color;
      transition: transform 0.2s ease, box-shadow 0.2s ease;

      &:hover {
        transform: translateY(-2px);
        box-shadow: $shadow-medium;
      }

      .info-title {
        font-size: 1.125rem;
        font-weight: 600;
        margin-bottom: 1rem;
        color: $text-primary;
      }

      .info-content {
        .sun-time {
          display: flex;
          align-items: center;
          justify-content: space-between;
          margin-bottom: 0.5rem;

          .sun-icon {
            font-size: 1.5rem;
          }

          .time {
            font-weight: 600;
            font-size: 1.125rem;
          }
        }

        .visibility-info {
          text-align: center;

          .visibility-label {
            display: block;
            font-weight: 600;
            font-size: 1.125rem;
            margin-bottom: 0.25rem;
            color: $success-color;
          }

          .visibility-value {
            color: $text-secondary;
          }
        }

        .coordinates {
          .coord-label {
            display: block;
            margin-bottom: 0.5rem;
            font-size: 0.875rem;
            color: $text-secondary;
          }
        }
      }
    }
  }
}

// Футер
.app-footer {
  background: $card-background;
  border-top: 1px solid $border-color;
  padding: 1.5rem 2rem;
  text-align: center;
  color: $text-secondary;
  margin-top: auto;
}

// Адаптивность
@media (max-width: 768px) {
  .app-header {
    padding: 1rem;

    .app-title {
      font-size: 1.5rem;
    }

    .app-nav {
      .nav-button {
        padding: 0.375rem 0.75rem;
        font-size: 0.875rem;
      }
    }
  }

  .app-main {
    padding: 1rem;
  }

  .search-section .search-container {
    margin: 0 1rem;
  }

  .additional-info .info-grid {
    grid-template-columns: 1fr;
    gap: 1rem;
  }

  .app-footer {
    padding: 1rem;
    font-size: 0.875rem;
  }
}
</style>