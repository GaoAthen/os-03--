## Фрагмент вывода pstree -p | head - 40
~~~
gao@fedora:~$ pstree -p | head -40
systemd(1)-+-ModemManager(991)-+-{ModemManager}(1017)
           |                   |-{ModemManager}(1019)
           |                   `-{ModemManager}(1022)
           |-NetworkManager(1114)-+-{NetworkManager}(1116)
           |                      |-{NetworkManager}(1117)
           |                      `-{NetworkManager}(1118)
           |-VGAuthService(910)
           |-abrt-dump-journ(1004)
           |-abrt-dump-journ(1008)
           |-abrt-dump-journ(1010)
           |-abrtd(923)-+-{abrtd}(1000)
           |            |-{abrtd}(1001)
           |            `-{abrtd}(1003)

~~~
В выводе показаны текущие процессы которые запущены в операционной системе.


## Цепочка от выданного PPID до PID 1
~~~
gao@fedora:~$ echo $$
3637
gao@fedora:~$ ps -o pid,ppid,user,cmd -p 3637
    PID    PPID USER     CMD
   3637    3619 gao      /usr/bin/bash
gao@fedora:~$ ps -o pid,ppid,user,cmd -p 3619
    PID    PPID USER     CMD
   3619    3611 gao      /usr/libexec/ptyxis-agent --socket-fd=3 --rlimit-nofile=1024
gao@fedora:~$ ps -o pid,ppid,user,cmd -p 3611
    PID    PPID USER     CMD
   3611    1974 gao      /usr/bin/ptyxis --gapplication-service
gao@fedora:~$ ps -o pid,ppid,user,cmd -p 1974
    PID    PPID USER     CMD
   1974       1 gao      /usr/lib/systemd/systemd --user
gao@fedora:~$ ps -o pid,ppid,user,cmd -p 1
    PID    PPID USER     CMD
      1       0 root     /usr/lib/systemd/systemd --switched-root --system --deserialize=55 rhgb
~~~
В выводе нам показан номер процесса, номер процесса который запустился от него и что это за процесс:
PID - Уникальный идентификатор процесса
PPID - Идентификатор родительского процесса, который запустил данный процесс
CMD - Команда которой соответсвует данный процесс

## Какой процесс стоит в корне дерева и почему у него нет родителя

В корне дерева стоит процесс init(в современных дистрибутивах systemd), у него всегда стоит PID1, у данного процесса нету нету родителя потому что при загрузке ОС он запускается самым первым и ему присвается номер 1 ядром.


## Сколько процессов в системе ps aux
~~~
gao@fedora:~$ ps aux | wc -l
264
~~~
Все эти процессы находятся в состоянии S потому что ждут какого то определённого действия со стороны пользователя.

