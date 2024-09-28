# System design roud with coding 

To design and build a system that can make the Amazon campus more energy efficient by automatically turning off lights in conference rooms when they are unoccupied, we can follow a systematic approach to create a class/component level design and explain the data flow through the system. Here’s how to approach this:

### High-Level Design

1. **Sensors**:
    - **Motion Sensors**: Installed in each conference room to detect occupancy.
    - **Light Sensors**: Detect the status of lights (on/off).

2. **Devices**:
    - **Control Units**: Interface with the sensors and control the lights.
    - **Communication Module**: Handles communication between the control units and the central server.

3. **Central Server**:
    - **API Gateway**: Receives data from the control units and provides endpoints for management and monitoring.
    - **Data Processing Service**: Processes incoming data to determine the occupancy status.
    - **Database**: Stores historical data for occupancy and light status.
    - **Notification Service**: Sends alerts for maintenance or anomalies.
    - **Scheduler**: Manages the rules for when lights should be turned off based on occupancy data.

4. **User Interface**:
    - **Admin Dashboard**: Allows facility managers to monitor room statuses, configure rules, and view reports.

### Data Flow

1. **Sensor Data Collection**:
    - Motion sensors and light sensors collect data continuously.
    - The control units aggregate sensor data and periodically send it to the central server via the communication module.

2. **Data Processing**:
    - The API Gateway receives data and forwards it to the Data Processing Service.
    - The Data Processing Service analyzes the data to determine if a room is occupied or unoccupied.

3. **Action Trigger**:
    - Based on the processed data, if a room is determined to be unoccupied for a predefined period, the Scheduler triggers an action to turn off the lights via the control unit.

4. **Storage and Monitoring**:
    - The Database stores the sensor data and the actions taken.
    - The Admin Dashboard provides real-time monitoring and historical reports to the facility managers.

### Class/Component Level Design

```plaintext
[MotionSensor]--(data)-->[ControlUnit]--(send)-->[API Gateway]--(forward)-->[DataProcessingService]--(store)-->[Database]
                                                [LightSensor]--(data)-->[ControlUnit]--(send)-->[API Gateway]--(forward)-->[DataProcessingService]--(store)-->[Database]
[Scheduler]--(trigger)-->[ControlUnit]--(action)-->[Lights]

[AdminDashboard]--(configure/view)-->[API Gateway]
[NotificationService]--(notify)-->[AdminDashboard]
```

### Components and Responsibilities

1. **MotionSensor**:
    - **Methods**: `detectMotion()`
    - **Attributes**: `motionStatus`

2. **LightSensor**:
    - **Methods**: `detectLightStatus()`
    - **Attributes**: `lightStatus`

3. **ControlUnit**:
    - **Methods**: `collectData()`, `sendData()`, `receiveCommand()`
    - **Attributes**: `sensorData`, `communicationModule`

4. **APIGateway**:
    - **Methods**: `receiveData()`, `forwardData()`, `receiveConfig()`, `sendConfig()`
    - **Attributes**: `endpoints`

5. **DataProcessingService**:
    - **Methods**: `processData()`, `determineOccupancy()`, `storeData()`
    - **Attributes**: `rawData`, `processedData`

6. **Scheduler**:
    - **Methods**: `scheduleCheck()`, `triggerAction()`
    - **Attributes**: `rules`, `actions`

7. **Database**:
    - **Methods**: `storeData()`, `retrieveData()`
    - **Attributes**: `occupancyData`, `lightStatusData`

8. **AdminDashboard**:
    - **Methods**: `displayStatus()`, `configureRules()`, `viewReports()`
    - **Attributes**: `statusData`, `configuration`

9. **NotificationService**:
    - **Methods**: `sendAlert()`
    - **Attributes**: `alerts`

### Example UML Class Diagram

```plaintext
+------------------+     +------------------+     +------------------+     +------------------+
|   MotionSensor   |     |   LightSensor    |     |   ControlUnit    |     |    APIGateway    |
+------------------+     +------------------+     +------------------+     +------------------+
| - motionStatus   |     | - lightStatus    |     | - sensorData     |     | - endpoints      |
| + detectMotion() |     | + detectLight()  |     | - commModule     |     | + receiveData()  |
+------------------+     +------------------+     | + collectData()  |     | + forwardData()  |
                                                   | + sendData()    |     | + receiveConfig()|
                                                   | + receiveCmd()  |     | + sendConfig()   |
                                                   +------------------+     +------------------+
      ^                      ^                      ^
      |                      |                      |
      |                      |                      |
      +----------------------+----------------------+
                             |
+------------------+         |        +------------------+     +------------------+     +------------------+
| DataProcessing   |<--------+        |     Scheduler    |     |     Database     |     | AdminDashboard   |
+------------------+                  +------------------+     +------------------+     +------------------+
| - rawData        |                  | - rules          |     | - occupancyData  |     | - statusData     |
| - processedData  |                  | - actions        |     | - lightStatusData|     | - configuration  |
| + processData()  |                  | + scheduleCheck()|     | + storeData()    |     | + displayStatus()|
| + determineOcc() |                  | + triggerAction()|     | + retrieveData() |     | + configureRules()|
| + storeData()    |                  +------------------+     +------------------+     | + viewReports()  |
+------------------+                                                                     +------------------+
                                                                                                  ^
                                                                                                  |
                                                                                                  |
                                                                                         +------------------+
                                                                                         |NotificationService|
                                                                                         +------------------+
                                                                                         | - alerts         |
                                                                                         | + sendAlert()    |
                                                                                         +------------------+
```

