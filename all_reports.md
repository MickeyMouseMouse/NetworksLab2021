# Lab1: Система обмена позитивным контентом  
**Описание протокола взаимодействия сервера и клиента**  
1) клиент отправляет свой никнейм на сервер: {"nickname":"Ivan"}  
2) сервер проверяет, не занят ли полученный никнейм другим пользователем или самим сервером ('SERVER')  
и отвечает клиенту согласием или ошибкой: {"status":"success"} / {"status":"error: this nickname is already taken"}  
3) после логина клиент отсылает сообщение с полем "text" или полями "text" и "attachment":  
{"text":"Hello"} / {"text":"Hello", "attachment":"1.jpg"}  
4) если у текстового сообщения есть вложение ("attachment":"sample.file"),  
то далее отправляется требуемый файл в виде массива байт (b'...')  
5) сервер пересылает каждое полученное текстовое сообщение всем клиентам, кроме отправителя:  
{"time":"2021-09-27 12:34:56.789000+00:00", "nickname": "Ivan", "text":"Hello"} /  
{"time":"2021-09-27 12:34:56.789000+00:00", "nickname": "Ivan", "text":"Hello", "attachment":"1.jpg"}  
6) если у текстового сообщения есть вложение, то сервер пересылает и его в виде массива байт  

**Сериализация данных**  
Модуль "Serialization.py" (самодельный аналог JSON) содержит следующие функции:
1) "dump" для конвертации словаря в массив байт  
2) "load" для конвертации массива байт в словарь  

**Серверный скрипт**  
1) обрабатывает каждого клиента в отдельном потоке  
2) присваивается полученным сообщением время в формате UTC  
3) помимо рассылки полученных сообщений, также уведомляет клиентов о подключении/отключении пользователей  
4) не хранит историю переписки  

**Клиентский скрипт**  
1) запускается с одним аргументом, соответствующим желаемому никнейму  
2) способен одновременно отправлять и принимать сообщения  
3) предлагает к каждому текстовому сообщению также добавить вложение в виде файла (полученные от собеседников файлы сохраняются в текущей директории)  
4) при потере связи с сервером сразу же сообщает об этом  
5) для отключения от сервера и завершения нужно ввести "\q"  

**Тестирование клиент-серверного приложения на удаленном сервере**  
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/lab1/lab1/images/check.PNG "")  

# Lab2: Реализация Trivial File Transfer Protocol (TFTP)  
Полное описание протокола приведено в RFC 1350: https://datatracker.ietf.org/doc/html/rfc1350  

**Формат TFTP пакетов**  
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/lab2/lab2/images/tftp_message_format.PNG "")  

**Тестирование клиент-серверного приложения на удаленном сервере**  
Попытка скачать файл с сервера (ошибка, потому что такого файла на сервере нет) и загрузка файла на сервер:
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/lab2/lab2/images/remote1.PNG "")  
Скачивание файла с сервера:
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/lab2/lab2/images/remote2.PNG "")  

**Тестирование сервера при работе со встроенным в Windows TFTP клиентом:**  
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/lab2/lab2/images/default_tftp_client.PNG "")  

**Тестирование клиента при работе со сторонним TFTP сервером:**  
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/lab2/lab2/images/third_party_tftp_server.PNG "")  

# Lab3: Non-blocking & Async  

В данной лабораторной работе был модифицирован сервер из первой лабораторной в соответствии с новыми требованиями: использовать неблокирующие сокеты и асинхронное программирование.  
Для достижения асинхронности использовалась стандартная Python библиотека asyncio.  

# Lab4_1: Online Calculator  

Server: https://github.com/MickeyMouseMouse/NetworksLab2021/blob/lab4_1/calc/server.py  
Client: https://github.com/TsaplinIA/NetworksLab2021/blob/Lab4_1/client.py  

