~~~
sudo pacman -S virt-manager qemu vde2 ebtables dnsmasq bridge-utils openbsd-netcat
~~~

Ставим QEMU и манагер

В libvirtd.conf надо раскомментить две строки:

```
sudo nano \etc\libvirt\libvirtd.conf
```

~~~
unix_sock_group = "libvirt" // Активирует группу для доступа к демону lIbvirt. Юзеры сей группы имеют беспарольный доступ к демону.

unix_sock_rw_perms = “0770” // Дает права для Read/Write сокета
~~~

Добавляем себя в группа libvirt:

~~~
sudo usermod -a -G libvirt %USER% // groups %USER%, дабы убедиться что мы добавились в группу
~~~

Запускаем демона.
~~~
sudo systemctl enable --now libvirtd.service
~~~

Все. Готово. Статья => [тут](https://wiki.archlinux.org/title/Libvirt)

# Траблшутинг

1. Если вдруг у тебя не подключается ВМ к сети и в `systemctl status libvirtd.service` видишь:
```
libvirtd[4773]: network port not found: Network port with UUID does not exist
```

Проверь, а включен ли `iptables`?  

~~~
sudo systemctl status iptables.service

//Если нет
sudo systemctl enable --now iptables.service
~~~
