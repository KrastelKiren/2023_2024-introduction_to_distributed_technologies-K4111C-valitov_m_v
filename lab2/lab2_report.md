University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed   technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2023/2024  
Group: K4111C  
Author: Valitov Maxim Vasilievich  
Lab: Lab2  
Date of create: 18.10.2023  
Date of finished:  

# 1. Запуск Minikube  
- Запускаем minikube: 
```
minikube start --driver=docker
```
![pics/1](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab2/pics/1.png?raw=true)  
# 2. Написание манифеста  
Манифет представлен в [этом файле]()  
Опишем новые строки:  
- **kind: Deployment** - тип создаваемого объекта - Deployment. Это тип - это контроллер развертывания, который позволяет обновлять поды и реплики декларативно  
- **spec** - требуемое состояние объекта. Параметры:  
--replicas - количество реплик пода  
--template - описание шаблона пода  
--selector - селектор, необходим для идентификации набора объектов по меткам (Labels)  

Для контейнеров также добавилось новое описание env. Environment variables - это описание переменных окружения. В нашем случае, это переменные:  
- REACT_APP_USERNAME = KrastelKiren  
- REACT_APP_COMPANY_NAME = ITMO_Company  

# 3. Запуск
- Загружаем манифест:
```
minikube cubectl -- apply -f Manifest2.yaml
```
![pics/2](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab2/pics/2.png?raw=true)
- Смотрим, какие объекты были созданы:
```
minikube kubectl get all
```
![pics/3](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab2/pics/3.png?raw=true)
Как мы видим, был создан deployment, replicaset и 2 контейнера.

- Создаём сервис для доступа к контейнеру:
```
minikube kubectl -- expose deployment.apps secondlab-deployment --type=NodePort --port=3000
```
- Проверяем список объектов:

![pics/4](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab2/pics/4.png?raw=true)
- Запускаем и переходим на **http://localhost:3000**:
```
minikube kubectl -- port-forward service/secondlab-deployment 3000:3000
```
![pics/5](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab2/pics/5.png?raw=true)
Значения **REACT_APP_USERNAME** и **REACT_APP_COMPANY_NAME** меняться не будут, так как оба пода имеют одинаковые значения этих переменных.

# 4. Логи
![pics/6](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab2/pics/6.png?raw=true)
