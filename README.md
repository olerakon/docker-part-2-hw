# Домашнее задание к занятию "Docker. Часть 2." - `Гилельс Кирилл`

---

### Задание 1

**Напишите ответ в свободной форме, не больше одного абзаца текста.**

Установите Docker Compose и опишите, для чего он нужен и как может улучшить лично вашу жизнь.


### Решение

Установил актуальные docker и docker compose:

![alt text](https://github.com/olerakon/docker-part-2-hw/blob/main/img/6.4.1.1.png)


Docker Compose позволит настроить и запустить много-контейнерные приложения через YAML-файл, обеспечивает воспроизводимые окружения на Linux-серверах, экономит время на ручной запуск контейнеров и отладку сетей.

​
---

### Задание 2 

**Выполните действия и приложите текст конфига на этом этапе.** 

Создайте файл docker-compose.yml и внесите туда первичные настройки: 

 * version;
 * services;
 * volumes;
 * networks.

При выполнении задания используйте подсеть 10.5.0.0/16.
Ваша подсеть должна называться: <ваши фамилия и инициалы>-my-netology-hw.
Все приложения из последующих заданий должны находиться в этой конфигурации.

### Решение
(Для моей версии docker compose 5.0.0 указание версии не требуется)
docker-compose.yml :
```
#version: '5.0'

networks:
  gilels-km-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16

volumes:
    prometheus_data: {}
    grafana_data: {}

services:
```

---

### Задание 3 

**Выполните действия:** 

1. Создайте конфигурацию docker-compose для Prometheus с именем контейнера <ваши фамилия и инициалы>-netology-prometheus. 
2. Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории [6-04/prometheus](https://github.com/netology-code/sdvps-homeworks/tree/main/lecture_demos/6-04/prometheus) ).
3. Обеспечьте внешний доступ к порту 9090 c докер-сервера.

### Решение

services:
```
  gilels-km-netology-prometheus:
    image: prom/prometheus:latest
    container_name: gilels-km-netology-prometheus
    volumes:
      - prometheus_data:/prometheus
      - /home/kirill/netology-docker-hw/configs/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"
    networks:
      - gilels-km-my-netology-hw
```

---

### Задание 4 

**Выполните действия:**

1. Создайте конфигурацию docker-compose для Pushgateway с именем контейнера <ваши фамилия и инициалы>-netology-pushgateway. 
2. Обеспечьте внешний доступ к порту 9091 c докер-сервера.


### Решение

services:
```
  gilels-km-netology-pushgateway:
    image: prom/pushgateway:latest
    container_name: gilels-km-netology-pushgateway
    ports:
      - "9091:9091"
    networks:
      - gilels-km-my-netology-hw
```

---

### Задание 5 

**Выполните действия:** 

1. Создайте конфигурацию docker-compose для Grafana с именем контейнера <ваши фамилия и инициалы>-netology-grafana. 
2. Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории [6-04/grafana](https://github.com/netology-code/sdvps-homeworks/blob/main/lecture_demos/6-04/grafana/custom.ini).
3. Добавьте переменную окружения с путем до файла с кастомными настройками (должен быть в томе), в самом файле пропишите логин=<ваши фамилия и инициалы> пароль=netology.
4. Обеспечьте внешний доступ к порту 3000 c порта 80 докер-сервера.

### Решение

services:
```
  gilels-km-netology-grafana:
    image: grafana/grafana:latest
    container_name: gilels-km-netology-grafana
    volumes:
      - /home/kirill/netology-docker-hw/configs/grafana/custom.ini:/etc/grafana/grafana.ini:ro
      - grafana_data:/var/lib/grafana
    environment:
       - GF_PATHS_CONFIG=/etc/grafana/grafana.ini
    ports:
      - "80:3000"
    networks:
      - gilels-km-my-netology-hw
```
Файл custom.ini:

![alt text](https://github.com/olerakon/docker-part-2-hw/blob/main/img/5.1.png)

---

### Задание 6 

**Выполните действия.**

1. Настройте поочередность запуска контейнеров.
2. Настройте режимы перезапуска для контейнеров.
3. Настройте использование контейнерами одной сети.
5. Запустите сценарий в detached режиме.

### Решение будет приложено в пт.7

---

### Задание 7 

**Выполните действия.**
1. Выполните запрос в Pushgateway для помещения метрики <ваши фамилия и инициалы> со значением 5 в Prometheus: ```echo "<ваши фамилия и инициалы> 5" | curl --data-binary @- http://localhost:9091/metrics/job/netology```.
2. Залогиньтесь в Grafana с помощью логина и пароля из предыдущего задания.
3. Cоздайте Data Source Prometheus (Home -> Connections -> Data sources -> Add data source -> Prometheus -> указать "Prometheus server URL = http://prometheus:9090" -> Save & Test).
4. Создайте график на основе добавленной в пункте 5 метрики (Build a dashboard -> Add visualization -> Prometheus -> Select metric -> Metric explorer -> <ваши фамилия и инициалы -> Apply.

В качестве решения приложите:

* docker-compose.yml **целиком**;
* скриншот команды docker ps после запуске docker-compose.yml;
* скриншот графика, постоенного на основе вашей метрики.

### Решение
Полная версия файла
docker-compose.yml :
```
#version: '5.0'

networks:
  gilels-km-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16

volumes:
    prometheus_data: {}
    grafana_data: {}

services:

  gilels-km-netology-prometheus:
    image: prom/prometheus:latest
    container_name: gilels-km-netology-prometheus
    volumes:
#      - /home/kirill/netology-docker-hw/data/prometheus:/prometheus
      - prometheus_data:/prometheus
      - /home/kirill/netology-docker-hw/configs/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml:ro
    ports:
      - "9090:9090"
    networks:
      - gilels-km-my-netology-hw
    restart: always
    depends_on:
      - gilels-km-netology-pushgateway

  gilels-km-netology-pushgateway:
    image: prom/pushgateway:latest
    container_name: gilels-km-netology-pushgateway
    ports:
      - "9091:9091"
    networks:
      - gilels-km-my-netology-hw
    restart: always

  gilels-km-netology-grafana:
    image: grafana/grafana:latest
    container_name: gilels-km-netology-grafana
    volumes:
#      - /home/kirill/netology-docker-hw/data/grafana:/var/lib/grafana
      - /home/kirill/netology-docker-hw/configs/grafana/custom.ini:/etc/grafana/grafana.ini:ro
      - grafana_data:/var/lib/grafana
#      - ./grafana/provisioning/:/etc/grafana/provisioning/
    environment:
       - GF_PATHS_CONFIG=/etc/grafana/grafana.ini
#      - GF_PATHS_CONFIG=/home/kirill/netology-docker-hw/configs/grafana/custom.ini
    ports:
      - "80:3000"
    networks:
      - gilels-km-my-netology-hw
    restart: always
    depends_on:
      - gilels-km-netology-prometheus

```
Запуск docker compose:

![alt text](https://github.com/olerakon/docker-part-2-hw/blob/main/img/7.1.png)

Далее выполнил для сбора метрик:
bash:
```
echo "gilels_km 5" | curl --data-binary @- http://localhost:9091/metrics/job/netology/instance/netology
sleep 10
echo "gilels_km 7" | curl --data-binary @- http://localhost:9091/metrics/job/netology/instance/netology
sleep 10
echo "gilels_km 3" | curl --data-binary @- http://localhost:9091/metrics/job/netology/instance/netology
```
Графана и графики:

![alt text](https://github.com/olerakon/docker-part-2-hw/blob/main/img/7.2.png)
---

### Задание 8

**Выполните действия:** 

1. Остановите и удалите все контейнеры одной командой.

В качестве решения приложите скриншот консоли с проделанными действиями.

Остановка контейнеров и их удаление:

![alt text](https://github.com/olerakon/docker-part-2-hw/blob/main/img/8.1.png)
---

---

## Дополнительные задания* (со звёздочкой)

Их выполнение необязательное и не влияет на получение зачёта по домашнему заданию. Можете их решить, если хотите лучше разобраться в материале.

---

### Задание 9* 

**Выполните действия:** 

1. Создайте конфигурацию docker-compose для Alertmanager с именем контейнера <ваши фамилия и инициалы>-netology-alertmanager. 
2. Добавьте необходимые тома с данными и [конфигурацией](https://github.com/netology-code/sdvps-homeworks/tree/main/6-04/alertmanager), сеть, режим и очередность запуска.
3. Обновите конфигурацию Prometheus (необходимые изменения ищите в презентации или документации) и перезапустите его. 
4. Обеспечьте внешний доступ к порту 9093 c докер-сервера.

В качестве решения приложите скриншот с событием из Alertmanager.

---

### Задание 10* 

Запустите свой сценарий на чистом железе без предзагруженных образов.

**Ответьте на вопросы в свободной форме:**

1. Опишите выполненный вами процесс развертывания сценария.
2. Как вы думаете зачем может понадобиться такой способ развертывания?
