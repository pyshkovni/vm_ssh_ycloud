# Экспресс-описание по созданию и настройке ВМ на YCloud
_на коленке_

## Часть 1. Снимаем доступ по ключам.
### 1.1. Создать новую виртуальную машину в Yandex Cloud со следующими параметрами.
  * ОС - Ubuntu 20.04 
  * vCPU - Intel Icelake 2 Core
  * HDD 10 ГБ
  * 50 % гарантированной доли CPU
  * Прерываемая
  * Логин: testadmin (пример)
  * SSH-ключ: см. пункт 2

### 1.2. Сгенерируйте новые ssh-ключи в директории, которая указана по умолчанию.

    >>> ssh-keygen
    Generating public/private rsa key pair.
    Enter file in which to save the key (/Users/pyshkovni/.ssh/id_rsa): --> жми Return
    Enter passphrase (empty for no passphrase): --> введи n12345 (пример)

### 1.3. Скопируй содержимое открытого ключа в дирретории и вставь в YCloud при создании ВМ (пункт 1).
    
    /Users/<пользователь>/.ssh/id_rsa

### 1.4. Скопируй Публичный IP-адрес (динамичсекий, при перезапуске ВМ ip изменится). <br> 
Подключись через любую оболочку или клиент к ВМ.

    ssh testadmin@<публичный ip>
    
### 1.5. Пеключись на пользователя root.

    sudo -i 
    
### 1.6. Поставь пароль.

    passwd root
    
    passwd testadmin
    
### 1.7. Отредактируй файл конфигурации sshd_config.

    vim /etc/ssh/sshd_config
    
    ========================================================
    PubkeyAuthentication no
    ...
    PasswordAuthentication yes 
    ...
    ChallengeResponseAuthntication yes
    :wq
    ========================================================
    
### 1.8. Перезапусти ssh.

    service ssh restart
    
### 1.9. Создай пользователя hacker и его каталог, добавь его в sudoers.

    mkdir /home/hacker
    
    useradd -s /bin/bash -d /home/hacker/ hacker
    
    usermod -a -G sudo hacker
    
### 1.10. Установи пароль для hacker и заверши сеанс.
    passwd hacker
    
    exit

### 1.11. Подключись к ВМ от лица hacker.

    ssh hacker@<публичный ip>
    
### 1.12. Оставь сообщение в домашнем каталоге и заверши сеанс.
    echo 'Привет. Я пользователь hacker. Пишу с твоей ВМ' > greeting.txt 
    
    exit
    

## Часть 2. Доступ для двух пользователей по ключам.
### 2.1. Добавь нового пользователя student с домашней директорией и правами sudo.
    
    sudo -i
    
    mkdir /home/student && useradd -d /home/student -s /bin/bash student
    
### 2.2. Установить пароль для пользователя student.
    
    passwd student

### 2.3. Добавь строку AllowUsers в конце в файле конфигурации sshd_config, чтобы разрешить доступ двум пользователям.

    vim /etc/ssh/sshd_config
    
    ========================================================
    ...
    AllowUsers testadmin student
    
    :wq
    ========================================================
    
### 2.4. Перезапусти ssh.

    service ssh restart

### 2.5. Переименовать группу student и установить права пользователя student на владение домашним каталогом.
    
    sudo groupmod -n edu student
    
    sudo chown student:edu /home/student
    
### 2.6. Создайте папку в терминале (terminal/bash/powershell), сгенерируйте новую пару ключей и укажите путь их размещения.

    >>> mkdir /Users/<пользователь>/.sshkeys/
    
    >>> ssh-keygen
    Generating public/private rsa key pair.
    Enter file in which to save the key (/Users/pyshkovni/.ssh/id_rsa): 
    --> укажи путь /Users/<пользователь>/.sshkeys/id_rsa --> жми Return
    
    Enter passphrase (empty for no passphrase): --> введи n12345 (пример)

#### 2.6.1. **Для MacOS/Linux**. Передай открытый ключ на ВМ с помощью команды. Потребуется пароль от student.

    ssh-copy-id -i /Users/<пользователь>/.sshkeys/id_rsa.pub student@<публичный ip>
    
#### 2.6.2. **Для Windows**. Создай папку на ВМ и передай открытый ключ с помощью команд. Потребуется пароль от student.

    ssh student@<публичный ip> mkdir /home/student/.ssh/

    scp C:\Users\<имя пользователя>\.sshkeys\id_rsa.pub student@<публичный ip>:/home/student/.ssh/authorized_keys
    
### 2.7. Измените следующие строки в sshd_config, чтобы закрыть доступ по паролям.

    vim /etc/ssh/sshd_config
    
    ========================================================
    #PubkeyAuthentication yes
    ...
    #PasswordAuthentication no 
    ...
    ChallengeResponseAuthntication no
    ...
    AllowUsers testadmin student
    
    :wq
    ========================================================
     
### 2.8. Создадай каталог для группы пользователя student с ограниченными правами на запись. 

    mkdir book 
    
    sudo chmod 755 /home/student/book

### 2.9. Удалите пользователя hacker. 

    sudo apt update
    
    sudo apt install perl
    
    sudo deluser --remove-home hacker

## Теперь у нас есть ВМ, к которой имеют доступ только два пользователя по ключам.

_Thats all folks_
