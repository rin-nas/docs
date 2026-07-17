# Linux FAQ

## Как между серверами измерить скорость копирования данных или доступность портов?

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
iperf3 -c HOST [-p PORT]    # клиент отправляет данные, а сервер их принимает
iperf3 -c HOST [-p PORT] -R # сервер отправляет данные, а клиент их принимает 
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

## Как скопировать файл между Windows и Linux без WinSCP?

Выполнить на Windows
```powershell
# Windows -> Linux
# способ 1
pscp -pwfile pwfile C:\temp\filename.txt user@host:
# способ 2
plink -pwfile pwfile user@host < C:\temp\filename.txt "cat > ~/
filename.txt"

# Windows <- Linux
pscp user@host:/tmp/filename.txt C:\temp\filename.txt
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

## Как посмотреть содержимое файла `/etc/fstab` в отформатированном виде?

```bash
sed 's/#.*//' /etc/fstab \
 | column --table --table-columns SOURCE,TARGET,TYPE,OPTIONS,FREQ,PASS --table-right FREQ,PASS
```

# Как выполнить команду на нескольких серверах без Ansible
```bash
for HOST in myhostname-{{11..14},{21..24}}; do echo $HOST && ssh $HOST 'hostname -I' || break; done
```

## Генератор паролей

https://www.calculator.net/password-generator.html
