# INFLUX_NUT
Link between NUT and INFLUXDB

################# INFLUX-NUT######################

```
mkdir /var/tmp/influx-nut
cd /var/tmp/influx-nut
git clone https://github.com/lf-/influx_nut.git 			
```

############### configurar Dockerfile ############

```
nano /var/tmp/influx-nut/Dockerfile
```

--------------- Archivo Dockerfile ---------------

```
FROM python:3.7

ADD influx_nut.json /etc/influx_nut.json
ADD influx_nut /influx_nut
WORKDIR /influx_nut
RUN python setup.py install

CMD influx_nut --config /etc/influx_nut.json
```

############## configurar influx_nut.json ############

```
nano /var/tmp/influx-nut/influx_nut.json 
```

--------------- Archivo influx_nut.json ---------------

```
{
 "nut_host": "localhost",
 "nut_ups": "UPS 1",
 "nut_vars": {
 "ups.load": {"type": "int", "measurement_name": "ups_load"},
 "input.voltage": {"type": "float", "measurement_name": "ups_voltage"},
 "battery.charge": {"type": "int", "measurement_name": "ups_charge"}
  },
 "influx_host": "http://localhost:8086",
 "influx_creds": ["THE_INFLUXDB_USER", "THE_INFLUXDB_PASSWORD"],
 "influx_db": "telegraf",
 "influx_tags": {
 "ups": "NAME_OF_YOUR_UPS"
  }
}
```

############### Crear Docker ########################

```
cd /var/tmp/influx-nut
docker build . -t influxdb/nut
```

############### Creamos Directorio influx_nut ##########

```
mkdir -p /docker-data/influx-nut/
```

############### Configuramos influx_nut.json ##########

```
nano /docker-data/influx-nut/influx_nut.json
```

-------------------- Archivo influx_nut.json ------------

```
{
   "nut_host": "host",
   "nut_ups": "UPS 1",
   "nut_vars": {
   "input.L1-N.voltage": {"type": "float", "measurement_name": "ups_l1n_voltage"},
   "input.L2-N.voltage": {"type": "float", "measurement_name": "ups_l2n_voltage"},
   "input.L3-N.voltage": {"type": "float", "measurement_name": "ups_l3n_voltage"},
  
   "input.L1.current": {"type": "float", "measurement_name": "ups_l1_current_in"},
   "input.L2.current": {"type": "float", "measurement_name": "ups_l2_current_in"},
   "input.L3.current": {"type": "float", "measurement_name": "ups_l3_current_in"},
  
   "input.L1.realpower": {"type": "float", "measurement_name": "ups_l1_real_in"},
   "input.L2.realpower": {"type": "float", "measurement_name": "ups_l2_real_in"},
   "input.L3.realpower": {"type": "float", "measurement_name": "ups_l3_real_in"},

   "input.bypass.L1-N.voltage": {"type": "float", "measurement_name": "ups_l1n.by"},
   "input.bypass.L2-N.voltage": {"type": "float", "measurement_name": "ups_l2n.by"},
   "input.bypass.L3-N.voltage": {"type": "float", "measurement_name": "ups_l3n.by"},
  
   "output.L1-N.voltage": {"type": "float", "measurement_name": "ups_l1n_vol_out"},
   "output.L2-N.voltage": {"type": "float", "measurement_name": "ups_l2n_vol_out"},
   "output.L3-N.voltage": {"type": "float", "measurement_name": "ups_l3n_vol_out"},
  
   "output.L1.current": {"type": "float", "measurement_name": "ups_l1_current_out"},
   "output.L2.current": {"type": "float", "measurement_name": "ups_l2_current_out"},
   "output.L3.current": {"type": "float", "measurement_name": "ups_l3_current_out"},
  
   "output.L1.realpower": {"type": "float", "measurement_name": "ups_l1_real_out"},
   "output.L2.realpower": {"type": "float", "measurement_name": "ups_l2_real_out"},
   "output.L3.realpower": {"type": "float", "measurement_name": "ups_l3_real_out"},
  
   "output.L1.power.percent": {"type": "float", "measurement_name": "ups_l1_power_out"},
   "output.L2.power.percent": {"type": "float", "measurement_name": "ups_l2_power_out"},
   "output.L3.power.percent": {"type": "float", "measurement_name": "ups_l3_power_out"},
  
   "input.frequency": {"type": "float", "measurement_name": "ups_frequency_in"},
   "input.voltage.nominal": {"type": "float", "measurement_name": "ups_vol_nominal_in"},
  
   "output.frequency": {"type": "float", "measurement_name": "ups_frequency_out"},
   "output.frequency.nominal": {"type": "float", "measurement_name": "ups_frequency_n_out"},
   "output.voltage.nominal": {"type": "float", "measurement_name": "ups_vol_n_out"},
 
   "battery.charge": {"type": "float", "measurement_name": "ups_battery_charge"},
   "battery.current": {"type": "float", "measurement_name": "ups_current"},
   "battery.runtime.low": {"type": "float", "measurement_name": "ups_runtime_low"},
   "battery.voltage": {"type": "float", "measurement_name": "ups_voltage"},
   "ups.temperature": {"type": "float", "measurement_name": "ups_temperature"},
   "battery.runtime": {"type": "float", "measurement_name": "ups_runtime"}
      },
   "influx_host": "http://influx1:8086",
   "influx_creds": ["telegraf_w", "password"],
   "influx_db": "telegraf",
   "influx_tags": {
   "ups": "UPS 1"
      }
}
```  

###################### Configuramos docker-compose ###############

```
nano /docker-data/influx-nut/docker-compose.yml
```

-------------------- Archivo docker-compose.yml -----------------

```
version: '3.3'
services:
 nut:
   container_name: influx-nut
   restart: unless-stopped
   image: influxdb/nut
   environment:
      - TZ=America/Santiago
   volumes:
      - './influx_nut.json:/etc/influx_nut.json:ro'
      - '/etc/localtime:/etc/localtime:ro'
   networks:
      - i40sys
   extra_hosts:
      host: x.x.x.21
networks:
 i40sys:
   external:
     name: i40sys
```
###################### ejecutamos docker-compose ###########33

```
cd /docker-data/influx-nut/
docker-compose up 
```

