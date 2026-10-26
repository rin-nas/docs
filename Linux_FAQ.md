# Linux FAQ

## Как между серверами измерить скорость копирования данных и/или доступность портов?

**Вариант с `nc, pv, dd`**

```bash
# Приёмник (сервер)
nc -l -s HOST -p PORT | pv | dd of=/dev/null

# Источник (клиент)
dd if=/dev/zero | pv | nc HOST PORT
```

**Вариант с `iperf3`**

```bash
# Приёмник (сервер)
iperf3 -s [-B HOST] [-p PORT]

# Источник (клиент)
iperf3 -c HOST [-p PORT] -P 4    # клиент отправляет данные (в 4 потока), а сервер их принимает 
iperf3 -c HOST [-p PORT] -P 4 -R # сервер отправляет данные (в 4 потока), а клиент их принимает (наоборот)

iperf3 -c HOST [-p PORT] -b 1G # с ограничением скорости передачи данных (G = Гбит/с)
```

## Как изолировать текущий сервер от указанного сервера?

Глядя на [IPTABLES Single Rule Generator](https://www.latinsud.com/pub/iptables/), можно быстрее понять, как работает `iptables`.

Выполните команды на текущем сервере (который нужно изолировать):
```bash
# запретить входящий трафик от указанного сервера IP_addr
sudo iptables -A INPUT  -s IP_addr -j DROP

# запретить исходящий трафик к указанному серверу IP_addr
sudo iptables -A OUTPUT -d IP_addr -j DROP
```

## Как изолировать текущий сервер от остальных серверов по портам?

Выполните команды на текущем сервере (который нужно изолировать):
```bash
# запретить входящий трафик с любых серверов для указанного диапазона портов (пример)
sudo iptables -A INPUT  -i ens18 -p tcp -m multiport --dports 5432:5435 -j DROP

# запретить исходящий трафик на любые серверы для указанного диапазона портов (пример)
sudo iptables -A OUTPUT -o ens18 -p tcp -m multiport --dports 5432:5435 -j DROP
```

## Как отменить сетевую изоляцию текущего сервера?

Выполните команды на текущем сервере (на котором нужно отменить изоляцию):
```bash
# посмотреть правила с номерами:
sudo iptables -L -v -n --line-numbers

# сбросить все правила во всех цепочках:
sudo iptables -F
```

## Как настроить между серверами SSH вход без пароля по ключу?

```bash
# на всех серверах кластера:
cd ~ && ssh-keygen -t rsa -b 4096 && chmod 700 ~/.ssh && cat ~/.ssh/id_rsa.pub

# на другом сервере добавить в конец файла открытый ключ из другого сервера:
nano ~/.ssh/authorized_keys && chmod 640 ~/.ssh/authorized_keys

# проверить удалённое подключение:
ssh {remote-hostname}
```

## Как узнать потребление RAM для каждого ПО?
Если есть права локального админа - воспользоваться утилитой [`ps_mem`](https://github.com/pixelb/ps_mem) (`sudo dnf install ps_mem`), иначе:

```bash
ps -e -orss=,args= \
| awk '{print $1 " " $2 }' \
| awk '{tot[$2]+=$1;count[$2]++} END {for (i in tot) {print tot[i],i,count[i]}}' \
| sort -n \
| tail -n 15 \
| sort -nr \
| awk '{ hr=$1/1024; printf("%13.2fM", hr); print "\t" $2 }'
```

## Как скопировать папку с файлами между Linux и Linux?

```bash
# копирование файлов с локального сервера на удалённый
scp -r /path/to/*.txt user@host:/path/to

# копирование файлов с удалённого сервера на локальный
scp -r user@host:/path/to/*.txt /path/to
```

## Как скопировать файл между Windows и Linux без WinSCP?

Все команды выполнять на Windows в командной строке (запустите `cmd`).
Утилиты `pscp` и `plink` входят в поставку [Putty](https://putty.org.ru/).

**Windows → Linux**
```powershell
# способ 1
pscp -pwfile pwfile C:\temp\filename.txt user@host:

# способ 2
plink -pwfile pwfile user@host < C:\temp\filename.txt "cat > ~/filename.txt"
```

**Windows ← Linux**
```
pscp user@host:/tmp/filename.txt C:\temp\filename.txt
```

## Как заархивировать папку в файл формата `xz` или `zstd` с указанным уровнем сжатия?
```bash
sudo apt install xz-utils zstd
tar -I 'zstd -9' -cf my_folder.tar.zst my_folder
tar -c -f - my_folder | xz -9 > my_folder.tar.xz
tar -c -f - my_folder | pv -p -s $(du -sb my_folder | cut -f1) |  xz -9 > my_folder.tar.xz  # с отображением прогресса выполнения
```

## Как распаковать TAR файл в формате `xz` или `zstd`?

```bash
sudo apt install xz-utils zstd pv
tar -xf my_folder.tar.xz
tar -I zstd -xf my_folder.tar.zst
```

## Как в текущей папке перепаковать TAR файлы из `gz` в `xz` с максимальной компрессией?

Файл `recompress.sh`:

```bash
for FILE in *{.tar.gz,.tgz}; do
    echo "Перепаковка: $FILE"
    FILE_TMP=${FILE%.tar.gz}   # удаляем ".tar.gz"
    FILE_TMP=${FILE_TMP%.tgz}  # удаляем ".tgz"
    gzip -d -v -k -f $FILE && xz -T0 -9 -e -v $FILE_TMP.tar && rm $FILE || exit
done
```

## Как уменьшить (оптимизировать) размер PNG файла?

Сценарий — сделали снимок экрана терминала, файл сохранился как PNG и занимает слишком много места.
True Color (полноцвет) нужно конвертировать в 256 цветов и сделать рекомпрессию.

```
sudo apt install pngquant optipng advancecomp
```

Файл `png_compress.sh`:
```bash
# convert true color to 256 colors
pngquant --verbose --skip-if-larger --force --strip --speed 1 256 $1 --output $1

# select optimal PNG algorithm
optipng -strip all -o7 $1

# recompress to best with zopfli
advpng -z -4 $1
```

Использование: 
```
bash png_compress.sh big_file.png
```

## Как посмотреть содержимое файла `/etc/fstab` в отформатированном виде?

```bash
sed 's/#.*//' /etc/fstab \
 | column --table --table-columns SOURCE,TARGET,TYPE,OPTIONS,FREQ,PASS --table-right FREQ,PASS
```

## Как выполнить команду на нескольких серверах без Ansible и Salt

Файл `run.sh`:

```bash
for HOST in myhostname-{{11..14},{21..24}}; do
    echo $HOST && ssh $HOST 'hostname -I' || exit
done
```

## Как выполнить команду на нескольких серверах и Salt

```bash
salt 'ont-fpb-[1,2]*' cmd.run 'ss -tunlp | grep 15432'
```

## Как перезагрузить сервер

```bash
# жёсткая перезагрузка сервера на уровне ядра ОС
echo b > /proc/sysrq-trigger
```

## Генератор паролей

https://www.calculator.net/password-generator.html
