University: **[ITMO University](https://itmo.ru/ru/)**  
Faculty: **[FICT](https://fict.itmo.ru)**  
Course: **[Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)**  
Year: **2023/2024**  
Group: **K4111C**  
Author: **Valitov Maxim Vasilievich**  
Lab: **Lab1**  
Date of create: **18.10.2023**  
Date of finished: **19.10.2023**  

# 1. Запуск Minikube
- Запускаем minikube: 
```
minikube start --driver=docker
```
![pics/1](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab1/pics/1.png?raw=true)  
**--driver = docker** позволяет явно указать драйвер для миникуба
# 2. Написание манифеста
Манифет представлен в [этом файле](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab1/Manifest.yaml)

Разберём его основные строки:
- **apiVersion** — используемая для создания объекта версия API Kubernetes
- **kind: Pod** - тип создаваемого объекта - Pod
- **metadata** - информация о создаваемом объекте: имя(name), пространство имён(namespace), labels и т.д.
- **spec** - требуемое состояние объекта. В нашём случае, под должен содержать контейнер. Контейнер имеет параметры:  
  - name - имя контейнера  
  - image - образ  
  - ports - информация о порте  

# 3. Запуск
- Загружаем манифест:
```
minikube cubectl -- apply -f Manifest.yaml
```
![pics/2](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab1/pics/2.png?raw=true)  

- Создаём сервис для доступа к контейнеру:
```
minikube kubectl -- expose pod vault --type=NodePort --port=8200
```
![pics/3](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab1/pics/3.png?raw=true)  
- Запускаем:
```
minikube kubectl -- port-forward service/vault 8200:8200
```
![pics/4](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab1/pics/4.png?raw=true)  
- Переходим на **http://localhost:8200**:

![pics/8](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab1/pics/8.png?raw=true)  

# 4. Поиск токена
- Открываем логи пода vault:
```
minikube kubectl logs vault
```
![pics/5](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab1/pics/5.png?raw=true)  
- Очень внимательно смотрим:

![pics/6](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab1/pics/6.png?raw=true)  
- Пробуем авторизоваться по найденному токену:

![pics/7](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab1/pics/7.png?raw=true)  

# 5. Схема организации контейнеров и сервисов
![pics/scheme](https://raw.githubusercontent.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/ba4d5143c8425baa2a63d23c7f3b854316d4f873/lab1/pics/ЛР1.svg)

- API - Сервер API Kubernetes проверяет и настраивает данные для объектов api, которые включают модули, службы, контроллеры репликации и другие.
- kube-controller-manager - это компонент плоскости управления, который отслеживает общее состояние кластера через api-сервер и вносит изменения, пытаясь приблизить текущее состояние к желаемому.
- etcd - это хранилище данных в формате "ключ-значение", которое используется как основное хранилище всех данных кластера в Kubernetes.
- kube-scheduler - это компонент плоскости управления, который отслеживает созданные поды без привязанного узла и выбирает узел, на котором они должны работать.
- kubelet - агент, работающий на каждом узле в кластере. Он следит за тем, чтобы контейнеры были запущены в поде.
- kube-proxy — сетевой прокси, работающий на каждом узле в кластере, который конфигурирует правила сети на узлах. При помощи них разрешаются сетевые подключения к вашими подам изнутри и снаружи кластера.
