# Обновление ядра в ОС Linux

### Название задания
Обновление ядра системы

### Текст задания
1. Запустить ВМ c Ubuntu.
2. Обновить ядро ОС на новейшую стабильную версию из mainline-репозитория.
3. Оформить отчёт в GitHub-репозитории.

### Основные команды
В инструкции рассмотрен процесс обновления ядра Linux в ОС.

Все дальнейшие действия были проверены в VirtualBox 7.1.4, на которой развёрнут Ubuntu Server 24.04.3 и клиентской машине с Ubuntu 24.04.1 Desktop.

Чтобы обновить ядро ОС на новую стабильную версию из mainline‑репозитория:

1. Создайте слепок состояния виртуальной машины с Ubuntu Server.
2. В терминале клиентской машины подключитесь к виртуальной машине по ssh. 
3. Проверьте текущую версию ядра Ubuntu Server с помощью команды:

```
uname -r
```
Результат вывода:

```bash
servjul@ubuntuserver2:~$ uname -r
6.8.0-87-generic
```

4. В браузере откройте страницу https://kernel.ubuntu.com/mainline.
5. Выберите последнюю стабильную версию ядра, которая соответствует вашей архитектуре. На момент составления документа нам подходит версия 6.17: https://kernel.ubuntu.com/mainline/v6.17/.

Архитектура системы для процессоров типа x86_64 требует установки пакетов amd64. Чтобы проверить тип процессора в терминале, выполните команду:

```
uname -p
```
6. Скачайте актуальные пакеты на виртуальную машину:

```
mkdir kernel && cd kernel
wget https://kernel.ubuntu.com/mainline/v6.17/amd64/linux-headers-6.17.0-061700-generic_6.17.0-061700.202509282239_amd64.deb
wget https://kernel.ubuntu.com/mainline/v6.17/amd64/linux-headers-6.17.0-061700_6.17.0-061700.202509282239_all.deb
wget https://kernel.ubuntu.com/mainline/v6.17/amd64/linux-image-unsigned-6.17.0-061700-generic_6.17.0-061700.202509282239_amd64.deb
wget https://kernel.ubuntu.com/mainline/v6.17/amd64/linux-modules-6.17.0-061700-generic_6.17.0-061700.202509282239_amd64.deb
```

Результат вывода:

```bash
servjul@ubuntuserver2:~/kernel$ wget https://kernel.ubuntu.com/mainline/v6.17/amd64/linux-headers-6.17.0-061700-generic_6.17.0-061700.202509282239_amd64.deb
wget https://kernel.ubuntu.com/mainline/v6.17/amd64/linux-headers-6.17.0-061700_6.17.0-061700.202509282239_all.deb
wget https://kernel.ubuntu.com/mainline/v6.17/amd64/linux-image-unsigned-6.17.0-061700-generic_6.17.0-061700.202509282239_amd64.deb
wget https://kernel.ubuntu.com/mainline/v6.17/amd64/linux-modules-6.17.0-061700-generic_6.17.0-061700.202509282239_amd64.deb
--2025-11-04 15:39:48--  https://kernel.ubuntu.com/mainline/v6.17/amd64/linux-headers-6.17.0-061700-generic_6.17.0-061700.202509282239_amd64.deb
Resolving kernel.ubuntu.com 
...
...
...
```

7. Установите скачанные пакеты с помощью команды:

```
sudo dpkg -i *.deb
```

Результат вывода:

```bash
servjul@ubuntuserver2:~/kernel$ sudo dpkg -i *.deb
[sudo] password for servjul: 
Выбор ранее не выбранного пакета linux-headers-6.17.0-061700.
(Чтение базы данных … на данный момент установлено 87987 файлов и каталогов.)
Подготовка к распаковке linux-headers-6.17.0-061700_6.17.0-061700.202509282239_all.deb …
Распаковывается linux-headers-6.17.0-061700 (6.17.0-061700.202509282239) …
...
...
...
/etc/kernel/postinst.d/zz-update-grub:
Sourcing file `/etc/default/grub'
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-6.17.0-061700-generic
Found initrd image: /boot/initrd.img-6.17.0-061700-generic
Found linux image: /boot/vmlinuz-6.8.0-87-generic
Found initrd image: /boot/initrd.img-6.8.0-87-generic
Warning: os-prober will not be executed to detect other bootable partitions.
Systems on them will not be added to the GRUB boot configuration.
Check GRUB_DISABLE_OS_PROBER documentation entry.
Adding boot menu entry for UEFI Firmware Settings ...
done

```

8. Проверьте, что ядро появилось в /boot:

```
ls -al /boot
```

Результат вывода:

```bash
servjul@ubuntuserver2:~/kernel$ ls -al /boot
...
...
lrwxrwxrwx  1 root root       29 ноя  4 15:42 vmlinuz -> vmlinuz-6.17.0-061700-generic
-rw-------  1 root root 16146944 сен 28 22:39 vmlinuz-6.17.0-061700-generic
-rw-------  1 root root 15006088 окт 10 18:41 vmlinuz-6.8.0-87-generic
lrwxrwxrwx  1 root root       24 ноя  4 13:42 vmlinuz.old -> vmlinuz-6.8.0-87-generic
```

9. Обновите конфигурацию загрузчика, чтобы назначить новое ядро по умолчанию:

```
sudo update-grub
```

Результат вывода:

```bash
servjul@ubuntuserver2:~$ sudo update-grub
Sourcing file `/etc/default/grub'
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-6.17.0-061700-generic
Found initrd image: /boot/initrd.img-6.17.0-061700-generic
Found linux image: /boot/vmlinuz-6.8.0-87-generic
Found initrd image: /boot/initrd.img-6.8.0-87-generic
Warning: os-prober will not be executed to detect other bootable partitions.
Systems on them will not be added to the GRUB boot configuration.
Check GRUB_DISABLE_OS_PROBER documentation entry.
Adding boot menu entry for UEFI Firmware Settings ...
done


```

10. Выберите загрузку нового ядра по умолчанию:

```
sudo grub-set-default 0
```

Результат вывода:

```bash
servjul@ubuntuserver2:~$ sudo grub-set-default 0
```

11. Перезагрузите виртуальную машину с помощью команды:

```
sudo reboot
```
12. После перезагрузки виртуальной машины, проверьте версию ядра:

```
uname -r
```
Результат вывода:

```bash
servjul@ubuntuserver2:~$ uname -r
6.17.0-061700-generic
```

Обновление ядра закончено.
