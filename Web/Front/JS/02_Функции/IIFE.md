Выглядит это следующим образом:
```javascript
(function() {
  var Hui = "Хуек";
  alert(Hui); 
})();
```

Здесь создаётся и немедленно вызывается Function Expression. Так что код выполняется сразу же и у него есть свои локальные переменные.

Function Expression обёрнуто в скобки `(function {...})`, потому что, когда JavaScript встречает `"function"` в основном потоке кода, он воспринимает это как начало Function Declaration. Но у Function Declaration должно быть имя, иначе получим пизды:

``` JS
function() { //SyntaxError: Function statements require a function name
  var Hui = "Хуек";
  alert(Hui); 
}();
```

Да даже если мы просто назовем функцию, мы все равно опиздюлимся, поскольку сам JS не позволяет вызывать Function Declaration немедленно.

``` JS
function Huek() { //SyntaxError: Function statements require a function name
  var Hui = "Хуек";
  alert(Hui); 
}(); //SyntaxError: Unexpected token ')'
```

Так что скобки вокруг функции – это трюк, который позволяет объяснить JavaScript, что функция была создана в контексте другого выражения, а значит, что это Function Expression: ей не нужно имя и её можно вызвать немедленно.

Помимо круглых скобок существуют и другие способы сообщить JavaScript, что мы имеем в виду Function Expression:

```javascript
// Способы создания IIFE

(function() {
  var Hui = "Хуек";
  alert(Hui); //"Круглые скобки вокруг функции"
})();

(function() {
  var Hui = "Хуек";
  alert(Hui); //"Круглые скобки вокруг всего выражения"
}());

!function() {
  var Hui = "Хуек";
  alert(Hui); //"Выражение начинается с логического оператора НЕ"
}();

+function() {
  var Hui = "Хуек";
  alert(Hui); //"Выражение начинается с унарного плюса"
}();
```

Во всех перечисленных случаях мы объявляем Function Expression и немедленно запускаем его. Ещё раз отметим: в настоящее время необходимости писать подобный код нет.