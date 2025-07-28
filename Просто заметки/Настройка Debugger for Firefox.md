1. Ставим плагин.
`code --install-extension firefox-devtools.vscode-firefox-debug`

2. Запускаем Firefox в режиме отладки.
Открываем about:config => включаем `devtools.debugger.remote-enabled` = `true`

3. Создаем конфиг отладки в VSCode
    - Открываем Run and Debug
    - Тычем "Create a launch.json file"
    - Пишем в файлик
        ```
            {
              "version": "0.2.0",
              "configurations": [
                {
                  "type": "firefox",
                  "request": "launch",
                  "name": "Открыть в Firefox",
                  "url": "http://localhost:5500/index.html", // или путь к файлу
                  "webRoot": "${workspaceFolder}"
                }
              ]
            }
        ```
4. ПРОФИТ!!! 