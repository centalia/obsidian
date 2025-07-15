5. `color` — цвет текста

```css
h1 {
  color: red;
}

p {
  color: #FF0000;
}
```

🎨 Поддерживаются:

- Имена цветов (`red`, `blue`, `green`)
- Шестнадцатеричные (`#FF0000`)
- Функции: `rgb()`, `rgba()`, `hsl()`, `hsla()` и т.п.

---

## 8. Тень текста — `text-shadow`

```css
text-shadow: X Y Blur Color;
```

Пример:

```css
h1 {
  text-shadow: 2px 2px 4px #000000;
}
```

Пояснение параметров:

| Параметр      | Значение                           |
|---------------|------------------------------------|
| X             | Горизонтальное смещение            |
| Y             | Вертикальное смещение              |
| Blur          | Радиус размытия (необязателен)     |
| Color         | Цвет тени                          |

---

## 📌 Пример комбинирования

```css
h1 {
  font-family: "Georgia", serif;
  font-size: 2rem;
  font-weight: bold;
  font-style: italic;
  color: #333;
  text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.3);
}
```

---

## 📚 Полезные ссылки

- [MDN: font-family](https://developer.mozilla.org/en-US/docs/Web/CSS/font-family)
- [Google Fonts](https://fonts.google.com/)
- [text-shadow](https://developer.mozilla.org/en-US/docs/Web/CSS/text-shadow)


---

## 9. `line-height` — межстрочный интервал

Определяет расстояние между базовыми линиями строк текста.

```css
p {
  line-height: 1.6;
}
```

- Без единиц (`1.6`) — множитель от `font-size`
- Можно задать в `px`, `em`, `%` и др.

---

## 10. `letter-spacing` — расстояние между буквами

```css
h1 {
  letter-spacing: 2px;
}
```

- Может быть положительным (раздвигает) или отрицательным (сжимает)
- Хорошо влияет на читаемость заголовков

---

## 11. `text-align` — выравнивание текста

```css
p {
  text-align: justify;
}
```

Значения:
- `left`, `right`, `center`, `justify`
- `start`, `end` — зависят от направления (`ltr`, `rtl`)

---

## 12. `direction` — направление текста

```css
p {
  direction: rtl;
}
```

- `ltr` — слева направо (по умолчанию)
- `rtl` — справа налево (например, арабский или иврит)

---

## 13. `word-break` и `overflow-wrap`

```css
.wordy {
  word-break: break-word;
  overflow-wrap: break-word;
}
```

- `word-break: break-all` — ломает слово по символам
- `overflow-wrap: break-word` — мягкий перенос, если строка не влезает

---

## 14. `text-overflow` — обрезка текста

```css
.truncate {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
```

Используется для показа `...` в конце строки, если текст не помещается.

---

## ✅ Комбинированный пример

```css
.article-title {
  font-family: 'Open Sans', sans-serif;
  font-size: 1.25rem;
  font-weight: 600;
  line-height: 1.4;
  letter-spacing: 0.5px;
  color: #222;
  text-align: left;
  text-shadow: 0.5px 0.5px 1px rgba(0, 0, 0, 0.2);
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
```

---

## 📚 Дополнительные ссылки

- [MDN: line-height](https://developer.mozilla.org/en-US/docs/Web/CSS/line-height)
- [MDN: letter-spacing](https://developer.mozilla.org/en-US/docs/Web/CSS/letter-spacing)
- [MDN: text-overflow](https://developer.mozilla.org/en-US/docs/Web/CSS/text-overflow)
- [CSS Tricks: Truncating text](https://css-tricks.com/snippets/css/truncate-string-with-ellipsis/)