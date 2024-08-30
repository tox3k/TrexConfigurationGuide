# Инструкция по установке и настройке Trex Cisco Traffic Generator
#### <i>Примечание.</i>
<i>Все действия выполняются на ОС Ubuntu 22.04LTS. Нет никаких гарантий, что указанная инструкции будет работать на других ОС</i>

### 1. После установки системы открываем консоль и заходим за суперпользователя <p></p>
```bash
sudo su
```
### 2. Обновляем все пакеты  <p></p>
```bash
apt update -y && apt upgrade -y
```
### 3. Скачиваем net-tools  <p></p>
```bash
apt install net-tools
```
### 4. Скачиваем dpdk  <p></p>
```bash
apt install dpdk -y
```
### 5. Включаем драйвер vfio-pci <p></p>
```bash
modprobe vfio-pci
```
### 6. Биндим интерфейсы в dpdk: <p></p>
   - Смотрим все интерфейсы
      ```bash
     dpdk-devbind.py -s
      ```
   - Выбираем интерфейсы для trex  и биндим к vfio-pci
      ```bash
     dpdk-devbind.py -b vfio-pci 01:00.0 
     #Последние цифры номера интерфейса, как в выводе предыдущего пункта

     # Если вылазит ошибка, то перед этой командой выполняем
     echo 1 > /sys/module/vfio/parameters/enable_unsafe_noiommu_mode
      ```
### 7. Скачиваем и распаковываем trex: <p></p>
```bash
mkdir -p /opt/trex
cd opt/trex
wget —no-cache --no-check-certificate https://trex-tgn.cisco.com/trex/release/v2.61.tar.gz
tar -xzvf ./v2.61.tar.gz	
```
### 8. Заходим в папку v2.61 <p></p>
```bash
cd v2.61
```
### 9. Скачиваем установщик пакетов python
```bash
apt install python3-pip -y
```
### 10. Устанавливаем пакет setuptools
```bash
pip install setuptools --force
```
### 11. Запускаем интерактивную настройку интерфесов
```bash
./dpdk_setup_ports.py -i
```

![изображение](https://github.com/user-attachments/assets/152bd41f-e1e2-4121-8bf1-e0e4dc40fa2d)
```python
By default, IP based configuration file will be created. Do you want to use MAC based config? (y/N)
#Пишем N
```
![изображение](https://github.com/user-attachments/assets/420335d3-d566-4a6b-9e56-046318aa8e82)

```python
#Выбираем два интерфейса с драйвером vfio-pci
```

![изображение](https://github.com/user-attachments/assets/9105264c-48cd-4b3e-ac21-519143b4ad9d)

```python
For interface 1, assuming loopback to its dual interface.
Putting IP 1.1.1.1, defaeult gw 2.2.2.2 Change ti? (y/N)
#Пишем y
```

Настройка интерфейсов схожа с настройкой в самой системе, кроме того, что тут не указывается маска, поэтому в IP указываем IP интерфейса в default gw соответственно гейтвей

```bash
Print preview of generated config? (y/N)
#Пишем y
```
![изображение](https://github.com/user-attachments/assets/8b118f01-e2d9-4e14-8f34-db570682cfc8)

Проверяем правильность введённых данных

```bash
Save the config to file? (y/N)
#Пишем y
#Жмём Enter
```

### 12. Запускаем trex
```bash
./t-rex-64 -i
```

## Поздравляем вы ~~прошли игру~~ настроили trex
### Теперь можете подключиться к машине на которой он запущен с помощью клиента последней версии (v4.5.6) https://github.com/cisco-system-traffic-generator/trex-stateless-gui/releases/tag/v4.5.6