### Data Flow Explanation

1. **Sensor Data Collection**:
    - Sensors detect occupancy and light status.
    - Control units collect and aggregate this data.

2. **Data Transmission**:
    - Control units send data to the API Gateway.

3. **Data Processing**:
    - API Gateway forwards data to the Data Processing Service.
    - Data Processing Service analyzes the data and determines occupancy status.

4. **Action Based on Data**:
    - Scheduler checks occupancy status and triggers actions to control lights.
    - Control units receive commands to turn lights on or off.

5. **Storage and Monitoring**:
    - Data is stored in the database for historical analysis.
    - Admins monitor and configure the system through the Admin Dashboard.
    - Notifications are sent for anomalies or maintenance.

This design ensures efficient energy management by leveraging sensors, control units, and a central processing system, while providing real-time monitoring and configuration capabilities.

In deciding whether the system should run peer-to-peer (P2P) or be centrally designed, let's consider the specific requirements and trade-offs of each approach.

### Centrally Designed System

**Advantages:**
1. **Centralized Control and Management:** Easier to manage and update the system. All configurations and updates can be made from a central location.
2. **Simplified Data Aggregation:** Easier to collect, process, and analyze data from a single location.
3. **Security:** Centralized systems can be more easily secured and monitored.

**Disadvantages:**
1. **Single Point of Failure:** If the central server goes down, the entire system may become non-functional.
2. **Scalability Challenges:** As the number of devices grows, the central server may become a bottleneck.

### Peer-to-Peer System

**Advantages:**
1. **Fault Tolerance:** No single point of failure. If one peer fails, the others can continue to operate.
2. **Scalability:** More scalable as each peer contributes resources to the network.
3. **Decentralization:** Reduces the load on any single server, distributing processing and storage.

**Disadvantages:**
1. **Complex Management:** More complex to manage and coordinate. Each peer needs to maintain its own state and communicate with others.
2. **Data Consistency:** Ensuring data consistency across all peers can be challenging.
3. **Security:** More points of attack and potentially harder to secure.

### Recommendation for the Amazon Campus Energy Efficiency System

Given the requirements:
- **Ease of management and control:** A central system allows easier updates and management.
- **Data aggregation and processing:** Centralized data processing simplifies analysis and decision-making.
- **Reliability and fault tolerance:** Adding redundancy and failover mechanisms to a centralized system can mitigate single points of failure.
- **Security:** Easier to implement and manage security in a centralized system.

Considering these factors, a **centrally designed system** is likely more suitable for this use case. It simplifies management, security, and data processing, which are critical for maintaining and optimizing energy efficiency on a large campus like Amazon's.

### High-Level Design in Python

Here's a simplified implementation of the system:

```python
# Import necessary libraries
import time
from threading import Thread, Event
from typing import Dict

# Define a mock database (In-memory)
class MockDatabase:
    def __init__(self):
        self.data = {}

    def update_room_status(self, room_id: str, status: str):
        self.data[room_id] = {'status': status, 'timestamp': time.time()}

    def get_room_status(self, room_id: str) -> Dict:
        return self.data.get(room_id, {})

# Define the motion sensor class
class MotionSensor:
    def __init__(self, room_id: str, control_unit):
        self.room_id = room_id
        self.control_unit = control_unit

    def detect_motion(self):
        # Simulate motion detection
        self.control_unit.receive_motion_data(self.room_id, 'occupied')

# Define the control unit class
class ControlUnit:
    def __init__(self, database: MockDatabase):
        self.database = database

    def receive_motion_data(self, room_id: str, status: str):
        self.database.update_room_status(room_id, status)
        print(f"Updated room {room_id} status to {status}")

    def check_room_status(self, room_id: str):
        status = self.database.get_room_status(room_id)
        if status and status['status'] == 'occupied' and (time.time() - status['timestamp']) > 300:
            # Turn off lights if room is unoccupied for more than 5 minutes
            self.database.update_room_status(room_id, 'unoccupied')
            print(f"Room {room_id} is unoccupied. Turning off lights.")

# Define the central server class
class CentralServer:
    def __init__(self):
        self.database = MockDatabase()
        self.control_units = {}

    def register_control_unit(self, room_id: str):
        self.control_units[room_id] = ControlUnit(self.database)

    def process_data(self):
        while True:
            for room_id, control_unit in self.control_units.items():
                control_unit.check_room_status(room_id)
            time.sleep(60)

# Setup the system
central_server = CentralServer()
central_server.register_control_unit('room_101')
central_server.register_control_unit('room_102')

# Simulate motion detection
motion_sensor_101 = MotionSensor('room_101', central_server.control_units['room_101'])
motion_sensor_102 = MotionSensor('room_102', central_server.control_units['room_102'])

# Start central server processing thread
server_thread = Thread(target=central_server.process_data)
server_thread.start()

# Simulate motion detected in room 101
motion_sensor_101.detect_motion()
time.sleep(10)  # Wait for some time before detecting motion again
motion_sensor_102.detect_motion()

# Stop the server after some time for demonstration purposes
time.sleep(400)
server_thread.join()
```

