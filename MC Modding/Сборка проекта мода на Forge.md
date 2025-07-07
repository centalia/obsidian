
Актуальная версия MC на момент написания заметки: **1.21.1**
Актуальная версия Forge на момент написания заметки: **52.0.9**  

1. Скачиваем zip с [https://files.minecraftforge.net/net/minecraftforge/forge/](https://files.minecraftforge.net/net/minecraftforge/forge/)
2. Распаковываем
3. Открываем его в Idea
4. Ждем, когда соберется проект
5. В настройках проекта ставить Java 21
6. В settings.gradle в  дописываем:
    ~~~
    maven { url = 
	    'https://maven.parchmentmc.org' }
    ~~~
7. В build.gradle в plugins дописываем: 
    ~~~
    id 
    'org.parchmentmc.librarian.forgegradle' version '1.+'
    ~~~
8. В файле gradle.properties заменяем в:
- mapping_channel с official на parchment
- mapping_version с MC версии на версию Parchment под версию мода (см. [https://parchmentmc.org/docs/getting-started](https://parchmentmc.org/docs/getting-started))  
10. Обновляем проект
11. Чистим и подготавливаем [главный класс мода](upnote://x-callback-url/openNote?noteId=6925f210-5d0f-43cd-a57d-86909f79d76a "upnote://x-callback-url/openNote?noteId=6925f210-5d0f-43cd-a57d-86909f79d76a") 
12. В Gradle в Tasks - forgegradle runs генерим конфиги для запуска MC (genIntellijRuns)
13. Тестовый запуск
14. ПРОФИТ!!!