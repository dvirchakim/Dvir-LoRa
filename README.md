# LoRaWAN Device with Deep Sleep and Session Persistence

This project demonstrates how to implement a LoRaWAN device using the LMIC library with support for deep sleep and session persistence across deep sleep cycles and full power cycles. The device periodically collects sensor data and transmits it over a LoRaWAN network. To conserve power, the device enters deep sleep between transmissions. It also saves the network session state to non-volatile memory to avoid rejoining the network after every wake-up.



![WhatsApp Image 2024-08-15 at 01 40 01](https://github.com/user-attachments/assets/7542691a-f426-455a-807d-e46ec4e08e25)




## Features

- **Deep Sleep:** The device enters deep sleep between data transmissions to conserve power.
- **Session Persistence:** The device stores the LoRaWAN session state in EEPROM, allowing it to retain the session across deep sleep cycles and full power cycles.
- **Automatic Rejoin:** The device automatically rejoins the network if it loses session validity or after a specified number of wake-up cycles.
- **Dynamic Transmission Interval:** The transmission interval can be adjusted via downlink messages.

## Components

- **ESP32 or ESP8266 Microcontroller:** Provides deep sleep functionality and non-volatile memory storage.
- **GP2YDustSensor:** A dust sensor that collects air quality data.
- **LoRaWAN Module:** Communicates with a LoRaWAN gateway using the LMIC library.

## Setup

1. **Install Required Libraries:**
   - `Arduino LMIC library`
   - `EEPROM`
   - `GP2YDustSensor`

2. **Wiring:**
   - Connect the sensors (PIR, MQ135, MQ7, and dust sensor) to the appropriate GPIO pins on your ESP32 or ESP8266.
   - Connect the LoRaWAN module (e.g., RFM95) to the appropriate SPI pins on your microcontroller.

3. **Configure LoRaWAN Keys:**
   - Update the `DEVEUI`, `APPEUI`, and `APPKEY` constants with your device's LoRaWAN credentials.

4. **Upload the Code:**
   - Upload the code to your ESP32 or ESP8266 using the Arduino IDE.

5. **Monitor Serial Output:**
   - Use the Arduino Serial Monitor to view the device's status, including whether it joins the network successfully and sends data.

## Code Explanation

### Key Sections of the Code

1. **Deep Sleep Functionality:**
   - After transmitting data, the device enters deep sleep using the `ESP.deepSleep(TX_INTERVAL);` command. The `TX_INTERVAL` is set based on the transmission interval and is stored in microseconds.

2. **Session Persistence:**
   - The session state is stored in EEPROM, which retains the data across full power cycles. The session state includes the network ID, device address, and session keys.

3. **Automatic Rejoin:**
   - The device periodically rejoins the network if the session state becomes invalid or after a set number of wake-up cycles (controlled by `MAX_WAKEUPS_BEFORE_REJOIN`).

4. **Dynamic Transmission Interval:**
   - The device can receive downlink messages to adjust the transmission interval dynamically. The interval is stored in seconds and can be adjusted via a single-byte or two-byte downlink payload.

### Code Walkthrough

- **`setup()`:**
  - Initializes the device, reads session data from EEPROM, and either restores the session or joins the network based on the saved session state.

- **`loop()`:**
  - Runs the LMIC loop, checking for events and handling them as they occur.

- **`onEvent()`:**
  - Handles LMIC events, such as joining the network, transmitting data, and receiving downlink messages. It saves the session state to EEPROM after a successful join and enters deep sleep after data transmission.

- **`saveSessionState()` and `restoreSessionState()`:**
  - These functions handle saving and restoring the session state to and from EEPROM, ensuring that the device can retain its session across power cycles.

- **`do_send()`:**
  - Gathers sensor data and queues it for transmission over the LoRaWAN network.

## Usage Notes

- **EEPROM Wear:** Be mindful of EEPROM's limited write cycles. The code is designed to minimize unnecessary writes by only saving the session state after a successful network join.
- **Session Validity:** The code assumes the session remains valid between wake-up cycles. However, if the session becomes invalid (e.g., due to a network timeout), the device will attempt to rejoin automatically.
- **Power Optimization:** This code is optimized for low power usage by leveraging deep sleep and avoiding unnecessary network joins.

## Troubleshooting

- **Device Not Joining:** Ensure that the LoRaWAN keys are correctly configured and that the device is within range of a LoRaWAN gateway.
- **Frequent Network Joins:** If the device frequently rejoins the network, consider increasing the `MAX_WAKEUPS_BEFORE_REJOIN` value or ensuring that the device is within network coverage during all wake-up cycles.
- **EEPROM Overwrite:** If you experience issues with session persistence, consider using external storage with higher write cycles for storing session data.

## License

This code is open-source and available under the MIT License. Feel free to modify and distribute as needed.
