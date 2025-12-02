# Лабораторная работа №6 — Балансирование нагрузки и авто‑масштабирование  
---

# Шаг 1. Создание VPC и подсетей

1. Перешла в **VPC → Create VPC → VPC and more (wizard)**.
2. Указала:
   - VPC CIDR: `10.0.0.0/16`
   - Public subnets: `10.0.1.0/24`, `10.0.2.0/24`
   - Private subnets: `10.0.3.0/24`, `10.0.4.0/24`

<img width="468" height="136" alt="image" src="https://github.com/user-attachments/assets/920a6efe-36e4-422a-bd5a-5d0a8157cf8d" />

3. Создала **Internet Gateway** → Attach to VPC.

<img width="468" height="99" alt="image" src="https://github.com/user-attachments/assets/5d9f1dbf-919d-4812-a0c0-7949dcb872ae" />\
<img width="468" height="146" alt="image" src="https://github.com/user-attachments/assets/463d91c9-d2b5-4cf4-8f8b-85464b5b55e1" />\
4. В Route Table публичных подсетей:
   - `0.0.0.0/0 → Internet Gateway`.

<img width="468" height="216" alt="image" src="https://github.com/user-attachments/assets/77a318fc-54f5-45b1-a33e-7ecaeafe08f7" />\
<img width="468" height="185" alt="image" src="https://github.com/user-attachments/assets/b5161e11-0682-46cd-9ed4-a170bb13da58" />\
<img width="468" height="119" alt="image" src="https://github.com/user-attachments/assets/599c4bc8-a763-48a7-bd5d-e0d253ed4301" />

---

# Шаг 2. Создание EC2 и установка nginx

1. **EC2 → Launch instance**.
2. Параметры:
   - AMI: *Amazon Linux 2*
  
<img width="468" height="272" alt="image" src="https://github.com/user-attachments/assets/b79b3a18-78e3-440f-919e-ad3596fa7ea1" />

   - Instance type: *t3.micro*

<img width="468" height="232" alt="image" src="https://github.com/user-attachments/assets/235d9f64-ddf1-48f5-a36d-26ffca25daa8" />

   - Subnet: публичная
   - Auto-assign Public IP → *Enable*

<img width="468" height="234" alt="image" src="https://github.com/user-attachments/assets/f95c9434-e7b7-49c9-901b-6c198b6f96a6" />

3. Security Group:
   - SSH (22) — My IP  
   - HTTP (80) — 0.0.0.0/0
  
<img width="468" height="300" alt="image" src="https://github.com/user-attachments/assets/afc52880-3ceb-44e2-9b4f-7ade7e416b95" />

4. Advanced details:
   - Detailed monitoring → Enable

<img width="468" height="100" alt="image" src="https://github.com/user-attachments/assets/9bbda85a-9b18-4b75-adba-d0f8470946ca" />

   - User Data:

<img width="468" height="317" alt="image" src="https://github.com/user-attachments/assets/7dfd9703-4b6d-4b66-9d77-9f0282e745e6" />

5. Дождаться **3/3 checks passed**.

<img width="468" height="89" alt="image" src="https://github.com/user-attachments/assets/68bd160d-d0aa-433c-8727-aa817a67ea51" />

6. Открыла публичный IP → страница nginx загружается успешно.
   
<img width="764" height="191" alt="Снимок экрана 2025-12-03 в 01 03 17" src="https://github.com/user-attachments/assets/3543db01-1d04-4b28-bd56-3927ff4d6d3d" />

---

# Шаг 3. Создание AMI

1. EC2 → Instance → **Actions → Image and templates → Create image**.
2. Имя: `project-web-server-ami`.

<img width="468" height="226" alt="image" src="https://github.com/user-attachments/assets/a1aec8b4-895b-41c8-adce-a1bbe8f3c5f3" />

3. Дождалась появления AMI в **EC2 → AMIs**.

<img width="468" height="94" alt="image" src="https://github.com/user-attachments/assets/0697a98b-31e4-47a0-a846-43f43514cf19" />

### AMI vs Snapshot (кратко)
- **AMI** — полный шаблон EC2 (ОС + настройки + программы).  
- **Snapshot** — копия диска EBS.  
- AMI используется для Auto Scaling и развёртывания новых серверов.
  
---

# Шаг 4. Создание Launch Template  

1. В разделе **EC2** открыла пункт **Launch Templates → Create launch template**.  
2. Заполнила поля:
   - **Name**: `project-launch-template`

<img width="468" height="268" alt="image" src="https://github.com/user-attachments/assets/5e5c62dc-c918-46bb-beed-9f3e0587c0cc" />

   - **AMI**: выбрала ранее созданный образ `project-web-server-ami` (My AMIs)

<img width="468" height="324" alt="image" src="https://github.com/user-attachments/assets/2c2b7b00-08ad-496d-af0b-86935c606a8b" />

   - **Instance type**: `t3.micro`

