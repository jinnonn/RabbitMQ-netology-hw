# Домашнее задание к занятию  «Очереди RabbitMQ»

---

### Задание 1. Установка RabbitMQ

Используя Vagrant или VirtualBox, создайте виртуальную машину и установите RabbitMQ.
Добавьте management plug-in и зайдите в веб-интерфейс.

*Итогом выполнения домашнего задания будет приложенный скриншот веб-интерфейса RabbitMQ.*

### Решение 1.

![image](https://github.com/jinnonn/RabbitMQ-netology-hw/assets/146999555/9dc89f6c-af9e-4bbf-959a-38425bd391bf)

---

### Задание 2. Отправка и получение сообщений

Используя приложенные скрипты, проведите тестовую отправку и получение сообщения.
Для отправки сообщений необходимо запустить скрипт producer.py.

Для работы скриптов вам необходимо установить Python версии 3 и библиотеку Pika.
Также в скриптах нужно указать IP-адрес машины, на которой запущен RabbitMQ, заменив localhost на нужный IP.

```shell script
$ pip install pika
```

Зайдите в веб-интерфейс, найдите очередь под названием hello и сделайте скриншот.
После чего запустите второй скрипт consumer.py и сделайте скриншот результата выполнения скрипта

*В качестве решения домашнего задания приложите оба скриншота, сделанных на этапе выполнения.*

Для закрепления материала можете попробовать модифицировать скрипты, чтобы поменять название очереди и отправляемое сообщение.

### Решение 2.

producer.py

![image](https://github.com/jinnonn/RabbitMQ-netology-hw/assets/146999555/00828ba3-56f8-442a-adb0-b1cf00406568)

consumer.py

![image](https://github.com/jinnonn/RabbitMQ-netology-hw/assets/146999555/360d28c9-182e-43cb-b89f-ffb640ff9f64)

---

### Задание 3. Подготовка HA кластера

Используя Vagrant или VirtualBox, создайте вторую виртуальную машину и установите RabbitMQ.
Добавьте в файл hosts название и IP-адрес каждой машины, чтобы машины могли видеть друг друга по имени.

Пример содержимого hosts файла:
```shell script
$ cat /etc/hosts
192.168.0.10 rmq01
192.168.0.11 rmq02
```
После этого ваши машины могут пинговаться по имени.

Затем объедините две машины в кластер и создайте политику ha-all на все очереди.

*В качестве решения домашнего задания приложите скриншоты из веб-интерфейса с информацией о доступных нодах в кластере и включённой политикой.*

![image](https://github.com/jinnonn/RabbitMQ-netology-hw/assets/146999555/7eb33506-3e50-4837-9d8d-ddeff911b8fa)
![image](https://github.com/jinnonn/RabbitMQ-netology-hw/assets/146999555/dac3727d-0914-48e1-96ed-cf00e272434c)
![image](https://github.com/jinnonn/RabbitMQ-netology-hw/assets/146999555/2504587f-7da0-4766-b2d0-d9a175d60e96)

Также приложите вывод команды с двух нод:

```shell script
$ rabbitmqctl cluster_status
```
![image](https://github.com/jinnonn/RabbitMQ-netology-hw/assets/146999555/379eb19c-5f83-48cf-b47a-cb6053f69b81)
![image](https://github.com/jinnonn/RabbitMQ-netology-hw/assets/146999555/4c987dc1-c85b-47ae-8814-cffa04a31146)

Для закрепления материала снова запустите скрипт producer.py и приложите скриншот выполнения команды на каждой из нод:

```shell script
$ rabbitmqadmin get queue='hello'
```
![image](https://github.com/jinnonn/RabbitMQ-netology-hw/assets/146999555/04f4a01a-a03e-4af6-a861-08cb0a333f53)
![image](https://github.com/jinnonn/RabbitMQ-netology-hw/assets/146999555/9974c876-b429-4a62-aae5-d41a1aa62aec)

После чего попробуйте отключить одну из нод, желательно ту, к которой подключались из скрипта, затем поправьте параметры подключения в скрипте consumer.py на вторую ноду и запустите его.

![image](https://github.com/jinnonn/RabbitMQ-netology-hw/assets/146999555/c8ec6307-0149-413d-b622-bb9f790c7564)
![image](https://github.com/jinnonn/RabbitMQ-netology-hw/assets/146999555/e820d390-7e66-413a-a4c2-7c2dd429452e)

```
#!/usr/bin/env python
# coding=utf-8
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters('rabbit2',credentials=pika.PlainCredentials('jinon', '1')))
channel = connection.channel()
channel.queue_declare(queue='hello')


def callback(ch, method, properties, body):
    print(" [x] Received %r" % body)


channel.basic_consume(queue='hello', on_message_callback=callback, auto_ack=True)
channel.start_consuming()
```
*Приложите скриншот результата работы второго скрипта.*

