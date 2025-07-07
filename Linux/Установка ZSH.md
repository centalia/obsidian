1. Ставим сам пакет zsh
```
sudo pacman -S zsh
```

2. Ставим Oh my ZSH
```
sh -c "$(wget https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

3. Ставим пропатченные шрифты [MesloLGS](https://github.com/romkatv/powerlevel10k?tab=readme-ov-file#meslo-nerd-font-patched-for-powerlevel10k)

4. Клонируем репу PowerLevel10K от romkatv
~~~
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git "${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k"
~~~

5. В `~/.zshrc` ищем строчку `ZSH_THEME` и меняем значение `"powerlevel10k/powerlevel10k"`


Можно дабы не ебаться с репами и конфигами можно напрямую с AUR скачать пакет
~~~
yay -S --noconfirm zsh-theme-powerlevel10k-git
echo 'source /usr/share/zsh-theme-powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
~~~

