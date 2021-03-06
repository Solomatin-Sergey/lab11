# Туториал 

Что такое Ngrok?

Ngrok - это такая программа, которая предоставляет доступ к локальным серверам за NATS и брандмауэрами в общедоступный Интернет по защищенным туннелям.

Как это работает?

1. вы загружаете и запускаете программу на своем компьютере и предоставляете ей порт сетевой службы, обычно веб-сервера.
2. подключаетесь к облачному сервису ngrok, который принимает трафик (объём информации, передаваемой через компьютерную сеть за определённый период времени) по общедоступному адресу.
3. наконец, трафик передается через процесс ngrok, запущенный на вашем компьютере, а затем на указанный вами локальный адрес.

```
$ cd ~
$ mkdir install
$ mkdir tmp
$ export HOME_PREFIX=`pwd`/install
$ echo $HOME_PREFIX
$ export USERNAME=`sergsolo`
$ cd tmp # заходим во временную директорию, куда будем скачивать необходимые пакеты и зависимости
```
Есть 2 способа установки утилит tmux и ngrok (о tmux рассказано ниже)
## 1-ый способ: поставить всё вручную (со всеми необходимыми библиотеками и зависимостями) 

```
// Установка библиотеки libevent-2.1.8-stable, которая предоставляет программистам доступ к кроссплатформенному асинхронному сетевому API 
// API (англ. application programming interface) — описание способов (набор классов, процедур, функций, структур или констант), которыми одна компьютерная программа может взаимодействовать с другой программой. 
$ wget https://github.com/libevent/libevent/releases/download/release-2.1.8-stable/libevent-2.1.8-stable.tar.gz
$ tar -xvzf libevent-2.1.8-stable.tar.gz
$ cd libevent-2.1.8-stable
$ ./configure --prefix=${HOME_PREFIX} # в HOME_PREFIX (по этому пути) произойдёт инсталляция
$ make && make install
$ cd ..
// Аналогично делаем для библиотеки ncurses 
// ncurses - это библиотека, предназначенная для управления вводом-выводом на терминал. Позволяет задавать экранные координаты и цвет выводимых символов 
$ wget http://invisible-island.net/datafiles/release/ncurses.tar.gz
$ tar -xvzf ncurses.tar.gz
$ cd ncurses-6.2
$ ./configure --prefix=${HOME_PREFIX}
$ make && make install
$ cd ..
// примерно то же самое, но с некоторыми оговорками выполняется для tmux
// tmux - это терминальный мультиплексор или иными словами, 
// вы можете в одном терминале запустить несколько терминальных сессий и переключаться между ними или даже разместить их в одном окне, 
// а также отключиться от сессии оставив её работать в фоне, а затем вернуться и посмотреть результат, это может быть очень удобно при работе по SSH. 
$ wget https://github.com/tmux/tmux/releases/download/2.5/tmux-2.5.tar.gz
$ tar -xvzf tmux-2.5.tar.gz
$ cd tmux-2.5
$ ./configure --prefix=${HOME_PREFIX} CFLAGS="-I${HOME_PREFIX}/include -I${HOME_PREFIX}/include/ncurses" LDFLAGS="-L${HOME_PREFIX}/lib" # флаги для того, где искать библиотеки
$ make && make install # компилируется решение и в директорию install переносятся необходимые артефакты
$ cd ..
Скачивание ngrok, разархивирование и перемещение zip-архива в домашнюю директорию.
$ wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip
$ unizp ngrok-stable-linux-amd64.zip
$ mv ngrok ${HOME_PREFIX}/bin
$ export LD_LIBRARY_PATH=${HOME_PREFIX}/lib # устанавливаем путь к динамическим библиотекам
$ export PATH="${HOME_PREFIX}/bin:${PATH}" # добавляем директорию, в которой будет находиться ngrok
$ tmux # Для создания новой сессии (сессией называется коллекция групп процессов (заданий)) достаточно запустить утилиту без параметров
// Примечание: c tmux вы можете легко переключаться между несколькими программами в одном терминале, отсоединять их и повторно присоединять к другому терминалу.
```
Удаляем ранее созданные файлы
```$ cd ~
$ rm -rf tmp install
```
## 2-ой способ при помощи утилиты brew/linuxbrew (простой, но может не работать)
```
$ brew install tmux # or use linuxbrew 🎉 
$ brew cask install ngrok 
```
```
$ tmux new -s session_with_group # создаётся новая сессия с именем session_with_group 
# Alisa:
$ open https://ngrok.com/signup # пользователь Alisa заходит на сервис ngrok.com
$ export NGROK_TOKEN=<токен> # она получает токен
$ ngrok authtoken ${NGROK_TOKEN} # она запускает у себя на компьютере сервер и предоставляет данному серверу токен
$ ngrok tcp 22 # и запускает SSH на 22 порту
<порт_ngrok_сервера> # на выходе получается порт ngrok-сервера
# Bob:
$ ssh ${USERNAME}@0.tcp.ngrok.io -p<порт_ngrok_сервера> # Bob по открытому IP-адресу и имени пользователя, который создал сессию, подключается
<пароль_от_учетной_записи>
$ tmux a -t session_with_group # процесс подключения к терминальной сессии
$ <C-B>"
```
Дополнение: среди загруженных файлов есть скриншоты интерфейсов tmux и ngrok (с ними можно ознакомиться выше)
