## Создал зомби
~~~
gao@fedora:/etc$ bash -c 'sleep 1 & exec sleep 120' &
[1] 3920 - его PPID
~~~
## Нашёл зомби
~~~
gao@fedora:/etc$ ps -eo pid,ppid,stat,cmd | awk '$3 ~ /Z/'
   3924    3920 Z    [sleep] <defunct>
~~~
## Убил зомби
~~~
kill -9 3920
[1]+  Убито
~~~
У меня зомби умер сразу.
## Остальные команды не сработали потому что зомби уже был мёртвым
~~~
gao@fedora:/etc$ ps -eo pid,ppid,stat,cmd | awk '$3 ~ /Z/'
gao@fedora:/etc$ kill -9 3920
bash: kill: (3920) - Нет такого процесса
gao@fedora:/etc$ sleep 2
gao@fedora:/etc$ ps -eo pid,ppid,stat,cmd | awk '$3 ~ /Z/'
~~~
## Чем опасно если в системе будет тысячи зомби
Это может полностью парализовать операционную систему,даже если оперативка и процессор будут полностью работоспособны
