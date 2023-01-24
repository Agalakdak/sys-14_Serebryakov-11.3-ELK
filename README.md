# Домашнее задание к занятию 11.3. «ELK» - "Ruslan Serebryakov"


### Задание 1. Elasticsearch 

Установите и запустите Elasticsearch, после чего поменяйте параметр cluster_name на случайный. 

*Приведите скриншот команды 'curl -X GET 'localhost:9200/_cluster/health?pretty', сделанной на сервере с установленным Elasticsearch. Где будет виден нестандартный cluster_name*.

![](../img/q1.png)
---

### Задание 2. Kibana

Установите и запустите Kibana.

*Приведите скриншот интерфейса Kibana на странице http://<ip вашего сервера>:5601/app/dev_tools#/console, где будет выполнен запрос GET /_cluster/health?pretty*.


![](../img/q2.png)
---

### Задание 3. Logstash

Установите и запустите Logstash и Nginx. С помощью Logstash отправьте access-лог Nginx в Elasticsearch. 

*Приведите скриншот интерфейса Kibana, на котором видны логи Nginx.*

!!!!!!!!!!!!!!!!!!!!!!!!
Вот, что я делал:
Установил logstash
Установил nginx
Создал в /etc/logstash/conf.d файл nginx_log_search.conf

Содержимое файла ниже

input {
  file {
    path => "/var/log/nginx/access.log"
    start_position => "beginning"
  }
}
filter {
    grok {
        match => { "message" => "%{IPORHOST:remote_ip} - %{DATA:user_name}\[%{HTTPDATE:access_time}\] \"%{WORD:http_method} %{DATA:url}HTTP/%{NUMBER:http_version}\" %{NUMBER:response_code} %{NUMBER:body_sent_bytes}\"%{DATA:referrer}\" \"%{DATA:agent}\"" }
    }
    mutate {
        remove_field => [ "host" ]
    }
}
output {
    elasticsearch {
        hosts => "84.201.139.232:9200"
        data_stream => "true"
    }
}

выполнил команду systemctl restart logstash
И ничего не поменялось, в http://84.201.139.232:5601/app/kibana_overview
Там нет никаких данных. 
Я не понимаю где мне смотреть эти изменения и почему мои действия не привели к результату

Рисунок ниже - просто окно в котором как я понял должны были отобразиться логи(ну или произойти какие-то изменения)

---

### Задание 4. Filebeat. 

Установите и запустите Filebeat. Переключите поставку логов Nginx с Logstash на Filebeat. 

*Приведите скриншот интерфейса Kibana, на котором видны логи Nginx, которые были отправлены через Filebeat.*


## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

### Задание 5*. Доставка данных 

Настройте поставку лога в Elasticsearch через Logstash и Filebeat любого другого сервиса , но не Nginx. 
Для этого лог должен писаться на файловую систему, Logstash должен корректно его распарсить и разложить на поля. 

*Приведите скриншот интерфейса Kibana, на котором будет виден этот лог и напишите лог какого приложения отправляется.*
