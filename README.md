# Student Attendance Telegram Bot

Этот Telegram-бот предназначен для автоматизации процесса отслеживания посещаемости студентов и уведомления их о начале занятий согласно расписанию.

## Возможности

*   **Регистрация пользователей:** Студенты регистрируются, указывая свой номер в группе и выбирая подгруппу.
*   **Автоматические уведомления:** Бот отправляет напоминания о начале занятий студентам их подгруппы незадолго до начала пары (согласно времени в `Schedule`).
*   **Отметка о присутствии:** Студенты могут отметить свое присутствие, нажав кнопку "Я на паре", прикрепленную к уведомлению.
*   **Ограничение по времени:** Отметиться можно только до определенного часа по местному времени (`TIME_LIMIT_HOUR_LOCAL`).
*   **Просмотр расписания:** Команда `/schedule` показывает расписание на текущий день для подгруппы пользователя.
*   **Информация о пользователе:** Команда `/info` отображает ФИО, номер в группе и подгруппу зарегистрированного студента.
*   **Отметка на паре другой подгруппы:** Команда `/attend_other_group` позволяет посмотреть расписание другой подгруппы и отметиться на их занятии (если время позволяет).
*   **Ежедневный отчет старосте:** После окончания времени для отметки бот отправляет старосте список студентов, отметившихся в течение дня.
*   **Меню команд:** Удобное меню с основными командами (`ReplyKeyboardMarkup`).
*   **Работа с часовым поясом:** Бот учитывает локальный часовой пояс, указанный в конфигурации, для расписания и лимита времени отметки.
*   **Модульная структура:** Код разбит на логические модули для лучшей читаемости и поддержки.

## Технологии

*   **Python 3.9+** (Рекомендуется из-за встроенного `zoneinfo`)
*   **pyTelegramBotAPI:** Библиотека для взаимодействия с Telegram Bot API.
*   **psycopg2:** Адаптер для работы с базой данных PostgreSQL.
*   **PostgreSQL:** Система управления реляционными базами данных.
*   **zoneinfo / backports.zoneinfo:** Для работы с часовыми поясами.

## Установка

1.  **Клонируйте репозиторий:**
    ```bash
    git clone <URL вашего репозитория>
    cd <папка репозитория>
    ```

2.  **Создайте и активируйте виртуальное окружение (рекомендуется):**
    ```bash
    python -m venv venv
    # Windows
    .\venv\Scripts\activate
    # macOS/Linux
    source venv/bin/activate
    ```

3.  **Установите зависимости:**
    Убедитесь, что у вас установлен пакет `tzdata` в вашей операционной системе (особенно актуально для Linux/Docker).
    ```bash
    # Debian/Ubuntu: sudo apt update && sudo apt install tzdata
    # CentOS/Fedora: sudo dnf install tzdata
    ```
    Затем установите Python-зависимости:
    ```bash
    pip install -r requirements.txt
    ```

## Конфигурация

Перед запуском бота необходимо настроить параметры в файле `config.py`:

*   `TELEGRAM_BOT_TOKEN`: Токен вашего Telegram-бота. Получите его у [@BotFather](https://t.me/BotFather).
*   `DB_CONFIG`: Словарь с параметрами подключения к вашей базе данных PostgreSQL:
    *   `dbname`: Имя базы данных.
    *   `user`: Имя пользователя БД.
    *   `password`: Пароль пользователя БД.
    *   `host`: Адрес хоста БД (например, `localhost`).
    *   `port`: Порт БД (обычно `5432`).
*   `TELEGRAM_ADMIN_ID`: Числовой Telegram ID администратора, которому будут приходить уведомления об ошибках или перезапусках бота.
*   `TIME_LIMIT_HOUR_LOCAL`: Час по *местному* времени (согласно `LOCAL_TIMEZONE_NAME`), до которого студенты могут отмечаться на парах (например, `17` для лимита до 17:00).
*   `LOCAL_TIMEZONE_NAME`: Имя вашего часового пояса из базы данных IANA (TZ database name), например, `'Europe/Moscow'`, `'Asia/Yekaterinburg'`. Это определяет, как интерпретируется время в расписании и `TIME_LIMIT_HOUR_LOCAL`.

## Настройка базы данных

1.  Убедитесь, что у вас установлен и запущен сервер PostgreSQL.
2.  Создайте базу данных, если ее еще нет (например, с именем `postgres` или любым другим, указанным в `DB_CONFIG`).
3.  Выполните SQL-скрипт (сохраните предоставленный SQL-код в файл, например, `schema.sql`) для создания необходимых таблиц и загрузки начальных данных (список студентов, расписание, староста):
    ```bash
    psql -U <user> -d <dbname> -a -f schema.sql
    ```
    Замените `<user>` и `<dbname>` на ваши имя пользователя и имя базы данных.
4.  Убедитесь, что данные для подключения в `config.py` (`DB_CONFIG`) соответствуют настройкам вашей БД.

## Запуск бота

1.  Активируйте виртуальное окружение (если вы его создавали):
    ```bash
    # Windows
    .\venv\Scripts\activate
    # macOS/Linux
    source venv/bin/activate
    ```
2.  Запустите главный скрипт:
    ```bash
    python main.py
    ```
    Бот запустится и начнет опрашивать Telegram API. Логи работы (включая проверки расписания) будут выводиться в консоль.

## Использование

1.  **Начало работы:** Найдите вашего бота в Telegram и отправьте команду `/start`.
2.  **Регистрация:**
    *   Бот покажет список студентов. Введите свой порядковый номер.
    *   Бот предложит выбрать подгруппу с помощью кнопок. Нажмите на свою подгруппу.
    *   После успешного выбора подгруппы регистрация завершена, появится меню команд.
3.  **Команды:**
    *   `/start`: Начать работу, зарегистрироваться или увидеть приветствие.
    *   `/schedule`: Показать расписание на сегодня для вашей подгруппы (время местное).
    *   `/info`: Показать вашу информацию (ФИО, номер, подгруппа).
    *   `/attend_other_group`: Показать расписание другой подгруппы на сегодня и предложить кнопки для отметки на их парах (если время позволяет).
    *   `/help`: Показать справку по командам и времени отметки.
4.  **Уведомления:** Бот будет автоматически присылать сообщения перед началом пар вашей подгруппы.
5.  **Отметка:** Если уведомление пришло до истечения времени отметки (`TIME_LIMIT_HOUR_LOCAL`), под ним будет кнопка "Я на паре". Нажмите ее, чтобы подтвердить присутствие.

## Лицензия

Лицензия не указана."