В данной лабораторной работе создано веб-приложение "Калькулятор" на языке Python с использованием веб-фреймворка Flask.  
Реализовано:  
* авторизация пользователей по логину и паролю  
* выполнение быстрых операций (+, -, \*, \/)  
* выполнение медленных операций (sqrt, !) с отложенным ответом сервера  

Для коммуникации клиента и сервера используется SocketIO. Клиент с помощью метода emit посылает запросы на сервер и получает ответы через callback вызовы. Также сервер посылает сообщения клиенту с помощью emit.  

Описание работы Python библиотеки socketio_client: https://pypi.org/project/socketIO-client/  

Описание протокола взаимодействия:  
1. Клиент с помощью метода emit() вызывает на сервере событие "user_login" и передает ему параметры "login" и "password" с соответсвующими данными, затем ожидает вызов callback функции с параметрами в виде словаря и кода состояния HTTP. Если авторизация прошла успешно, то в словаре поле "status" содержит строку "success", а поле "login" - логин авторизованного пользователя. Если авторизация не прошла, то возвращается словарь только с полем "status", содержащим строку "failed".  
2. Авторизованный клиент с помощью метода emit() вызывает на сервере событие "fast_calc" и передает ему параметры "operation" ("+" или "-" или "\*" или "/"), "operand1" (тип str) и "operand2" (тип str), затем ожидает вызов callback функции с параметрами в виде словаря и кода состояния HTTP. Словарь содержит ответ сервера на поставленную задачу.  
3. Авторизованный клиент с помощью метода emit() вызывает на сервере событие "slow_calc" и передает ему параметры "operation" ("!" или "sqrt") и "value", затем ожидает вызов callback функции с параметрами в виде словаря и кода состояния HTTP. Словарь содержит ответ сервера на поставленную задачу.  
4. Сервер с помощью метода emit() может вызывать у авторизованного клиента событие "server_message", передавая ему словарь с соответсвующим информационным сообщением и код состояния HTTP.  
5. Для правильного завершения работы клиент должен с помощью метода emit() вызывает на сервере событие "user_logout", а также закрыть socketio соединение.  

# Lab4_2: Roulette Online  

## Errors  
ERROR 435 - The croupier already exists  
ERROR 441 - try login twice  
ERROR 442 - Some field is not filled  
ERROR 443 - Incorrect login or password  
ERROR 444 - Incorrect type or amount  
ERROR 445 - insufficient number of coins  

## Routes  
- POST /login
  - WWW-FORM{"login": "String", "password": "String", "croupier": "String"}
  - Ответы:
    - Code: 441:
      - You already login
    - Code: 443 | 442:
      - Incorrect login or password
    - Code: 435:
      - The croupier already exists
    - Code: 302 | 200:
      - Login successful
- GET /login
  - Ответы:
    - Code:200
      - text/html
- GET /userInfo
  - Ответы:
    - Code:200
      - text/html
    - Code:401
      - Unauthorized
- GET /userInfo/json
  - Ответы:
    - Code:200
      - JSON{"coins": "Int", "is_croupier": "boolean", "login": "String"}
    - Code:401
      - Unauthorized
- GET /logout
  - Ответы:
    - Code:200
      - html/text
    - Code:401
      - Unauthorized
- GET /bet
  - Ответы:
    - Code: 200
      - html/text
    - Code: 401
      - Unauthorized
    - Code: 403
      - You are croupier. You can't do bets
- POST /bet
  - Ответы:
    - Code: 200
      - bet accepted
    - Code: 401
      - Unauthorized
    - Code: 403
      - You are croupier. You can't do bets
    - Code: 444
      - Incorrect type or amount
    - Code: 445
      - Insufficient number of coins
- GET /bet/all
  - Ответы:
    - Code: 200
      - html/text
- GET /bet/all/json
  - Ответы:
    - Code: 200
      - JSON:{"users": list<String>,"types": list<Int>,"amounts": list<Int>}
    - Code: 204
      - Bets not found