<img width="468" height="126" alt="image" src="https://github.com/user-attachments/assets/517632d0-faf1-467d-a98f-aeb72264fa40" />

   - **Security groups**: выбрала ту же группу безопасности, что и у веб-сервера:

<img width="468" height="239" alt="image" src="https://github.com/user-attachments/assets/bcdfaa17-5e59-4d8c-b498-879ef9bc345d" />

3. В блоке **Advanced details** включила опцию **Detailed CloudWatch monitoring → Enable**.

<img width="468" height="72" alt="image" src="https://github.com/user-attachments/assets/ba8c370f-148e-4ba2-ac8d-2bcbceb9312c" />

4. Нажала кнопку **Create launch template** и сохранила шаблон.

<img width="468" height="233" alt="image" src="https://github.com/user-attachments/assets/b374e518-137c-4208-b9b4-0ecb78b8e122" />\
<img width="468" height="74" alt="image" src="https://github.com/user-attachments/assets/45454276-fac3-48dd-9027-5d09a614b360" />

---

## Шаг 5. Создание Target Group

1. В разделе **EC2** открыла **Target Groups → Create target group**.  
2. Указала параметры:
   - **Target group name**: `project-target-group`
   - **Target type**: `Instances`
  
<img width="468" height="225" alt="image" src="https://github.com/user-attachments/assets/2c0dad45-2a9b-48c7-ac46-83620e85a1d2" />

   - **Protocol**: `HTTP`
   - **Port**: `80`

<img width="468" height="177" alt="image" src="https://github.com/user-attachments/assets/9c6299d3-3d55-467f-bb03-9fdc940e8366" />

   - **VPC**: выбрала созданную VPC
3. Нажала **Next → Next → Create target group**, завершив создание группы целей.

<img width="468" height="193" alt="image" src="https://github.com/user-attachments/assets/53d8b07f-3c7f-42a0-8292-25f5bfc68d49" />

---

## Шаг 6. Создание Application Load Balancer

1. В разделе **EC2** открыла **Load Balancers → Create Load Balancer → Application Load Balancer**.  
2. Указала основные параметры:
   - **Name**: `project-alb`
   - **Scheme**: `Internet-facing`

<img width="468" height="216" alt="image" src="https://github.com/user-attachments/assets/72d5fd75-d77b-4e47-9f03-b73c68be5dbe" />

3. В блоке **Network mapping** выбрала две **публичные подсети**, созданные ранее.

<img width="468" height="298" alt="image" src="https://github.com/user-attachments/assets/d099cb00-a15e-441a-9dfa-21ccbd0d9b84" />

4. В блоке **Security groups** выбрала ту же группу безопасности, которая используется для веб-сервера (разрешён HTTP).

<img width="445" height="183" alt="image" src="https://github.com/user-attachments/assets/4ee78de6-39bc-43a2-b22f-cb2501beab7a" />

5. В разделе **Listeners** настроила:
   - **Protocol**: `HTTP`
   - **Port**: `80`
   - **Default action**: выбрала `Forward to project-target-group`

<img width="468" height="259" alt="image" src="https://github.com/user-attachments/assets/c795d5da-0876-4c7a-ae48-c4f15297ca07" />

6. Нажала **Create load balancer**.

<img width="468" height="215" alt="image" src="https://github.com/user-attachments/assets/f1777aea-872a-4df5-b864-c64137d4ee32" />

7. Открыла вкладку **Resource map** и убедилась, что от Listener по правилу трафик перенаправляется в Target Group `project-target-group`.

<img width="468" height="197" alt="image" src="https://github.com/user-attachments/assets/5e77972d-5a1d-4575-adab-1924af2db858" />

---

## Шаг 7. Создание Auto Scaling Group

1. В разделе **EC2** открыла **Auto Scaling Groups → Create Auto Scaling group**.  
2. Указала параметры:
   - **Name:** `project-auto-scaling-group`
   - **Launch template:** выбрала `project-launch-template`

<img width="468" height="262" alt="image" src="https://github.com/user-attachments/assets/0d55ce76-34f7-44a0-a33d-baae93f74d75" />

3. Перешла в **Choose instance launch options**.
4. В блоке **Network** выбрала:
   - свою **VPC**
   - две **приватные подсети**
5. Выбрала **Availability Zone distribution → Balanced best effort**.

<img width="468" height="289" alt="image" src="https://github.com/user-attachments/assets/a331557b-150e-4827-9f4f-7fc3d5f0a3fc" />

6. В разделе **Integrate with other services** выбрала:
   - **Attach to an existing load balancer**
   - Target group: `project-target-group`

<img width="468" height="276" alt="image" src="https://github.com/user-attachments/assets/b471f592-b0fe-4430-82ec-d8f81726fd90" />

7. В разделе **Configure group size and scaling** указала:
   - **Minimum:** 2  
   - **Desired:** 2  
   - **Maximum:** 4

<img width="468" height="268" alt="image" src="https://github.com/user-attachments/assets/7d9fe375-df41-46e5-ba98-8e30790018fd" />

