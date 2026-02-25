# Linux FAQ

## Как измерить скорость копирования данных между серверами?

Сервер-приёмник
```bash
nc -l -p 9000 | pv | dd of=/dev/null
```

Сервер-источник
```bash
dd if=/dev/zero | pv | nc {target-hostname} 9000
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
```
# Windows -> Linux
# способ 1
pscp -pwfile pwfile C:\temp\filename.txt user@host:
# способ 2
plink -pwfile pwfile user@host < C:\temp\filename.txt "cat > ~/
filename.txt"

# Windows <- Linux
pscp user@host:/tmp/filename.txt C:\temp\filename.txt
```

## Генератор паролей

https://www.calculator.net/password-generator.html
