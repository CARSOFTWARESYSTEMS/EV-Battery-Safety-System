# AI-Powered EV Battery Fire Prevention System

## How to Collect SOC & SOH from EV BMS

## 1. Understand BMS Communication Protocols
Battery Management Systems (BMS) communicate SOC (State of Charge) and SOH (State of Health) data using industry-standard protocols:

- **CAN Bus (Controller Area Network)** – Most EVs use this.
- **Modbus (RS-485)** – Used in some industrial applications.
- **OBD-II (On-Board Diagnostics)** – Useful for accessing battery data from commercial EVs.
- **Ethernet/IP or Wi-Fi** – Found in advanced BMS setups.

### Tools to interact with these protocols:
- **CAN Bus:** Use a CAN-to-USB adapter (e.g., Peak PCAN-USB, Seeed CAN-BUS Shield).
- **OBD-II:** Use an OBD-II scanner (e.g., ELM327, Carloop).
- **Modbus:** Use a USB-to-RS485 adapter.

---

## 2. Define Software Architecture
Your software should include:
- **Data Acquisition Layer** – Reads SOC and SOH from the BMS.
- **Processing Layer** – Filters, processes, and normalizes data.
- **Storage Layer** – Saves data for analysis (e.g., database, cloud storage).
- **Visualization & API Layer** – Displays battery health metrics via GUI or API.

---

## 3. Develop Software to Read Data from BMS

### (a) Reading from CAN Bus using Python
```python
import can

bus = can.interface.Bus(channel='can0', bustype='socketcan')  # Use appropriate interface

while True:
    message = bus.recv()  # Receive messages from BMS
    print(f"Message: {message.arbitration_id}, Data: {message.data}")
```
- SOC and SOH are usually in **specific CAN message IDs**.
- The **BMS documentation** provides these IDs.

### (b) Reading SOC & SOH from OBD-II (ELM327 Adapter)
```python
import obd

connection = obd.OBD()  # Connect to OBD-II

cmd_soc = obd.commands.REMAINING_FUEL  # SOC equivalent for some EVs
cmd_soh = obd.commands.BATTERY_HEALTH  # May not be available on all EVs

soc = connection.query(cmd_soc)
soh = connection.query(cmd_soh)

print(f"SOC: {soc.value}, SOH: {soh.value}")
```
- OBD-II might not expose SOH directly; **manufacturer-specific PIDs** are needed.

---

## 4. Store & Analyze Data
- **Database:** PostgreSQL, InfluxDB for time-series data.
- **Cloud Storage:** AWS S3, Firebase, or local CSV logs.

```python
import sqlite3

conn = sqlite3.connect("bms_data.db")
cursor = conn.cursor()

cursor.execute("CREATE TABLE IF NOT EXISTS battery_data (timestamp DATETIME, soc REAL, soh REAL)")
cursor.execute("INSERT INTO battery_data VALUES (CURRENT_TIMESTAMP, ?, ?)", (soc.value, soh.value))

conn.commit()
conn.close()
```

---

## 5. Visualizing Data
- Use **Flask/Django** to create an API for real-time monitoring.
- Use **Grafana/Matplotlib** for visualization.

### Creating an API for SOC & SOH
```python
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/battery_status', methods=['GET'])
def battery_status():
    return jsonify({'SOC': soc.value, 'SOH': soh.value})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```
Now, you can get battery status by visiting:
```
http://localhost:5000/battery_status
```

---

## 6. Implement Security
- **Secure CAN Bus** to prevent tampering.
- **Encrypt data transmission** when sending to cloud.
- **Use authentication** for API access.

---

## Next Steps
- Identify your **BMS model & supported protocol**.
- Collect **BMS documentation** for CAN IDs.
- Set up **hardware (CAN adapter, OBD-II scanner, etc.)**.
- Start building the **software stack** (data acquisition → storage → visualization).

---

## Contact
- **EV.Engineer**: Sudarshana Karkala 
- **Email**: carsoftwaresystems@gmail.com
- **Website**: https://carsoftwaresystems.com
- **More Details**: https://carsoftwaresystems.com/EVE&D/AIPoweredEVBatteryFirePreventionSystem.pdf