### Explanation

- **MockDatabase**: Simulates a database storing room statuses.
- **MotionSensor**: Simulates a motion sensor in a room.
- **ControlUnit**: Receives motion data from sensors and updates the room status in the database.
- **CentralServer**: Registers control units and processes room statuses to turn off lights if a room is unoccupied for more than 5 minutes.
- **Main Script**: Sets up the system, simulates motion detection, and runs the central server in a separate thread to process data.

### Runtime Complexity
- **Data update (ControlUnit.receive_motion_data)**: O(1)
- **Data retrieval (ControlUnit.check_room_status)**: O(1)
- **Processing loop (CentralServer.process_data)**: O(n), where n is the number of rooms.

This system provides a basic structure that can be expanded with more features like advanced scheduling, user interfaces, and integration with real hardware.


Let's walk through the system step-by-step to explain what happens when a user walks into a room equipped with a motion sensor.

### Step-by-Step Process

1. **User Enters the Room**:
    - The user walks into a room equipped with a motion sensor.

2. **Motion Detection**:
    - The motion sensor in the room detects the presence of the user. Let's assume this room is identified as `room_101`.

3. **Sensor Sends Data to Control Unit**:
    - The `MotionSensor` object associated with `room_101` detects motion and calls the `detect_motion` method.
    - `MotionSensor.detect_motion()` triggers the `ControlUnit.receive_motion_data()` method, passing the room ID (`room_101`) and the status (`occupied`).

    ```python
    motion_sensor_101.detect_motion()  # Simulates motion detection
    ```

4. **Control Unit Updates Room Status**:
    - The `ControlUnit` receives the motion data and updates the status of `room_101` in the `MockDatabase` to `occupied` along with the current timestamp.

    ```python
    def receive_motion_data(self, room_id: str, status: str):
        self.database.update_room_status(room_id, status)
        print(f"Updated room {room_id} status to {status}")
    ```

    - The `MockDatabase` method `update_room_status` is called:

    ```python
    def update_room_status(self, room_id: str, status: str):
        self.data[room_id] = {'status': status, 'timestamp': time.time()}
    ```

5. **Central Server Monitoring**:
    - The central server continuously runs a loop (in a separate thread) to check the status of each room. Every 60 seconds, it calls `ControlUnit.check_room_status()` for each registered room.

    ```python
    def process_data(self):
        while True:
            for room_id, control_unit in self.control_units.items():
                control_unit.check_room_status(room_id)
            time.sleep(60)
    ```

6. **Control Unit Checks Room Status**:
    - During the next iteration of the monitoring loop, the `ControlUnit` checks the status of `room_101`.

    ```python
    def check_room_status(self, room_id: str):
        status = self.database.get_room_status(room_id)
        if status and status['status'] == 'occupied' and (time.time() - status['timestamp']) > 300:
            # Turn off lights if room is unoccupied for more than 5 minutes
            self.database.update_room_status(room_id, 'unoccupied')
            print(f"Room {room_id} is unoccupied. Turning off lights.")
    ```

7. **Status Check and Light Control**:
    - The `check_room_status` method retrieves the status of `room_101` from the database.
    - If the status is `occupied` and the current time minus the timestamp is greater than 300 seconds (5 minutes), it updates the status to `unoccupied` and sends a command to turn off the lights.
    - If the room is still occupied (i.e., a user is present and the motion sensor detects movement periodically), the status remains `occupied`, and the lights stay on.

### Summary

When a user walks into the room:

1. The motion sensor detects motion.
2. The sensor communicates this to the control unit.
3. The control unit updates the room's status in the central database to `occupied`.
4. The central server periodically checks the room's status.
5. If the room remains unoccupied for over 5 minutes, the control unit updates the status to `unoccupied` and turns off the lights.

This sequence ensures that lights are turned off automatically when the room is not in use, thereby contributing to energy efficiency.

Updating the design to include the ability for sensors to send messages directly to a central service, which then immediately updates the status and sends control messages to the lights, involves a few key changes. Here's the updated design and process:

### Updated High-Level Design

1. **Sensors**:
    - **Motion Sensors**: Installed in each conference room to detect occupancy.
    - **Light Sensors**: Detect the status of lights (on/off).

2. **Devices**:
    - **Control Units**: Interface with the sensors and control the lights.
    - **Communication Module**: Handles communication between the sensors/control units and the central server.

3. **Central Server**:
    - **API Gateway**: Receives data from the sensors and provides endpoints for management and monitoring.
    - **Data Processing Service**: Processes incoming data to determine the occupancy status and sends control messages to lights.
    - **Database**: Stores historical data for occupancy and light status.
    - **Notification Service**: Sends alerts for maintenance or anomalies.
    - **Scheduler**: Manages the rules for when lights should be turned off based on occupancy data.

4. **User Interface**:
    - **Admin Dashboard**: Allows facility managers to monitor room statuses, configure rules, and view reports.

### Updated Data Flow

1. **Sensor Data Collection and Transmission**:
    - Motion sensors and light sensors detect data and immediately send it to the central server via the communication module.

2. **Data Processing**:
    - The API Gateway receives data and forwards it to the Data Processing Service.
    - The Data Processing Service updates the occupancy status and sends control messages to the control units to turn lights on or off as needed.

