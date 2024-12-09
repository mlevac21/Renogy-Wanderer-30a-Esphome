# Renogy-Wanderer-30a-Esphome

This is a code to let you get data in home assistant from Esphome

I found inspiration with [wrybread project](https://github.com/wrybread/ESP32ArduinoRenogy/tree/main)

esphome:
  name: renogy-charge-controller
  platform: ESP32
  board: esp32dev

wifi:
  ssid: "Deeznutz"
  password: "667Patricia"
  manual_ip:
    static_ip: 192.168.2.2  # Replace X with a unique IP in your network
    gateway: 192.168.2.1    # Typically your router's IP
    subnet: 255.255.255.0
  
  # Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "Renogy Charge Controller"
    password: "configportal"

# Enable logging
logger:

# Enable Home Assistant API
api:
  # No encryption for local network

# Enable over-the-air updates
ota:
  platform: esphome
  password: "your_ota_update_password"

# Modbus configuration
uart:
  - id: mod_uart
    tx_pin: GPIO4  # TXD1 in your original code
    rx_pin: GPIO16  # Changed from GPIO5 to avoid strapping pin
    baud_rate: 9600
    stop_bits: 1
    data_bits: 8
    parity: NONE

modbus:
  - id: mod_bus
    uart_id: mod_uart

sensor:
  # Battery State of Charge
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Battery Capacity"
    register_type: holding
    address: 0x100
    value_type: U_WORD
    unit_of_measurement: "%"
    accuracy_decimals: 0

  # Battery Voltage
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Battery Voltage"
    register_type: holding
    address: 0x101
    value_type: U_WORD
    unit_of_measurement: "V"
    accuracy_decimals: 1
    filters:
      - multiply: 0.1

  # Battery Charge Current
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Battery Charge Current"
    register_type: holding
    address: 0x102
    value_type: U_WORD
    unit_of_measurement: "A"
    accuracy_decimals: 1
    filters:
      - multiply: 0.1

 # Controller Temperature
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Controller Temperature"
    register_type: holding
    address: 0x103
    value_type: S_WORD  # Changed to signed word
    unit_of_measurement: "°C"
    accuracy_decimals: 0
    filters:
      - lambda: return (static_cast<int16_t>(x) >> 8);
    
  # Battery Temperature
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Battery Temperature"
    register_type: holding
    address: 0x103
    value_type: S_WORD  # Changed to signed word
    unit_of_measurement: "°C"
    accuracy_decimals: 0
    filters:
      - lambda: return (static_cast<int16_t>(x) & 0xFF);

  # Load Voltage
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Load Voltage"
    register_type: holding
    address: 0x104
    value_type: U_WORD
    unit_of_measurement: "V"
    accuracy_decimals: 1
    filters:
      - multiply: 0.1

  # Load Current
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Load Current"
    register_type: holding
    address: 0x105
    value_type: U_WORD
    unit_of_measurement: "A"
    accuracy_decimals: 2
    filters:
      - multiply: 0.01

  # Load Power
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Load Power"
    register_type: holding
    address: 0x106
    value_type: U_WORD
    unit_of_measurement: "W"
    accuracy_decimals: 0

  # Solar Panel Voltage
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Solar Panel Voltage"
    register_type: holding
    address: 0x107
    value_type: U_WORD
    unit_of_measurement: "V"
    accuracy_decimals: 1
    filters:
      - multiply: 0.1

  # Solar Panel Current
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Solar Panel Current"
    register_type: holding
    address: 0x108
    value_type: U_WORD
    unit_of_measurement: "A"
    accuracy_decimals: 2
    filters:
      - multiply: 0.01

  # Solar Panel Power
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Solar Panel Power"
    register_type: holding
    address: 0x109
    value_type: U_WORD
    unit_of_measurement: "W"
    accuracy_decimals: 0

  # Min Battery Voltage Today
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Min Battery Voltage Today"
    register_type: holding
    address: 0x10B
    value_type: U_WORD
    unit_of_measurement: "V"
    accuracy_decimals: 1
    filters:
      - multiply: 0.1

  # Max Battery Voltage Today
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Max Battery Voltage Today"
    register_type: holding
    address: 0x10C
    value_type: U_WORD
    unit_of_measurement: "V"
    accuracy_decimals: 1
    filters:
      - multiply: 0.1

  # Max Charge Current Today
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Max Charge Current Today"
    register_type: holding
    address: 0x10D
    value_type: U_WORD
    unit_of_measurement: "A"
    accuracy_decimals: 1
    filters:
      - multiply: 0.1

  # Max Discharge Current Today
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Max Discharge Current Today"
    register_type: holding
    address: 0x10E
    value_type: U_WORD
    unit_of_measurement: "A"
    accuracy_decimals: 1
    filters:
      - multiply: 0.1

  # Max Charge Power Today
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Max Charge Power Today"
    register_type: holding
    address: 0x10F
    value_type: U_WORD
    unit_of_measurement: "W"
    accuracy_decimals: 0

  # Max Discharge Power Today
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Max Discharge Power Today"
    register_type: holding
    address: 0x110
    value_type: U_WORD
    unit_of_measurement: "W"
    accuracy_decimals: 0

  # Charge Amp/Hrs Today
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Charge Amp Hours Today"
    register_type: holding
    address: 0x111
    value_type: U_WORD
    unit_of_measurement: "Ah"
    accuracy_decimals: 0

  # Discharge Amp/Hrs Today
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Discharge Amp Hours Today"
    register_type: holding
    address: 0x112
    value_type: U_WORD
    unit_of_measurement: "Ah"
    accuracy_decimals: 0

  # Charge Watt/Hrs Today
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Charge Watt Hours Today"
    register_type: holding
    address: 0x113
    value_type: U_WORD
    unit_of_measurement: "Wh"
    accuracy_decimals: 0

  # Discharge Watt/Hrs Today
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Discharge Watt Hours Today"
    register_type: holding
    address: 0x114
    value_type: U_WORD
    unit_of_measurement: "Wh"
    accuracy_decimals: 0

  # Controller Uptime
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Controller Uptime"
    register_type: holding
    address: 0x115
    value_type: U_WORD
    unit_of_measurement: "days"
    accuracy_decimals: 0

  # Total Battery Over-charges
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Total Battery Over-charges"
    register_type: holding
    address: 0x116
    value_type: U_WORD
    unit_of_measurement: "count"
    accuracy_decimals: 0

  # Total Battery Full Charges
  - platform: modbus_controller
    modbus_controller_id: renogy_controller
    name: "Total Battery Full Charges"
    register_type: holding
    address: 0x117
    value_type: U_WORD
    unit_of_measurement: "count"
    accuracy_decimals: 0


modbus_controller:
  - id: renogy_controller
    modbus_id: mod_bus
    address: 0x01  # Changed from 0xFF to 0x01 (typical Modbus slave address)
    update_interval: 10s  # Reduced from 60s for more frequent updates
