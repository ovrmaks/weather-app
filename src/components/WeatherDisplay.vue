<template>
    <section class="weather-display" v-if="weatherData">
      <article class="current-weather">
        <header class="weather-header">
          <div class="location-info">
            <h2 class="city-name">{{ weatherData.name }}</h2>
            <p class="country-name">{{ weatherData.sys.country }}</p>
            <time class="update-time">Обновлено: {{ formatUpdateTime(weatherData.dt) }}</time>
          </div>
          <div class="weather-icon-large">
            <span class="weather-emoji">{{ getWeatherIcon(weatherData.weather[0].icon) }}</span>
          </div>
        </header>
  
        <div class="temperature-section">
          <div class="main-temperature">
            <span class="temperature-value">{{ Math.round(weatherData.main.temp) }}</span>
            <span class="temperature-unit">°C</span>
          </div>
          <div class="temperature-details">
            <p class="weather-description">{{ weatherData.weather[0].description }}</p>
            <p class="feels-like">Ощущается как {{ Math.round(weatherData.main.feels_like) }}°C</p>
            <div class="temp-range">
              <span class="temp-high">Макс: {{ Math.round(weatherData.main.temp_max) }}°C</span>
              <span class="temp-low">Мин: {{ Math.round(weatherData.main.temp_min) }}°C</span>
            </div>
          </div>
        </div>
      </article>
  
      </section>
  </template>
  
  <script>
  export default {
    name: 'WeatherDisplay',
    props: {
      // Объявляем, что компонент ожидает получить объект weatherData
      weatherData: {
        type: Object,
        required: true,
      },
    },
    methods: {
      // Метод для форматирования времени из Unix timestamp в читаемый вид
      formatUpdateTime(timestamp) {
        const date = new Date(timestamp * 1000);
        return date.toLocaleTimeString('ru-RU', { hour: '2-digit', minute: '2-digit' });
      },
      // Метод для преобразования кода иконки от API в эмодзи
      getWeatherIcon(iconCode) {
        const iconMap = {
          '01d': '☀️', '01n': '🌙',
          '02d': '⛅️', '02n': '☁️',
          '03d': '☁️', '03n': '☁️',
          '04d': '☁️', '04n': '☁️',
          '09d': '🌦️', '09n': '🌦️',
          '10d': '🌧️', '10n': '🌧️',
          '11d': '⛈️', '11n': '⛈️',
          '13d': '❄️', '13n': '❄️',
          '50d': '🌫️', '50n': '🌫️',
        };
        return iconMap[iconCode] || '🌍';
      }
    }
  };
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
  $shadow-large: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
  
  .weather-display {
    display: grid;
    gap: 2rem;
    
    // Текущая погода
    .current-weather {
      background: linear-gradient(135deg, $primary-color 0%, #3b82f6 100%);
      border-radius: 1.5rem;
      padding: 2rem;
      color: white;
      box-shadow: $shadow-large;
      position: relative;
      overflow: hidden;
  
      &::before {
        content: '';
        position: absolute;
        top: -50%;
        right: -50%;
        width: 200%;
        height: 200%;
        background: radial-gradient(circle, rgba(255, 255, 255, 0.1) 0%, transparent 70%);
        animation: float 6s ease-in-out infinite;
      }
  
      @keyframes float {
        0%, 100% { transform: translateY(0px) rotate(0deg); }
        50% { transform: translateY(-20px) rotate(180deg); }
      }
  
      .weather-header {
        display: flex;
        justify-content: space-between;
        align-items: flex-start;
        margin-bottom: 2rem;
        position: relative;
        z-index: 1;
  
        .location-info {
          .city-name {
            font-size: 2rem;
            font-weight: 700;
            margin-bottom: 0.25rem;
          }
  
          .country-name {
            font-size: 1.125rem;
            opacity: 0.9;
            margin-bottom: 0.5rem;
          }
  
          .update-time {
            font-size: 0.875rem;
            opacity: 0.8;
          }
        }
  
        .weather-icon-large {
          .weather-emoji {
            font-size: 4rem;
            filter: drop-shadow(0 4px 8px rgba(0, 0, 0, 0.3));
          }
        }
      }
  
      .temperature-section {
        position: relative;
        z-index: 1;
  
        .main-temperature {
          display: flex;
          align-items: baseline;
          margin-bottom: 1rem;
  
          .temperature-value {
            font-size: 4.5rem;
            font-weight: 300;
            line-height: 1;
          }
  
          .temperature-unit {
            font-size: 2rem;
            margin-left: 0.25rem;
            opacity: 0.8;
          }
        }
  
        .temperature-details {
          .weather-description {
            font-size: 1.25rem;
            margin-bottom: 0.5rem;
            text-transform: capitalize;
          }
  
          .feels-like {
            font-size: 1rem;
            margin-bottom: 1rem;
            opacity: 0.9;
          }
  
          .temp-range {
            display: flex;
            gap: 1rem;
  
            .temp-high, .temp-low {
              padding: 0.25rem 0.75rem;
              background: rgba(255, 255, 255, 0.2);
              border-radius: 1rem;
              font-size: 0.875rem;
            }
          }
        }
      }
    }
  
    // Детальная информация
    .weather-details {
      .details-title {
        font-size: 1.5rem;
        font-weight: 600;
        margin-bottom: 1.5rem;
        color: $text-primary;
      }
  
      .details-grid {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
        gap: 1rem;
  
        .detail-card {
          background: $card-background;
          border-radius: 1rem;
          padding: 1.5rem;
          box-shadow: $shadow-light;
          border: 1px solid $border-color;
          transition: all 0.3s ease;
          display: flex;
          align-items: center;
          gap: 1rem;
  
          &:hover {
            transform: translateY(-2px);
            box-shadow: $shadow-medium;
          }
  
          .detail-icon {
            font-size: 2rem;
            flex-shrink: 0;
          }
  
          .detail-content {
            .detail-label {
              font-size: 0.875rem;
              font-weight: 600;
              color: $text-secondary;
              margin-bottom: 0.25rem;
            }
  
            .detail-value {
              font-size: 1.25rem;
              font-weight: 700;
              color: $text-primary;
              margin-bottom: 0.25rem;
            }
  
            .detail-description {
              font-size: 0.75rem;
              color: $text-secondary;
            }
          }
        }
      }
    }
  
    // Заглушка для прогнозов
    .forecast-placeholder {
      background: $card-background;
      border-radius: 1rem;
      padding: 2rem;
      text-align: center;
      box-shadow: $shadow-light;
      border: 1px solid $border-color;
  
      .forecast-title {
        font-size: 1.5rem;
        font-weight: 600;
        margin-bottom: 1.5rem;
        color: $text-primary;
      }
  
      .placeholder-content {
        .placeholder-icon {
          font-size: 3rem;
          margin-bottom: 1rem;
          opacity: 0.5;
        }
  
        .placeholder-text {
          color: $text-secondary;
          line-height: 1.6;
          margin-bottom: 1.5rem;
          max-width: 500px;
          margin-left: auto;
          margin-right: auto;
        }
  
        .placeholder-button {
          background: $secondary-color;
          color: white;
          border: none;
          padding: 0.5rem 1rem;
          border-radius: 0.5rem;
          cursor: pointer;
          transition: background-color 0.2s ease;
  
          &:hover {
            background: darken($secondary-color, 10%);
          }
        }
      }
    }
  
    // Скелетон загрузки
    .weather-skeleton {
      .skeleton-header {
        background: $card-background;
        border-radius: 1.5rem;
        padding: 2rem;
        margin-bottom: 2rem;
        display: flex;
        justify-content: space-between;
        align-items: flex-start;
  
        .skeleton-icon {
          width: 80px;
          height: 80px;
          border-radius: 50%;
          background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
          background-size: 200% 100%;
          animation: loading 1.5s infinite;
        }
      }
  
      .skeleton-temperature {
        background: $card-background;
        border-radius: 1rem;
        padding: 1.5rem;
        margin-bottom: 2rem;
  
        .skeleton-temp-main {
          width: 150px;
          height: 60px;
          margin-bottom: 1rem;
          background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
          background-size: 200% 100%;
          animation: loading 1.5s infinite;
          border-radius: 8px;
        }
      }
  
      .skeleton-details {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
        gap: 1rem;
  
        .skeleton-card {
          background: $card-background;
          border-radius: 1rem;
          padding: 1.5rem;
          display: flex;
          align-items: center;
          gap: 1rem;
  
          .skeleton-card-icon {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
            background-size: 200% 100%;
            animation: loading 1.5s infinite;
            flex-shrink: 0;
          }
  
          .skeleton-card-content {
            flex: 1;
  
            .skeleton-label {
              height: 16px;
              margin-bottom: 8px;
            }
  
            .skeleton-value {
              height: 20px;
              width: 80%;
            }
          }
        }
      }
  
      .skeleton-text {
        background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
        background-size: 200% 100%;
        animation: loading 1.5s infinite;
        border-radius: 4px;
  
        &.skeleton-title {
          width: 200px;
          height: 32px;
          margin-bottom: 8px;
        }
  
        &.skeleton-subtitle {
          width: 120px;
          height: 20px;
          margin-bottom: 16px;
        }
  
        &.skeleton-description {
          width: 160px;
          height: 16px;
        }
      }
  
      @keyframes loading {
        0% {
          background-position: -200% 0;
        }
        100% {
          background-position: 200% 0;
        }
      }
    }
  }
  
  // Адаптивность
  @media (max-width: 768px) {
    .weather-display {
      gap: 1.5rem;
  
      .current-weather {
        padding: 1.5rem;
  
        .weather-header {
          flex-direction: column;
          text-align: center;
          gap: 1rem;
  
          .location-info .city-name {
            font-size: 1.75rem;
          }
        }
  
        .temperature-section .main-temperature {
          justify-content: center;
  
          .temperature-value {
            font-size: 3.5rem;
          }
        }
      }
  
      .weather-details .details-grid {
        grid-template-columns: 1fr;
      }
  
      .weekly-forecast .week-forecast-grid .day-forecast {
        grid-template-columns: 1fr;
        text-align: center;
        gap: 0.5rem;
  
        .day-temps {
          justify-content: center;
        }
      }
    }
  }
  
  @media (max-width: 480px) {
    .hourly-forecast .forecast-scroll .forecast-item {
      min-width: 70px;
      padding: 0.75rem 0.5rem;
    }
  }
  </style>