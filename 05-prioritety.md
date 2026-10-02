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
## Top (оба)
<img width="1053" height="560" alt="image" src="https://github.com/user-attachments/assets/4597b3ae-4adf-4236-a909-761a712818ce" />

## Распределение процессора на nice 0 и nice 19
~~~
gao@fedora:~$ ps -o pid,ni,pcpu,psr,cmd -C yes
    PID  NI %CPU PSR CMD
   5829   0 94.1   0 yes
   5835  19  1.2   0 yes
~~~

## Команда renice и sudo
Она запрашивает судо из за базовых правил безопасности и защиты от монополизации системных ресурсов
## Команда taskset -c 0
Нужна для того что бы запускать процессы в 1 из ядер, если у вас их больше 1
