### **Установите Bootstrap Icons через npm**

bash

npm install bootstrap-icons

---

### 2️⃣ **Подключите шрифт в Sass (SCSS)**

Добавьте в ваш основной файл стилей (например, `main.scss`):

scss

// Подключение Bootstrap Icons
$bootstrap-icons-font: "bootstrap-icons";
$bootstrap-icons-font-src: url("~bootstrap-icons/font/fonts/bootstrap-icons.woff2") format("woff2"),
                          url("~bootstrap-icons/font/fonts/bootstrap-icons.woff") format("woff");

@font-face {
  font-family: $bootstrap-icons-font;
  src: $bootstrap-icons-font-src;
  font-display: block;
}

// Класс для иконок (аналогично .bi в Bootstrap)
.bi::before {
  font-family: $bootstrap-icons-font !important;
  font-style: normal;
  font-weight: normal !important;
  font-variant: normal;
  text-transform: none;
  line-height: 1;
  vertical-align: -.125em;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

---

### 3️⃣ **Используйте иконки в HTML**

Теперь можно использовать иконки через CSS-классы, как в официальной документации:

html

<i class="bi bi-arrow-right"></i>
<i class="bi bi-heart-fill"></i>

---

### 4️⃣ **Оптимизация (по желанию)**

Если хотите уменьшить размер шрифта, можно подключить только нужные иконки через **Unicode-коды**:

scss

// Пример для конкретных иконок
.icon-arrow::before {
  content: "\F138"; // Код иконки стрелки (см. https://icons.getbootstrap.com/)
}

.icon-heart::before {
  content: "\F415"; // Код иконки сердца
}

html

<span class="bi icon-arrow"></span>
<span class="bi icon-heart"></span>

---

### 🔹 **Проверьте настройки Vite**

Убедитесь, что Vite правильно обрабатывает шрифты. В `vite.config.js` должно быть:

js

export default {
  assetsInclude: ['**/*.woff2', '**/*.woff'],
}

---

### 🔹 **Альтернативный способ (если не работает)**

Если шрифты не подгружаются, можно просто импортировать CSS из `bootstrap-icons`:

scss

@import "~bootstrap-icons/font/bootstrap-icons";

---

### Итог

Теперь **Bootstrap Icons** будут работать в вашем проекте через **Sass**, и вы сможете использовать их как обычные классы (`bi bi-*`). 🚀

Как правильно прописать в assetsInclude шрифты bootstrap icons в vite.config.js?

Чтобы правильно подключить шрифты **Bootstrap Icons** в `vite.config.js`, нужно добавить поддержку файлов шрифтов (`.woff2`, `.woff`) в настройках Vite. Вот как это сделать:

---

### 🔧 **1. Обновленный `vite.config.js`**

javascript

import { defineConfig } from 'vite';
import path from 'path';

export default defineConfig({
  // Поддержка шрифтов Bootstrap Icons
  assetsInclude: [
    '**/*.woff2',
    '**/*.woff',
    '**/*.eot',
    '**/*.ttf',
    '**/*.svg'
  ],
  
  // Дополнительные настройки (опционально)
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'), // Пример алиаса для путей
    },
  },
  
  css: {
    preprocessorOptions: {
      scss: {
        additionalData: `@import "@/styles/variables.scss";` // Если используете SCSS
      }
    }
  }
});

---

### 📦 **2. Подключение шрифтов в Sass/SCSS**

Добавьте в ваш SCSS-файл (например, `main.scss`):

scss

// Способ 1: Импорт CSS из node_modules (рекомендуется)
@import "bootstrap-icons/font/bootstrap-icons.css";

// ИЛИ Способ 2: Ручное подключение шрифтов через @font-face
$bootstrap-icons-font: "bootstrap-icons";
$bootstrap-icons-font-dir: "~bootstrap-icons/font/fonts";

@font-face {
  font-family: $bootstrap-icons-font;
  src: url("#{$bootstrap-icons-font-dir}/bootstrap-icons.woff2") format("woff2"),
       url("#{$bootstrap-icons-font-dir}/bootstrap-icons.woff") format("woff");
  font-display: block;
}

.bi::before {
  font-family: $bootstrap-icons-font !important;
  font-style: normal;
  font-weight: normal !important;
}

---

### 🛠 **3. Проверка структуры проекта**

Убедитесь, что в `node_modules` есть папка `bootstrap-icons`:

node_modules/
  └── bootstrap-icons/
      └── font/
          ├── bootstrap-icons.css
          └── fonts/
              ├── bootstrap-icons.woff2
              └── bootstrap-icons.woff

---

### ⚠️ **Если шрифты не загружаются**

1. **Проверьте пути**:  
    В консоли разработчика (F12 → вкладка Network) должны загружаться `.woff2`/`.woff` файлы без ошибок 404.
    
2. **Добавьте явный импорт**:  
    В главном JS/TS файле добавьте:
    
    javascript
    

- import 'bootstrap-icons/font/bootstrap-icons.css';
    
- **Пересоберите проект**:  
    Удалите папку `node_modules` и `package-lock.json`, затем выполните:
    
    bash
    

1. npm install
    npm run dev
    

---

### 💡 **Оптимизация (опционально)**

Если нужно уменьшить размер, используйте только нужные иконки:

scss

// Вместо полного подключения:
.icon-heart::before {
  content: "\F415"; // Unicode-код иконки
  font-family: "bootstrap-icons";
}

html

<span class="icon-heart"></span>

---

Теперь шрифты Bootstrap Icons должны корректно загружаться через Vite + Sass. Если остались проблемы — проверьте консоль браузера на ошибки загрузки ресурсов.

Спасибо проблему решил

Отлично! Рад, что у тебя получилось решить проблему с подключением Bootstrap Icons через Sass в Vite. 🎉

Если вдруг понадобится ещё помощь с:

- **Оптимизацией загрузки иконок** (tree-shaking, использование только нужных иконок)
    
- **Кастомизацией Bootstrap** через Sass-переменные
    
- **Настройкой Vite** для других типов assets (например, SVG-спрайты)
    
- **Работой с шрифтами** в современных сборках
    

— обращайся! Могу подсказать лучшие практики.

Удачного кодинга! 💻🔥

P.S. Если захочешь сделать «Послать нахуй» с анимацией летящего среднего пальца — есть крутые варианты с GSAP или CSS-анимациями. 😄