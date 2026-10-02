## Вывод PS (до)
~~~
gao@fedora:~$ ps -o pid,stat,cmd -C sleep
    PID STAT CMD
   4363 T    sleep 1
   4456 T    sleep 1
~~~
## Вывод PS (после renice)
~~~
gao@fedora:~$ ps -o pid,stat,cmd -C sleep
    PID STAT CMD
   4363 T    sleep 1
   4456 T    sleep 1
~~~