3. **Action Trigger**:
    - Based on the processed data, if a room is detected to be occupied or unoccupied, the central server sends an immediate command to the control units to turn lights on or off.

4. **Storage and Monitoring**:
    - The Database stores the sensor data and the actions taken.
    - The Admin Dashboard provides real-time monitoring and historical reports to the facility managers.

### Updated Class/Component Level Design

```plaintext
[MotionSensor]--(send)-->[API Gateway]--(forward)-->[DataProcessingService]--(update/send)-->[ControlUnit]--(control)-->[Lights]
[LightSensor]--(send)-->[API Gateway]--(forward)-->[DataProcessingService]--(update)-->[Database]
[AdminDashboard]--(configure/view)-->[API Gateway]
[NotificationService]--(notify)-->[AdminDashboard]
```

### Components and Responsibilities

1. **MotionSensor**:
    - **Methods**: `detectMotion()`, `sendData()`
    - **Attributes**: `motionStatus`

2. **LightSensor**:
    - **Methods**: `detectLightStatus()`, `sendData()`
    - **Attributes**: `lightStatus`

3. **ControlUnit**:
    - **Methods**: `receiveCommand()`, `executeCommand()`
    - **Attributes**: `commands`

4. **APIGateway**:
    - **Methods**: `receiveData()`, `forwardData()`, `receiveConfig()`, `sendConfig()`
    - **Attributes**: `endpoints`

5. **DataProcessingService**:
    - **Methods**: `processData()`, `determineOccupancy()`, `sendControlMessage()`
    - **Attributes**: `rawData`, `processedData`

6. **Scheduler**:
    - **Methods**: `scheduleCheck()`, `triggerAction()`
    - **Attributes**: `rules`, `actions`

7. **Database**:
    - **Methods**: `storeData()`, `retrieveData()`
    - **Attributes**: `occupancyData`, `lightStatusData`

8. **AdminDashboard**:
    - **Methods**: `displayStatus()`, `configureRules()`, `viewReports()`
    - **Attributes**: `statusData`, `configuration`

9. **NotificationService**:
    - **Methods**: `sendAlert()`
    - **Attributes**: `alerts`

### Updated Software Implementation

Here's the updated software implementation in Python:

```python
import time
from threading import Thread, Event
from typing import Dict

class MockDatabase:
    def __init__(self):
        self.data = {}

    def update_room_status(self, room_id: str, status: str):
        self.data[room_id] = {'status': status, 'timestamp': time.time()}

    def get_room_status(self, room_id: str) -> Dict:
        return self.data.get(room_id, {})

class MotionSensor:
    def __init__(self, room_id: str, central_service):
        self.room_id = room_id
        self.central_service = central_service

    def detect_motion(self):
        self.central_service.receive_motion_data(self.room_id, 'occupied')

class ControlUnit:
    def __init__(self, room_id: str, central_service):
        self.room_id = room_id
        self.central_service = central_service

    def receive_command(self, command: str):
        if command == 'turn_off_lights':
            print(f"Lights turned off in {self.room_id}")
        elif command == 'turn_on_lights':
            print(f"Lights turned on in {self.room_id}")

class CentralService:
    def __init__(self):
        self.database = MockDatabase()
        self.control_units = {}

    def register_control_unit(self, room_id: str):
        self.control_units[room_id] = ControlUnit(room_id, self)

    def receive_motion_data(self, room_id: str, status: str):
        self.database.update_room_status(room_id, status)
        print(f"Updated room {room_id} status to {status}")
        self.send_control_message(room_id, 'turn_on_lights' if status == 'occupied' else 'turn_off_lights')

    def send_control_message(self, room_id: str, command: str):
        if room_id in self.control_units:
            self.control_units[room_id].receive_command(command)

# Setup the system
central_service = CentralService()
central_service.register_control_unit('room_101')
central_service.register_control_unit('room_102')

# Simulate motion detection
motion_sensor_101 = MotionSensor('room_101', central_service)
motion_sensor_102 = MotionSensor('room_102', central_service)

# Simulate motion detected in room 101
motion_sensor_101.detect_motion()
time.sleep(10)  # Wait for some time before detecting motion again
motion_sensor_102.detect_motion()

# Additional logic to turn off lights after a certain period of inactivity
def monitor_rooms():
    while True:
        for room_id in central_service.control_units.keys():
            status = central_service.database.get_room_status(room_id)
            if status and status['status'] == 'occupied' and (time.time() - status['timestamp']) > 300:
                central_service.receive_motion_data(room_id, 'unoccupied')
        time.sleep(60)

# Start monitoring thread
monitor_thread = Thread(target=monitor_rooms)
monitor_thread.start()

# Stop the monitoring thread after some time for demonstration purposes
time.sleep(400)
monitor_thread.join()
```

### Explanation

1. **MotionSensor**: Detects motion and sends data directly to the central service.
2. **ControlUnit**: Receives commands from the central service to control the lights.
3. **CentralService**: Receives motion data from sensors, updates the room status, and sends control messages to the control units.

### Data Flow Explanation

