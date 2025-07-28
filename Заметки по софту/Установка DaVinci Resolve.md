
1. Ставим через yay

Free - версия
~~~ 
yay -S davinci-resolve
~~~

Studio - версия

~~~
yay -S davinci-resolve-studio
~~~

2. Можно залогиниться в Blackmagic Cloud и скачать AppImage-образ инсталлера

## Крякаем DaVinci Resolve Studio

1. Качаем AppImage-образ инсталлера через центр поддержки Blackmagic
2. Прописываем сию команду

~~~
sudo /usr/bin/perl -pi -e 's/\x74\x11\xe8\x21\x23\x00\x00/\xeb\x11\xe8\x21\x23\x00\x00/g' /opt/resolve/bin/resolve
~~~

Работает на 20 версии DaVinci Resolve Studio

## Траблшутинг

1. Если в терминале видешь вот эту гадость 
~~~
/usr/lib/libpango-1.0.so.0: undefined symbol: g_once_init_leave_pointer
~~~

Пропиши сию команду 
~~~
sudo cp /usr/lib/libgio-2.0.so /opt/resolve/libs && sudo cp /usr/lib/libgmodule-2.0.so /opt/resolve/libs && sudo cp /usr/lib/libglib-2.0.so /opt/resolve/libs
~~~

Можно через `LD_PRELOAD` заранее загрузить `libglib-2.0.so`, `libgmodule-2.0.so` и запустить программу.

~~~
LD_PRELOAD="/usr/lib/libgio-2.0.so /usr/lib/libgmodule-2.0.so" /opt/resolve/bin/resolve
~~~

