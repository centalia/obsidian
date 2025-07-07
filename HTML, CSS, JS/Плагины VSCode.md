
`code --install-extension <название плагина>` — для установки плагина
`code --uninstall-extension <название плагина>` — для удаления плагина

ESLint - плагин для проверки синтаксиса и стиля кода 
`code --install-extension dbaeumer.vscode-eslint`

Prettier - плагин для форматирования кода в соответствии с выбранным стилем
`code --install-extension esbenp.prettier-vscode`

JavaScript Debugger - плагин для отладки JavaScript в браузере Google Chrome
`code --install-extension ms-vscode.js-debug-nightly`

Live Server - плагин для автоматической перезагрузки страницы при изменении кода
`code --install-extension ritwickdey.LiveServer`

GitLens - плагин для удобного использования системы контроля версий Git
`code --install-extension eamodio.gitlens`

IntelliSense for CSS/SCSS/Less - плагин для подсказок и автодополнения CSS, SCSS и Less кода
`code --install-extension Zignd.html-css-class-completion
`
Path Intellisense - плагин для автодополнения путей к файлам и папкам в проекте.
`code --install-extension christian-kohler.path-intellisense`

streetsidesoftware.code-spell-checker

streetsidesoftware.code-spell-checker-cspell-bundled-dictionaries

streetsidesoftware.code-spell-checker-russian

## Настройка Debugger for Firefox

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