1. **Sensor Data Transmission**: When a motion sensor detects occupancy, it sends this information to the `CentralService`.
2. **Data Processing and Immediate Action**: The `CentralService` updates the room status in the `MockDatabase` and sends a control message to the corresponding `ControlUnit` to turn the lights on.
3. **Periodic Monitoring**: The central service continuously monitors the status of each room. If a room remains unoccupied for over 5 minutes, it sends a command to turn off the lights.

This design ensures immediate action in response to sensor data while also providing periodic checks to ensure lights are turned off if rooms remain unoccupied for an extended period.

Sure! Below is a detailed example of the Central Service API. This API will receive data from motion sensors, process the data, and send control messages to the lights. The API will be designed using Flask, a lightweight web framework for Python.

### Central Service API with Flask

1. **API Endpoints**:
    - `POST /motion`: Receives motion data from the sensors.
    - `GET /status/<room_id>`: Retrieves the status of a specific room.
    - `POST /control`: Sends control commands to the control units.

### Implementation

```python
from flask import Flask, request, jsonify
import time
from threading import Thread

app = Flask(__name__)

# Mock Database
class MockDatabase:
    def __init__(self):
        self.data = {}

    def update_room_status(self, room_id: str, status: str):
        self.data[room_id] = {'status': status, 'timestamp': time.time()}

    def get_room_status(self, room_id: str):
        return self.data.get(room_id, {})

# Control Unit
class ControlUnit:
    def __init__(self, room_id: str):
        self.room_id = room_id

    def receive_command(self, command: str):
        if command == 'turn_off_lights':
            print(f"Lights turned off in {self.room_id}")
        elif command == 'turn_on_lights':
            print(f"Lights turned on in {self.room_id}")

# Central Service
class CentralService:
    def __init__(self):
        self.database = MockDatabase()
        self.control_units = {}

    def register_control_unit(self, room_id: str):
        self.control_units[room_id] = ControlUnit(room_id)

    def receive_motion_data(self, room_id: str, status: str):
        self.database.update_room_status(room_id, status)
        self.send_control_message(room_id, 'turn_on_lights' if status == 'occupied' else 'turn_off_lights')

    def send_control_message(self, room_id: str, command: str):
        if room_id in self.control_units:
            self.control_units[room_id].receive_command(command)

    def get_room_status(self, room_id: str):
        return self.database.get_room_status(room_id)

# Initialize Central Service
central_service = CentralService()
central_service.register_control_unit('room_101')
central_service.register_control_unit('room_102')

# Flask Routes
@app.route('/motion', methods=['POST'])
def motion():
    data = request.json
    room_id = data['room_id']
    status = data['status']
    central_service.receive_motion_data(room_id, status)
    return jsonify({'message': 'Motion data received'}), 200

@app.route('/status/<room_id>', methods=['GET'])
def status(room_id):
    status = central_service.get_room_status(room_id)
    if status:
        return jsonify(status), 200
    else:
        return jsonify({'error': 'Room not found'}), 404

@app.route('/control', methods=['POST'])
def control():
    data = request.json
    room_id = data['room_id']
    command = data['command']
    central_service.send_control_message(room_id, command)
    return jsonify({'message': 'Control command sent'}), 200

# Additional logic to turn off lights after a certain period of inactivity
def monitor_rooms():
    while True:
        for room_id in central_service.control_units.keys():
            status = central_service.get_room_status(room_id)
            if status and status['status'] == 'occupied' and (time.time() - status['timestamp']) > 300:
                central_service.receive_motion_data(room_id, 'unoccupied')
        time.sleep(60)

# Start monitoring thread
monitor_thread = Thread(target=monitor_rooms)
monitor_thread.start()

if __name__ == '__main__':
    app.run(debug=True)
```

### Explanation

1. **Endpoints**:
    - `POST /motion`: Receives JSON payloads from motion sensors. The payload should contain `room_id` and `status` (e.g., `{"room_id": "room_101", "status": "occupied"}`).
    - `GET /status/<room_id>`: Retrieves the current status of the specified room.
    - `POST /control`: Receives control commands (e.g., `turn_on_lights` or `turn_off_lights`) for a specific room.

2. **Components**:
    - **MockDatabase**: Stores the status of each room.
    - **ControlUnit**: Simulates a control unit that can receive and execute commands.
    - **CentralService**: Manages the communication between sensors, database, and control units.

3. **Monitoring Thread**:
    - The `monitor_rooms` function runs in a separate thread to periodically check the status of each room and turn off lights if the room has been unoccupied for more than 5 minutes.

This API allows for real-time updates and control of the lighting system based on motion detection, ensuring energy efficiency.


To write the `ReceiveEvent` function in Python 3, we need to consider it as a part of the `CentralService` class. This function will handle incoming events, process them, and update the system accordingly. Here's how you can implement it:

### Implementation of `ReceiveEvent`

We'll assume that `ReceiveEvent` will handle different types of events, such as motion detected, light status update, etc. For this example, let's focus on the motion detection event.

