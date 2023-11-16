University: **[ITMO University](https://itmo.ru/ru/)**  
Faculty: **[FICT](https://fict.itmo.ru)**  
Course: **[Introduction to distributed   technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)**  
Year: **2023/2024**  
Group: **K4111C**  
Author: **Valitov Maxim Vasilievich**  
Lab: **Lab4**  
Date of create: **15.11.2023**  
Date of finished: **16.11.2023**  

# 1. Запуск Minikube и нод
- Запускаем minikube вместе с calico: 
```
minikube start --network-plugin=cni --cni=calico
```
![pics/1](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab4/pics/1.png?raw=true)  
- Создаём две ноды  
```
minikube node add  
```
![pics/2](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab4/pics/2.png?raw=true)
- Проверяем наши ноды  
```
minikube kubectl get nodes  
```
![pics/3](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab4/pics/3.png?raw=true)
- Проверяем поды calico  
```
minikube kubectl -- get pod -l k9s-app=calico-node -A  
```
![pics/4](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab4/pics/4.png?raw=true)

# 2. Настройка IPAM
- Устанавливаем ***calicoctl*** по инструкции [тут](https://docs.tigera.io/calico/latest/operations/calicoctl/install)  
- Удаляем стандартный существующий IPPool  
```
kubectl-calico delete ippools default-ipv4-ippool  
```
![pics/5](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab4/pics/5.png?raw=true)
- Добавляем метки к каждой ноде  
```
minikube kubectl -- label nodes minikube rack=0  
minikube kubectl -- label nodes minikube-m02 rack=1  
```
![pics/6](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab4/pics/6.png?raw=true)
- Проверяем наши метки
```
minikube kubectl -- get nodes -l rack=0  
minikube kubectl -- get nodes -l rack=1  
```
![pics/7](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab4/pics/7.png?raw=true)
# 3. Написание манифеста для calico

Манифест для calico представлен в [этом файле](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab4/Manifest4_Calico.yaml)  
Этот манифест создаёт новые IPPool'ы для каждой ноды, основываясь на их метках  
Опишем новые строки:  
- **kind: IPPool** - тип создаваемого объекта - IPPool. IPPool - это набор IP-адресов для Calico.
- **spec** - спецификация IPPool
  - **cidr** - Диапазон IP-адресов, который будет использоваться для этого пула.
  - **ipipMode** - параметр, который определяет режим использования протокола IP-in-IP (IPIP).
  - **natOutgoing** - параметр, который определяет будет ли выполняться Network Address Translation (NAT) при исходящем сетевом трафике из пула IP-адресов.
  - **nodeSelector** - выбор узлов, которым Calico IPAM должен назначить адреса из этого пула.

- Применяем манифест
```
kubectl-calico apply -f Manifest4_Calico.yaml
```
![pics/8](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab4/pics/8.png?raw=true)  

-Проверяем, всё ли успешно создано
```
kubectl-calico get ipp  
```
![pics/9](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab4/pics/9.png?raw=true)

# 4. Написание манифеста
Манифест для calico представлен в [этом файле](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab4/Manifest4.yaml)  
Этот манифест ничем не отличается от предыдущих лабораторных работ, так что описывать его не буду.  

- Проверяем, созданные поды
```
minikube kubectl get pods -o wide
```
![pics/10](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab4/pics/10.png?raw=true)
Как мы видим, поды были созданы в двух разных нодах и им присвоены ip-адреса из указанных диапазонов.  

- Проверяем, создалось ли всё остальное
```
minikube kubectl get all
```
![pics/11](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab4/pics/11.png?raw=true)


# 5. Запуск и пинг
- Запускаем и проверяем:
![pics/12](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab4/pics/12.png?raw=true)

- Пингуем в одну сторону:
```
minikube kubectl -- exec lab-deployment-bcf96d6d4-gh2fj 192.168.10.65
```
![pics/13](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab4/pics/13.png?raw=true)
- Пингуем в другую сторону:
```
minikube kubectl -- exec lab-deployment-bcf96d6d4-rh88c 192.168.11.0
```
![pics/14](https://github.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/blob/main/lab4/pics/14.png?raw=true)

# 6. Схема организации контейнеров и сервисов
![pics/scheme](https://raw.githubusercontent.com/KrastelKiren/2023_2024-introduction_to_distributed_technologies-K4111C-valitov_m_v/ea51f7ddeffce031ec2e3ebf7c78cd8ffc361c39/lab4/pics/ЛР4.svg)


