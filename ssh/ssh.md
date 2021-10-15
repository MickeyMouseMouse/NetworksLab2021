# SSH и утилиты  
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