```python
import time
from threading import Thread

# Mock Database
class MockDatabase:
    def __init__(self):
        self.data = {}

    def update_room_status(self, room_id: str, status: str):
        self.data[room_id] = {'status': status, 'timestamp': time.time()}

    def get_room_status(self, room_id: str):
        return self.data.get(room_id, {})

# Control Unit
class ControlUnit:
    def __init__(self, room_id: str):
        self.room_id = room_id

    def receive_command(self, command: str):
        if command == 'turn_off_lights':
            print(f"Lights turned off in {self.room_id}")
        elif command == 'turn_on_lights':
            print(f"Lights turned on in {self.room_id}")

# Central Service
class CentralService:
    def __init__(self):
        self.database = MockDatabase()
        self.control_units = {}

    def register_control_unit(self, room_id: str):
        self.control_units[room_id] = ControlUnit(room_id)

    def receive_motion_data(self, room_id: str, status: str):
        self.database.update_room_status(room_id, status)
        self.send_control_message(room_id, 'turn_on_lights' if status == 'occupied' else 'turn_off_lights')

    def send_control_message(self, room_id: str, command: str):
        if room_id in self.control_units:
            self.control_units[room_id].receive_command(command)

    def get_room_status(self, room_id: str):
        return self.database.get_room_status(room_id)

    def receive_event(self, event_type: str, room_id: str, status: str):
        if event_type == 'motion':
            self.receive_motion_data(room_id, status)
        else:
            print(f"Unknown event type: {event_type}")

# Initialize Central Service
central_service = CentralService()
central_service.register_control_unit('room_101')
central_service.register_control_unit('room_102')

# Simulate motion detection
motion_event_101 = {'event_type': 'motion', 'room_id': 'room_101', 'status': 'occupied'}
motion_event_102 = {'event_type': 'motion', 'room_id': 'room_102', 'status': 'occupied'}

# Process events
central_service.receive_event(**motion_event_101)
central_service.receive_event(**motion_event_102)

# Additional logic to turn off lights after a certain period of inactivity
def monitor_rooms():
    while True:
        for room_id in central_service.control_units.keys():
            status = central_service.get_room_status(room_id)
            if status and status['status'] == 'occupied' and (time.time() - status['timestamp']) > 300:
                central_service.receive_motion_data(room_id, 'unoccupied')
        time.sleep(60)

# Start monitoring thread
monitor_thread = Thread(target=monitor_rooms)
monitor_thread.start()

# Stop the monitoring thread after some time for demonstration purposes
time.sleep(400)
monitor_thread.join()
```

### Explanation

1. **CentralService Class**:
    - **`__init__`**: Initializes the central service with a mock database and control units.
    - **`register_control_unit`**: Registers a control unit for a specific room.
    - **`receive_motion_data`**: Handles motion data, updates the room status, and sends control messages.
    - **`send_control_message`**: Sends control messages to the control units.
    - **`get_room_status`**: Retrieves the current status of a room from the database.
    - **`receive_event`**: This is the newly implemented function that handles different types of events. For now, it supports motion events and calls `receive_motion_data` accordingly.

2. **Event Simulation**:
    - `motion_event_101` and `motion_event_102` simulate motion detection events.
    - `central_service.receive_event` processes these events.

3. **Monitoring Thread**:
    - The `monitor_rooms` function continuously checks the status of each room and turns off lights if the room has been unoccupied for more than 5 minutes.

This implementation allows the `CentralService` to handle incoming events dynamically and update the system based on the event type.

In the context of this system, the status is a key piece of information representing the current state of a room. It is used to determine whether actions should be taken, such as turning lights on or off. Here’s what the status values mean:

1. **`occupied`**: This status indicates that the room is currently occupied. When the system receives this status, it will trigger actions to ensure that the lights are turned on. This is typically sent by the motion sensor when it detects movement in the room.

2. **`unoccupied`**: This status indicates that the room is currently unoccupied. When the system receives this status, it will trigger actions to turn off the lights, provided that the room has remained unoccupied for a predefined period (e.g., 5 minutes). This status could be determined by the central service if no motion is detected for the specified duration.

### How Status is Used in the System

- **Motion Detection**: When the motion sensor detects movement, it sends a status of `occupied` to the central service. The central service updates the room status and sends a command to the control unit to turn on the lights.
  
- **Inactivity Monitoring**: The central service periodically checks the timestamp of the last `occupied` status for each room. If the room has been `occupied` but no further motion is detected for a predefined period, the service updates the room status to `unoccupied` and sends a command to the control unit to turn off the lights.

### Example with the CentralService Class

