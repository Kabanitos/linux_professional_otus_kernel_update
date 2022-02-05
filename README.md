# **Конфигурирование образа**
Переходим в файл `centos.json` где содержится описание образа.
Создаем переменные в секции  `variables`, где описываем название и версию проекта
```
    "artifact_description": "CentOS 7.7 with kernel 5.x",
    "artifact_version": "7.7.1908",
```
Я оставил без измененния.
Далее в секции `builders`задаем исходный образ, для создания своего в виде ссылки и контрольной суммы. Параметры подключения к создаваемой виртуальной машине.

```
      "iso_url": "http://mirror.yandex.ru/centos/7.7.1908/isos/x86_64/CentOS-7-x86_64-Minimal-1908.iso",
      "iso_checksum": "sha256:9a2c47d97b9975452f7d582264e9fc16d108ed8252ac6816239a3b58cef5c53d",
      
 ```
 Синтаксис немного отличается  тем, с которым было указано в инструкции т.к. в последней версии Packer нет параметра `"iso_checksum_type"`
# **Создание образа**
Для создание образа переходим в директорию где находится наш файл `centos.json` и выполняем команду:
```
    packer build centos.json
    
```
После выполнения команды появился образ `centos-7.7.1908-kernel-5-x86_64-Minimal.box`.
#***Тестирование образа***
Для выполнения тестирования необходимо выполнить импорт образа в `vagrant`
```
    vagrant box add --name centos-7-5 centos-7.7.1908-kernel-5-x86_64-Minimal.box
```
Проверка образа в списке имеющихся с помощью команды `vagrant box list`
Запуск виртуальной машины и подключение к ней 
```
    vagrant up
```
    vagrant ssh
```
Проверяем версию ядра:
```
  uname -r 
```
# **Vagrant Cloud**
Импортируем образ в `Vagrant Cloud`. Логинимся в `vagrant cloud`, указывая e-mail, пароль и описание выданого токена (можно оставить по-умолчанию)
```
    vagrant cloud auth login
```

Далее побликую свой образ:

```
    vagrant cloud publish --release <username>/centos-7-5 1.0 virtualbox centos-7.7.1908-kernel-5-x86_64-Minimal.box
    
```

# **Редактирование файла Vagrant**
Открываем редактором файл `Vagrantfile`
Редактируем пункт `box_name`:
```
    :box_name => "Kabanitos/centos-7-5",
```
В секции `Vagrant.configure("2") do |config|` добавлем 
```
  box.vm.box_version = "1.0"
```
