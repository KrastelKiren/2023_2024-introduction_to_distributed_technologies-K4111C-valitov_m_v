University: **[ITMO University](https://itmo.ru/ru/)**  
Faculty: **[FICT](https://fict.itmo.ru)**  
Course: **[Introduction to distributed   technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)**  
Year: **2023/2024**  
Group: **K4111C**  
Author: **Valitov Maxim Vasilievich**  
Lab: **Lab3**  
Date of create: **26.10.2023**  
Date of finished: **26.10.2023**  

# 1. Запуск Minikube  
- Запускаем minikube: 
```
minikube start
```
![pics/1](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab3/pics/1.png?raw=true)  
# 2. Написание манифеста 

Манифет представлен в [этом файле](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab3/Manifest3.yaml)  
Опишем новые строки:  
- **kind: ConfigMap** - тип создаваемого объекта - ConfigMap. Это объект API, используемый для хранения неконфиденциальных данных в парах ключ-значение. В нашем случае, это переменные:  
  - REACT_APP_USERNAME = KrastelKiren-ConfigMap  
  - REACT_APP_COMPANY_NAME = ITMO-ConfigMap  

- **kind: Ingress** - тип создаваемого объекта - Ingress. Это объект API, который управляет внешним доступом к службам в кластере, обычно по протоколу HTTP. Параметры:
  - tls - секрет с сертификатом TLS
  - rules - список правил для маршрутизации:
    - http - правила для http трафика:
      - paths - cписок путей
      - pathType: Prefix - тип пути префикс: совпадения основаны на префиксе пути URL, разделенном /
      - backend - это объект, относящийся к другому ресурсу Kubernetes в том же пространстве имен, что и входной объект. В нашем случае, это сервис thirdlab.

# 3. Создаём TLS-сертификат
- Создаём ключ:
```
openssl genrsa -out lab.key 2048
```
![pics/2](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab3/pics/2.png?raw=true)
- Создаём TLS-сертификат:
```
openssl req -x509 -new -nodes -key lab.key -subj "/CN=thirdlab.itmo" -out tls.crt
```
  - Обращаем внимание на CN - это доменное имя, в нашем случае **thirdlab.itmo**

# 3. Запуск
- Включаем аддон Ingress:
```
minikube addons enable Ingress
```
![pics/2](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab3/pics/4.png?raw=true)

- Загружаем манифест:
```
minikube cubectl -- apply -f Manifest3.yaml
```
![pics/2](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab3/pics/5.png?raw=true)

- Создаём Secret TLS:
```
minikube kubectl -- create -n itmo secret tls thirdlab-tls --key="lab.key" --cert="tls.crt"
```
![pics/3](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab3/pics/6.png?raw=true)  
Secret TLS - это специальный тип, предназначенный для хранения созданного нами сертификата и ключа.  

- Проверяем список объектов:

![pics/4](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab3/pics/7.png?raw=true)

- Добавляем в **/etc/hosts** IP-адрес minikube и доменное имя
- Запускаем:
```
minikube tunnel
```
![pics/5](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab3/pics/8.png?raw=true)
- Переходим на **http:/thirdlab.itmo** и проверяем сертификат:

![pics/5](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab3/pics/10.png?raw=true)
![pics/5](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab3/pics/9.png?raw=true)

# 4. Схема организации контейнеров и сервисов
![pics/scheme](https://raw.githubusercontent.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/4b7788dc4686410eda973ca27db3a929818b8fa1/lab3/pics/ЛР3.svg)