- GET /start
  - Ответы:
    - Code: 200
      - html/text
    - Code: 401
      - Unauthorized
    - Code: 403
      - You must be croupier
- GET /result
  - Ответы:
    - Code: 200
      - html/text
- GET /result/json
  - Ответы:
    - Code: 204
      - No results yet
    - Code: 200
      - JSON:{"number": Int, "users": list<String>,"types": list<Int>,"amounts": list<Int>, "result": list<String>}

## About Sessions  
Session in Java: https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpSession.html  
if you use only requests, then you need to specify the current cookies in the request.  

# Lab SSH  
**Secure Shell (SSH)** - зашифрованный сетевой протокол прикладного уровня, позволяющий производить удалённое управление операционной системой и туннелирование TCP-соединений.  
Подключиться к ssh-серверу: ssh userName@domainNameOrIpAddress [-p portNumber]  

Утилита **scp** работает по протоколу ssh; применяется для передачи файлов между клиентом и сервером.  
Выгрузить (upload) файл на сервер: scp pathToFileOnClient userName@domainNameOrIpAddress:pathToDirOnServer  
Скачать (download) файл с сервера: scp userName@domainNameOrIpAddress:pathToFileOnServer pathToDirOnClient  

**SSH port forwarding:** c помощью SSH туннелей можно пробросить порт с удалённого сервера на локальную машину.  
Сделаем удалённый сервер доступным локально на порте 8080 через ssh:  
ssh -L 8080:localhost:46090 userName@domainNameOrIpAddress  
Теперь можно из веб-браузера обращаться по адресу localhost:8080 к удаленному серверу на его порт 46090 (это будет работать, пока активна текущая ssh сессия).  

Чтобы каждый раз при подключении по SSH не вводить пароль, существует более удобный и безопасный **способ авторизации по ключам.** Для этого используются пары ключей (публичный-приватный) асимметричного алгоритма шифрования RSA.  
Сгенерировать новую пару ключей: ssh-keygen  
После генерации ключей необходимо скопировать содержимое файла с публичным ключом на сервер в файл ~/.ssh/authorized_keys.  

**Client url (curl)** - сетевая утилита, реализующая базовые возможности работы с URL страницами и передачу файлов; поддерживает большое количество протоколов (например, HTTP, HTTPS, FTP, POP3, IMAP, SMTP и прочие).  
Скачать содержимое файла по ссылке и сохранить в 1.txt: curl -o 1.txt https://sample/file.txt

Утилита **wget** похожа на curl, но поддерживает только протоколы HTTP, HTTPS и FTP. Преимущество wget перед curl заключается в том, что есть поддержка загрузки файлов по редиректам.  

**Задание 1**  
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task1_1.PNG "")
Загружаем файл с сервера:  
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task1_2.PNG "")

![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task1_3.PNG "")

**Задание 2**  
Скачиваем файл из Интернета на сервер:  
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task2_1.PNG "")

![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task2_2.PNG "")
Пробрасываем порт:  
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task2_3.PNG "")
Подключаемся к удаленному серверу через локальный порт:  
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task2_4.PNG "")

![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task2_5.PNG "")

![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task2_6.PNG "")
Загружаем файл на сервер:  
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task2_7.PNG "")

**Задание 3**  
Генерируем пару ключей:  
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task3_1.PNG "")
Копируем публичный ключ на сервер:  
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task3_2.PNG "")
Подключаемся к серверу по ключу без ввода пароля:  
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task3_3.PNG "")

**Задание 4**  
Анализ трафика в Wireshark  
Фильтр: http  
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task4_1.PNG "")

![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task4_2.PNG "")
Фильтр: tls  
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task4_3.PNG "")

![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task4_4.PNG "")

![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task4_5.PNG "")
Фильтр: dns  
![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task4_6.PNG "")

![](https://raw.githubusercontent.com/MickeyMouseMouse/NetworksLab2021/ssh/ssh/images/task4_7.PNG "")