```python
import time
from threading import Thread

class MockDatabase:
    def __init__(self):
        self.data = {}

    def update_room_status(self, room_id: str, status: str):
        self.data[room_id] = {'status': status, 'timestamp': time.time()}

    def get_room_status(self, room_id: str):
        return self.data.get(room_id, {})

class ControlUnit:
    def __init__(self, room_id: str):
        self.room_id = room_id

    def receive_command(self, command: str):
        if command == 'turn_off_lights':
            print(f"Lights turned off in {self.room_id}")
        elif command == 'turn_on_lights':
            print(f"Lights turned on in {self.room_id}")

class CentralService:
    def __init__(self):
        self.database = MockDatabase()
        self.control_units = {}

    def register_control_unit(self, room_id: str):
        self.control_units[room_id] = ControlUnit(room_id)

    def receive_motion_data(self, room_id: str, status: str):
        self.database.update_room_status(room_id, status)
        self.send_control_message(room_id, 'turn_on_lights' if status == 'occupied' else 'turn_off_lights')

    def send_control_message(self, room_id: str, command: str):
        if room_id in self.control_units:
            self.control_units[room_id].receive_command(command)

    def get_room_status(self, room_id: str):
        return self.database.get_room_status(room_id)

    def receive_event(self, event_type: str, room_id: str, status: str):
        if event_type == 'motion':
            self.receive_motion_data(room_id, status)
        else:
            print(f"Unknown event type: {event_type}")

# Initialize Central Service
central_service = CentralService()
central_service.register_control_unit('room_101')
central_service.register_control_unit('room_102')

# Simulate motion detection
motion_event_101 = {'event_type': 'motion', 'room_id': 'room_101', 'status': 'occupied'}
motion_event_102 = {'event_type': 'motion', 'room_id': 'room_102', 'status': 'occupied'}

# Process events
central_service.receive_event(**motion_event_101)
central_service.receive_event(**motion_event_102)

# Additional logic to turn off lights after a certain period of inactivity
def monitor_rooms():
    while True:
        for room_id in central_service.control_units.keys():
            status = central_service.get_room_status(room_id)
            if status and status['status'] == 'occupied' and (time.time() - status['timestamp']) > 300:
                central_service.receive_motion_data(room_id, 'unoccupied')
        time.sleep(60)

# Start monitoring thread
monitor_thread = Thread(target=monitor_rooms)
monitor_thread.start()

# Stop the monitoring thread after some time for demonstration purposes
time.sleep(400)
monitor_thread.join()
```

### Explanation of Status in Context

1. **`occupied` Status**:
    - **When Detected**: Sent by the motion sensor when a user enters the room.
    - **Action**: The central service updates the room's status to `occupied` and sends a command to the control unit to turn on the lights.

2. **`unoccupied` Status**:
    - **When Detected**: Determined by the central service if no motion is detected for a predefined period (e.g., 5 minutes).
    - **Action**: The central service updates the room's status to `unoccupied` and sends a command to the control unit to turn off the lights.

By handling these statuses, the system ensures efficient energy use by turning lights on when rooms are in use and off when they are not.



# coding round
Certainly! To solve the problem of counting the number of islands in a 2D grid (where islands are represented by `1`s and water by `0`s), we can use Depth-First Search (DFS). The algorithm will visit each cell in the grid, and for every `1` it finds that hasn't been visited yet, it will initiate a DFS to mark all connected `1`s as visited. This way, each time we start a new DFS, it indicates a new island.

Here is the Python implementation with comments on every line:

```python
def count_islands(grid):
    # If the grid is empty, return 0 islands
    if not grid:
        return 0

    # Dimensions of the grid
    rows, cols = len(grid), len(grid[0])
    
    # Function to perform DFS on the grid
    def dfs(r, c):
        # If out of bounds or at a water cell, return immediately
        if r < 0 or c < 0 or r >= rows or c >= cols or grid[r][c] == '0':
            return
        # Mark the current cell as visited by setting it to '0'
        grid[r][c] = '0'
        # Visit all 4 adjacent cells (up, down, left, right)
        dfs(r - 1, c) # Up
        dfs(r + 1, c) # Down
        dfs(r, c - 1) # Left
        dfs(r, c + 1) # Right
    
    # Initialize island count
    island_count = 0
    
    # Iterate over each cell in the grid
    for r in range(rows):
        for c in range(cols):
            # If the cell is land ('1'), it's part of a new island
            if grid[r][c] == '1':
                island_count += 1 # Increment the island count
                dfs(r, c) # Perform DFS to mark all cells in this island
    
    return island_count

# Example usage
example_grid = [
    ["1", "1", "1", "1", "0"],
    ["1", "1", "0", "1", "0"],
    ["1", "1", "0", "0", "0"],
    ["0", "0", "0", "0", "0"]
]
print(count_islands(example_grid)) # Output: 1
```

### Space and Runtime Complexity:

**Time Complexity:**
- The time complexity is \(O(M \times N)\), where \(M\) is the number of rows and \(N\) is the number of columns in the grid.
- Each cell is visited once, and in the worst case, every cell could be part of an island, resulting in a full traversal of the grid.

**Space Complexity:**
- The space complexity is \(O(M \times N)\) in the worst case due to the recursion stack. In the worst case, if the grid is filled with `1`s (a single large island), the DFS stack would hold all \(M \times N\) cells.
- Additional space for the grid itself is \(O(M \times N)\), but this is not counted as extra space since it's part of the input.

This solution is efficient and optimal for this problem.


# write a solution where the original grid is immutable

When dealing with an immutable grid, we can't modify the original grid to mark visited cells. Instead, we'll use a separate data structure to track visited cells. We can use a set to keep track of visited coordinates.

Here is the Python implementation using DFS with an immutable grid:

