## Самозванец 
~~~
   5858 root     [kworker/u5:6-ttm]
   5906 gao      /tmp/kworker 3000 <--- вот он
   5919 root     [kworker/u4:0-btrfs-endio-write]
   5926 root     [kworker/0:2-ata_sff]
~~~
## Проверяем /proc/pid/exe
~~~
gao@fedora:~$ sudo ls -l /proc/5906/exe <--- PID самозванца
[sudo] пароль для gao: 
lrwxrwxrwx. 1 gao gao 0 окт  2 18:07 /proc/5906/exe -> '/tmp/kworker (deleted)'
~~~
~~~
gao@fedora:~$ sudo ls -l /proc/*/exe 2>dev/null | grep -E '/tmp|/dev/shm|deleted'
bash: dev/null: Нет такого файла или каталога
~~~
~~~
gao@fedora:~$ sudo ss -tulpn | grep 8080
tcp   LISTEN 0      5            0.0.0.0:8080      0.0.0.0:*    users:(("python3",pid=5920,fd=3))
~~~
~~~
gao@fedora:~$ sudo lsof -i :8080
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
python3 5920  gao    3u  IPv4  60468      0t0  TCP *:webcache (LISTEN)
~~~
~~~
gao@fedora:~$ ps -o pid,ppid,user,cmd -p 5906
    PID    PPID USER     CMD
   5906    3637 gao      /tmp/kworker 3000
~~~
кто запустил самозванца
## Устранение самозванцев
~~~
gao@fedora:~$ kill 5906
[8]   Завершено         /tmp/kworker 3000
gao@fedora:~$ kill 5920
[9]   Завершено         python3 -m http.server 8080 < /dev/null 2>&1
~~~


# Отчёт
## Что обнаружил
Был обнаружен подозрительный системный процесс 'kworker'
Для поиска данного процесса была использована команда ps -eo pid,user,cmd | grep kworker 
~~~
 5858 root     [kworker/u5:6-ttm]
 5906 gao      /tmp/kworker 3000 <--- вот он
 5919 root     [kworker/u4:0-btrfs-endio-write]
 5926 root     [kworker/0:2-ata_sff]
~~~
Так же был обнаружен процесс python3 который использовал порт 8080,найден он был командой sudo ss -tulpn | grep 8080
~~~
tcp   LISTEN 0      5            0.0.0.0:8080      0.0.0.0:*    users:(("python3",pid=5920,fd=3))
~~~
## По каким признакам я понял что это вредоносный процесс
1 признак -Найденный процесс kworker не был в квадратных скобках
2 признак - Запуск из временного каталога
Для проверки файла использовалась команда:
~~~
sudo ls -l /proc/*/exe 2>dev/null | grep -E '/tmp|/dev/shm|deleted'
~~~
Так как процесс был запущен из каталога /tmp,а использование временного каталога является в свою очередь очень подозрительным
Признак 3 - После запуска процесса файл /tmp/kworker был удалён,но продолжал свою работу: 
~~~
lrwxrwxrwx. 1 gao gao 0 окт  2 18:07 /proc/5906/exe -> '/tmp/kworker (deleted)'
~~~
Признак 4 - Открытый сетевой порт
Для проверки сетевых соеденений была использована команда sudo ss -tulpn | grep 8080 а так же sudo lsof -i :8080 
~~~
tcp   LISTEN 0      5            0.0.0.0:8080      0.0.0.0:*    users:(("python3",pid=5920,fd=3))
~~~
~~~
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
python3 5920  gao    3u  IPv4  60468      0t0  TCP *:webcache (LISTEN)
~~~
данный порт прослушивался процессом python3
## Как была устранена угроза
Были найдены PID обоих процессов, устранены они оба были командой kill (PID процесса), для проверки удалились ли они точно нужно заного проверить сетевое соединение  через команду sudo ss -tulpn | grep 8080 и ps -eo pid,user,cmd | grep kworker
# Вывод
Потому что имя процесса можно подделать,а PID всегда указывает на реальный исполняемый файл
