### 4.1 GNOME в стиле Aqua

>Данную заметку можно использовать для настройки Cinammon

- Для начала ставим sassc, gnome-tweaks, gnome-shell-extension-manager
~~~
sudo pacman -S sassc gnome-tweaks && yay -S extension-manager
~~~

- Клонируем **[WhiteSur-icon-theme](https://github.com/vinceliuice/WhiteSur-icon-theme)**, **[WhiteSur-gtk-theme](https://github.com/vinceliuice/WhiteSur-gtk-theme)**, **[WhiteSur-cursors](https://github.com/vinceliuice/WhiteSur-cursors)**
~~~
git clone https://github.com/vinceliuice/WhiteSur-icon-theme.git && git clone https://github.com/vinceliuice/WhiteSur-gtk-theme.git --depth=1 && cd WhiteSur-gtk-theme && git clone https://github.com/vinceliuice/WhiteSur-cursors.git
~~~

- Ставим твики
	- [blur-my-shell](https://extensions.gnome.org/extension/3193/blur-my-shell)
	- [user-themes](https://extensions.gnome.org/extension/19/user-themes/)
	- [search-light](https://extensions.gnome.org/extension/5489/search-light/)
	- [gnome-4x-ui-improvements](https://extensions.gnome.org/extension/4158/gnome-40-ui-improvements/)
	- [quick-settings-audio-panel](https://extensions.gnome.org/extension/5940/quick-settings-audio-panel/)
	- [compiz-alike-magic-lamp-effect/](https://extensions.gnome.org/extension/3740/compiz-alike-magic-lamp-effect/)
	- [compiz-windows-effect](https://extensions.gnome.org/extension/3210/compiz-windows-effect/)
	- [coverflow-alt-tab](https://extensions.gnome.org/extension/97/coverflow-alt-tab/)

- Устанавливаем тему
~~~
./install.sh -o normal -c dark -a normal -t orange -s nord -m -N mojave -l -HD --shell -i arch --round --darker
~~~

- Твикаем тему 
~~~
sudo ./tweaks.sh -o normal -c dark -t orange -s nord 
sudo ./tweaks.sh --gdm -i arch 
sudo ./tweaks.sh --firefox adaptive
~~~

- Ставим пак иконок
~~~
./install.sh -t orange -a -b
~~~

- Ставим кастомный курсор
~~~
./install.sh
~~~

### 4.2 GNOME в стиле Aqua Liqiud Glass

>Данную заметку можно использовать для настройки Cinammon

 Для начала ставим sassc, gnome-tweaks, gnome-shell-extension-manager
~~~
sudo pacman -S sassc gnome-tweaks && yay -S extension-manager
~~~

- Клонируем **[Tahoe GTK Theme](`https://github.com/kayozxo/GNOME-macOS-Tahoe`)**
~~~
git clone https://github.com/vinceliuice/MacTahoe-gtk-theme.git --depth=1 && cd MacTahoe-gtk-theme
~~~

- Ставим твики
	- [blur-my-shell](https://extensions.gnome.org/extension/3193/blur-my-shell)
	- [user-themes](https://extensions.gnome.org/extension/19/user-themes/)
	- [search-light](https://extensions.gnome.org/extension/5489/search-light/)
	- [gnome-4x-ui-improvements](https://extensions.gnome.org/extension/4158/gnome-40-ui-improvements/)
	- [quick-settings-audio-panel](https://extensions.gnome.org/extension/5940/quick-settings-audio-panel/)
	- [compiz-alike-magic-lamp-effect/](https://extensions.gnome.org/extension/3740/compiz-alike-magic-lamp-effect/)
	- [compiz-windows-effect](https://extensions.gnome.org/extension/3210/compiz-windows-effect/)
	- [coverflow-alt-tab](https://extensions.gnome.org/extension/97/coverflow-alt-tab/)

- Устанавливаем тему
~~~
./install.sh -o normal -c dark -a normal -t orange -s nord -l -HD --shell -i arch --round --darker
~~~

> Для Cinnamon твики после `--shell` не нужны

- Твикаем тему 
~~~
sudo ./tweaks.sh -o normal -c dark -t orange -s nord &&
sudo ./tweaks.sh --gdm -i arch &&
sudo ./tweaks.sh --firefox flat
~~~

> Если не стоит GDM то твики `--gdm` не нужны

- Ставим пак иконок
~~~
./install.sh -t orange -a -b
~~~

- Ставим кастомный курсор
~~~
./install.sh
~~~