```python
def count_islands_immutable(grid):
    # If the grid is empty, return 0 islands
    if not grid:
        return 0

    # Dimensions of the grid
    rows, cols = len(grid), len(grid[0])
    
    # Set to keep track of visited cells
    visited = set()

    # Function to perform DFS on the grid
    def dfs(r, c):
        # If out of bounds, at a water cell, or already visited, return immediately
        if (r < 0 or c < 0 or r >= rows or c >= cols or
                grid[r][c] == '0' or (r, c) in visited):
            return
        # Mark the current cell as visited
        visited.add((r, c))
        # Visit all 4 adjacent cells (up, down, left, right)
        dfs(r - 1, c) # Up
        dfs(r + 1, c) # Down
        dfs(r, c - 1) # Left
        dfs(r, c + 1) # Right
    
    # Initialize island count
    island_count = 0
    
    # Iterate over each cell in the grid
    for r in range(rows):
        for c in range(cols):
            # If the cell is land ('1') and not yet visited, it's part of a new island
            if grid[r][c] == '1' and (r, c) not in visited:
                island_count += 1 # Increment the island count
                dfs(r, c) # Perform DFS to mark all cells in this island
    
    return island_count

# Example usage
example_grid = [
    ["1", "1", "1", "1", "0"],
    ["1", "1", "0", "1", "0"],
    ["1", "1", "0", "0", "0"],
    ["0", "0", "0", "0", "0"]
]
print(count_islands_immutable(example_grid)) # Output: 1
```

### Space and Runtime Complexity:

**Time Complexity:**
- The time complexity is \(O(M \times N)\), where \(M\) is the number of rows and \(N\) is the number of columns in the grid.
- Each cell is visited once.

**Space Complexity:**
- The space complexity is \(O(M \times N)\) in the worst case due to the visited set storing up to \(M \times N\) cells.
- The recursion stack in DFS could also take up to \(O(M \times N)\) space in the worst case.

This solution ensures the original grid remains unchanged by using an additional set to track visited cells.

## System design round
## High-Level System Design for Amazon Campus Efficiency System

### Functional Requirements
1. **Motion Detection**: Detect movement in work areas using motion sensor devices.
2. **Lighting Control**: Automatically turn lights on/off based on motion detection.
3. **Real-time Monitoring**: Provide real-time monitoring of lighting and occupancy status.
4. **Historical Data Analysis**: Store and analyze historical occupancy and lighting data.
5. **Alert System**: Send alerts for any system malfunctions or unusual patterns.
6. **User Interface**: Provide a dashboard for facility managers to monitor and control the system.
7. **Integration**: Integrate with existing building management systems.

### Non-Functional Requirements
1. **Scalability**: The system should handle a large number of sensors and devices.
2. **Reliability**: High availability and fault tolerance.
3. **Security**: Ensure secure communication and data storage.
4. **Performance**: Real-time response for motion detection and lighting control.
5. **Maintainability**: Easy to maintain and update the system.
6. **Data Privacy**: Ensure privacy of the occupants' data.

### Back-of-the-Envelope Estimation

#### Bandwidth
- Each motion sensor sends data every second.
- Assume each sensor sends 1KB of data per second.
- For 10,000 sensors: 10,000 sensors * 1KB/s = 10MB/s.

#### Storage
- Each sensor's data is stored for historical analysis.
- Assume 1KB per second per sensor.
- For 10,000 sensors: 10,000 sensors * 1KB/s * 86400 seconds/day = 864GB/day.

#### Processing Needs
- Real-time processing of sensor data for lighting control.
- Historical data analysis and reporting.
- Assume medium to high computational needs, depending on the complexity of analysis.

### System Diagram in Plaintext

```
[Motion Sensors] --> [Gateway Devices] --> [Load Balancer] --> [Backend Servers]
                                                          |            |
                                                          v            v
                                                   [Message Queue]  [Transcoder]
                                                          |
                                                          v
                                                 [Lighting Control System]
                                                          |
                                                          v
                                               [Database for Historical Data]
                                                          |
                                                          v
                                                    [Analytics Engine]
                                                          |
                                                          v
                                              [Facility Manager Dashboard]
                                                          |
                                                          v
                                                  [Alert/Notification System]
                                                          |
                                                          v
                                               [Existing Building Management System]
```

### Updated System Diagram with Additional Components

# question 
```
GOAL: MAKE AMAZON CAMPUS MORE EFFICIENT. With motion sensor devices in work areas to track people, automatically turn lights off in areas where people haven't been in a while. Design a software system to interact with devices and control. design a high-level system design.

list nonfunctional and functional requirements for the service

give a back of the envelope estimation for bandwidth, storage, processing needs

give me the system diagram in plantext.

add cdn, load banancer, rate limiter, message queue, and transcoding (if and when appropriate). Update the diagram and give me the diagram in plaintext

```
 # Diagram

```
[Motion Sensors] --> [Gateway Devices] --> [Load Balancer] --> [Backend Servers]
       |                   |                 |                       |
       v                   v                 v                       v
[Rate Limiter] --> [Message Queue] --> [Transcoder] --> [CDN for Dashboard and Alerts]
                                                          |
                                                          v
                                                 [Lighting Control System]
                                                          |
                                                          v
                                                 [Database for Historical Data]
                                                          |
                                                          v
                                                   [Analytics Engine]
                                                          |
                                                          v
                                             [Facility Manager Dashboard]
                                                          |
                                                          v
                                                 [Alert/Notification System]
                                                          |
                                                          v
                                      [Existing Building Management System]
```

In this updated system:
- **Rate Limiter**: Ensures that the system isn't overwhelmed by a sudden influx of data.
- **Message Queue**: Decouples data ingestion from processing, allowing for more scalable and reliable data handling.
- **Transcoder**: Converts sensor data into a suitable format for storage and analysis.
- **CDN (Content Delivery Network)**: Speeds up the delivery of dashboard and alert content to facility managers.