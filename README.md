# Экспресс-описание по созданию ВМ на YCloud
_на коленке_

## 1. Создать новую виртуальную машину в Yandex Cloud со следующими параметрами
  * ОС - Ubuntu 20.04 
  * vCPU - Intel Icelake 2 Core
  * HDD 10 ГБ
  * 50 % гарантированной доли CPU
  * Прерываемая
  * Логин: testadmin (пример)
  * SSH-ключ: см. пункт 2

## 2. Сгенерируйте новые ssh-ключи в директории, которая указана по умолчанию

    >>> ssh-keygen
    Generating public/private rsa key pair.
    Enter file in which to save the key (/Users/pyshkovni/.ssh/id_rsa): --> жми Return
    Enter passphrase (empty for no passphrase): --> введи n12345 (пример)

## 3. Скопируй содержимое открытого ключа в дирретории и вставь в YCloud при создании ВМ (пункт 1):
    
    /Users/<пользователь>/.ssh/id_rsa

## 4. Скопируй Публичный IP-адрес (динамичсекий, при перезапуске ВМ ip изменится). <br> 
Подключись через любую оболочку или клиент к ВМ

    ssh testadmin@<публичный ip>
    
## 5. Пеключись на пользователя root

    sudo -i 
    
## 6. Поставь пароль

    passwd root
    
    passwd testadmin
    
## 6. Отредактируй файл конфигурации sshd_config

    vim /etc/ssh/sshd_config
    
    ========================================================
    PubkeyAuthentication no
    ...
    PasswordAuthentication yes 
    :wq
    ========================================================
    
## 7. Перезапусти ssh

    service ssh restart
    
## 8. Создай пользователя hacker и его каталог, добавь его в sudoers

    mkdir /home/hacker
    
    useradd -s /bin/bash -d /home/hacker/ hacker
    
    usermod -a -G sudo hacker
    
## 9. Установи пароль для hacker и заверши сеанс
    passwd hacker
    
    exit

## 10. Подключись к ВМ от лица hacker

    ssh hacker@<публичный ip>
    
## 11. Оставь сообщение в домашнем каталоге и заверши сеанс
    echo 'Привет. Я пользователь hacker. Пишу с твоей ВМ' > greeting.txt 
    
    exit
    
Thats all folks
