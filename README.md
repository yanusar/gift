REST API сервис для хранения и анализа данных о потенциальных покупателях магазина подарков. Описание запросов находится в файле [TASK.pdf](https://github.com/smurphik/gift/blob/master/TASK.pdf).

### Install

В первую очередь, необходимо установить PostgreSQL сервер:

    $ sudo apt-get update
    $ sudo apt-get install postgresql postgresql-contrib

Устанавливаем сервер подарков (если на машине не установлен `git`, можно установить его или скачать содержимое репозитория, используя интерфейс github.com):

    $ git clone https://github.com/smurphik/gift
    $ cd gift
    $ sudo python3 setup.py install clean

Если выполнение последней команды завершилось с ошибкой `[Errno 104] Connection reset by peer -- Some packages may not be found!`, повторяйте эту команду до успешного завершения (сервер PyPi не очень стабилен).

### Setting & Testing

Теперь нужно создать базу данных с именем `gift_db` и завести пользователя `gift_server`, чтобы сервер мог через него обращаться к базе. Это можно сделать вызовом скрипта:

    $ ./config_db.sh

Теперь сервер можно запустить командой:

    $ gift_server

Запущенный сервер, можно протестировать:

    $ ./test.py

N.B. Тестирование оставляет после себя мусорные данные в базе. Для того чтобы почистить базу, нужно остановить сервер и выполнить:

    $ sudo -i -u postgres psql -d gift_db
    gift_db=# TRUNCATE imports, relations, unique_ids, posted_ids;
    gift_db=# \q

### Autorun

Для того, чтобы сервер запускался автоматически при запуске/перезапуске системы, нужно выполнить скрипт:

    $ sudo ./config_autorun.sh

Теперь, при необходимости, можно посмотреть состояние сервера, выполнить его остановку, запуск, перезагрузку при помощи следующих команд:

    $ sudo systemctl status gift.service
    $ sudo systemctl stop gift.service
    $ sudo systemctl start gift.service
    $ sudo systemctl restart gift.service

### Notes

* Если человек является родственником самому себе, то в свой день рождения он дарит себе один подарок, т.к. в этом родственном отношении нет двух разных сторон (хотя, условие задачи можно трактовать и так, что должно быть 2 подарка);

* Если в списке relatives есть дублирование идентификаторов, то данные не сохраняются и возвращается ошибка 400;

* Т.к. в реализации обошлось без глобальных изменяемых переменных, сервер, вероятно, можно размножить на несколько тредов без существенных изменений в коде, но заняться этим не хватило времени.
