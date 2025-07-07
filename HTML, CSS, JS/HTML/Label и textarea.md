 `label`используется для создания _метки_, которая связывает текстовое описание с элементом управления.

``` HTML

<label for="username">Имя пользователя:</label> 
```

`textarea`используется для создания текстового поля, в котором пользователь может _вводить_ несколько строк текста.

``` HTML

<textarea id="message" name="message" rows="5" cols="30"></textarea>
```

`<fieldset>...</fieldset>` предназначен для группировки элементов, связанных друг с другом, разделяя таким образом форму на логические фрагменты.

~~~ HTML

<fieldset>
    <legend>Контактная информация</legend>
    <p><label for="name">Имя <em>*</em></label><input type="text" id="name"></p>
    <p><label for="email">E-mail</label><input type="email" id="email"></p>
  </fieldset>
~~~

 название группе можно присвоить с помощью `<legend>`, который идет сразу за открывающим тегом элемента `<fieldset>`