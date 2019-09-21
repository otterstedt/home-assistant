# Send hydro quebec consumption through MQTT

## Client Setup

Install pyhydroquebec

https://github.com/titilambert/pyhydroquebec
```
pip install pyhydroquebec
```
Install Packages
```
jq - (CLI JSON Parser)
epel-release (Mosquitto is in the extra packages repo)
mosquitto - (Mosquitto daemon and clients)
```
Fetch Hydro Quebec data and publish on a MQTT topic:
```
pyhydroquebec -u <<User Name>> -p <<Password>> -j | /usr/bin/jq -c '."<<Contract Number>>"' | mosquitto_pub -h <<MQTT Broker Host>> -t '<<MQTT Topic>>' -u "<<MQTT Broker User>>" -P "<<MQTT Broker Password>>" -l -r
```
The ```jq``` command will extract the Hydro Quebec consumption json data for the specified contract number and convert to a single line

## Home Assistant Example Configuration
Example subscribes to MQTT Topic:  ```hass/hydroquebec```
```configuration.yml```
```
  - platform: mqtt
    name: Hydro Quebec Bill
    unique_id: hydroquebec_bill
    state_topic: hass/hydroquebec
    value_template: "{{ value_json.period_total_bill }}"
    unit_of_measurement: 'CAD'
    json_attributes:
      - balance
      - period_projection
      - period_length
      - period_total_days
      - period_total_consumption
      - yesterday_total_consumption
  - platform: mqtt
    name: Hydro Quebec Consumption
    unique_id: hydroquebec_consumption
    state_topic: hass/hydroquebec
    value_template: "{{ value_json.yesterday_total_consumption }}"
    unit_of_measurement: 'kWh'
```

