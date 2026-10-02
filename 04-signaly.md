# Часть А
## Запустил в фоновом режиме и прописал kill
~~~
gao@fedora:~$ ~/upryamy.sh &
[4] 4576
Мой PID: 4576
gao@fedora:~$ kill -TERM 4576
gao@fedora:~$ Получил SIGTERM, но продолжаю работать
~~~
## Проверил через ps -p
~~~
gao@fedora:~$ ps -p 4576  -o pid,stat,cmd
    PID STAT CMD
   4576 S    /bin/bash /home/gao/upryamy.sh
~~~

## Теперь принудительно
~~~
gao@fedora:~$ kill -KILL 4576
[4]   Убито                 ~/upryamy.sh
gao@fedora:~$ ps -p 4576 -o pid,stat,cmd
    PID STAT CMD
~~~

# Часть Б
## Остановленные процессы
~~~
gao@fedora:~$ jobs 
[1]   Остановлен       ~/upryamy.sh
[2]   Остановлен       ~/upryamy.sh
[3]-  Остановлен       ~/upryamy.sh
[4]+  Остановлен       sleep 600
~~~
~~~
gao@fedora:~$ ps -o pid,stat,cmd -C sleep
    PID STAT CMD
   4363 T    sleep 1
   4456 T    sleep 1
   4506 T    sleep 1
   4834 T    sleep 600
~~~
~~~
gao@fedora:~$ bg %1
[1] ~/upryamy.sh &
gao@fedora:~$ jobs
[1]   Запущен             ~/upryamy.sh &
[2]   Остановлен       ~/upryamy.sh
[3]-  Остановлен       ~/upryamy.sh
[4]+  Остановлен       sleep 600
gao@fedora:~$ fg %1
~/upryamy.sh
~~~
Запустил фоновый процесс (упрямый),после вернул его на передний план что забрало у меня терминал(что бы вернуть CTRL+Z(C)
## SIGTERM и SIGKILL
Term - Вежливо просит закрыть
SIGKILL - Без спроса просто удаляет процесс

## Правильный порядок
SIGTERM - если нужно просто завершить процесс если он не нужен на данный момент, SIGKILL если процесс завис.
Если сделать kill -9 то последствия могут быть необратимыми, вплоть до полной потери данных.
## Что получил sleep
Sleep получил состояние T(Stopped), означает что процесс остановлен

## CTRL+C CTRL+Z
CTRL+C - прерывает процесс
CTRL+Z - приостановку процесса со стороны терминала. Он переводит программу в то самое состояние T