8. Добавила **Target tracking scaling policy**:
   - **Average CPU utilization:** 50%
   - **Instance warm-up:** 60 секунд
  
<img width="468" height="206" alt="image" src="https://github.com/user-attachments/assets/22063c7c-a7b2-4c33-b35c-f6a85b01d248" />

9. В разделе **Additional settings** включила:
   - **Enable group metrics collection within CloudWatch**
10. Нажала **Create Auto Scaling group**.

<img width="468" height="78" alt="image" src="https://github.com/user-attachments/assets/b69ac1c1-82c0-4608-a36d-fea5da9ee2d5" />

### Почему выбираются приватные подсети?
Потому что инстансы Auto Scaling не должны быть доступны напрямую из интернета. Доступ к ним идёт только через Load Balancer — это безопаснее и правильнее.

### Зачем нужна Availability Zone distribution?
Эта настройка распределяет инстансы между зонами доступности, обеспечивая отказоустойчивость — если одна зона упадёт, система продолжит работать.

### Что такое Instance warm-up?
Это время, в течение которого новый EC2 не учитывается в метриках. Нужен, чтобы Auto Scaling не реагировал на временно повышенную нагрузку во время запуска инстанса.

---

## Шаг 8. Тестирование Application Load Balancer

1. В разделе **EC2 → Load Balancers** выбрала созданный ALB.  
2. Скопировала его DNS-имя.  
3. Вставила DNS в браузер — открылась страница nginx.  
4. Несколько раз обновила страницу и увидела разные IP-адреса в ответах.

<img width="468" height="111" alt="image" src="https://github.com/user-attachments/assets/117839e9-f42f-44fc-a074-6f08ca4ea9e4" />\
<img width="468" height="86" alt="image" src="https://github.com/user-attachments/assets/8de3b995-763b-4883-be64-09f35af009dd" />

### Какие IP-адреса я вижу и почему?
Я вижу приватные IP-адреса EC2-инстансов из Auto Scaling Group, потому что Load Balancer распределяет трафик между ними, чередуя ответы от каждого сервера.

---

## Шаг 9. Тестирование Auto Scaling

1. Перешла в **CloudWatch → Alarms** и убедилась, что созданы alarm-правила Auto Scaling.
   <img width="468" height="167" alt="image" src="https://github.com/user-attachments/assets/e836d26c-5341-4831-aa6a-d0d92994f8e4" />

2. Открыла AlarmHigh и посмотрела график CPU — он был на уровне 0–1%.  
3. Создала нагрузку, открыв в браузере 6–7 вкладок с адресом:\
   <img width="468" height="60" alt="image" src="https://github.com/user-attachments/assets/c2bc7ab3-6a69-48ed-8d89-8b38d7fcb1a2" />


```
http://project-alb-1729481806.eu-central-1.elb.amazonaws.com/load?seconds=60
```

4. Вернулась в CloudWatch — график CPU начал расти.\
   <img width="468" height="267" alt="image" src="https://github.com/user-attachments/assets/be0c48fa-85c0-4058-bd5e-c4625441d3c2" />

5. Через 2–3 минуты AlarmHigh сработал.\
   <img width="468" height="115" alt="image" src="https://github.com/user-attachments/assets/2d17b33d-1976-406b-b9af-02d36b381fa6" />

6. Перешла в **EC2 → Instances** и увидела, что Auto Scaling создал новые EC2-инстансы.\
   <img width="468" height="146" alt="image" src="https://github.com/user-attachments/assets/eb197c56-b9b1-4391-80e7-9524f970b0b1" />


### Роль Auto Scaling:
Auto Scaling автоматически увеличил количество серверов при росте нагрузки, обеспечив стабильную работу веб-приложения.

---

## Шаг 10. Завершение работы и очистка ресурсов

1. Остановила нагрузочный тест (закрыла вкладки браузера).  
2. Удалила ресурсы в таком порядке:
   - **Load Balancer → Delete**
   - **Target Group → Delete**
   - **Auto Scaling Group → Delete**
   - **EC2 Instances → Terminate**
   - **AMI → Deregister + Delete snapshot**
   - **Launch Template → Delete**
   - **VPC → Delete вместе с подсетями**

### Вывод
В ходе лабораторной работы я самостоятельно развернула отказоустойчивую и масштабируемую архитектуру в AWS, включающую виртуальные машины EC2, балансировщик нагрузки Application Load Balancer, Target Group и Auto Scaling Group на основе собственного AMI. Я настроила приватные и публичные подсети в VPC, обеспечила корректное сетевое взаимодействие, автоматическую установку веб-сервера nginx, а также конфигурацию мониторинга и автоматического реагирования на рост нагрузки через CloudWatch. В результате система корректно масштабировалась при увеличении запросов, что подтвердило правильность реализации и продемонстрировало работу современных облачных механизмов масштабирования и распределения нагрузки.
