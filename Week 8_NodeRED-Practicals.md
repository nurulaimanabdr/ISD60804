# IoT and M2M Node-RED Lab Practicals

This repository contains two step-by-step Node-RED practicals designed to apply theoretical concepts from the **Week 8 IoT Platforms Design Methodology.pdf** and **Week 6 IoT and M2M Difference between IoT and M2M.pdf** modules.

> **Prerequisite:** Ensure you have the `node-red-dashboard` palette installed in your Node-RED environment before beginning these practicals.

---

## 🛠️ Practical 1: IoT Design Methodology (Smart Home Automation)

**Objective:** Build a Node-RED flow that simulates a home automation system with "Auto" and "Manual" modes to control a light based on a simulated Light Dependent Resistor (LDR) sensor. This practical follows the step-by-step design approach, from device integration to application development.

### Phase 1: Simulating Sensors and Controls (Device Integration)

1. Drag a `ui_slider` (Dashboard) node to the workspace.
2. Double-click to configure it:
* **Name:** `Simulated LDR`
* **Min/Max:** `0` to `1000`
* **Topic:** `ldr`


3. Drag a `ui_switch` node to the canvas:
* **Name:** `Auto/Manual Mode`
* **Topic:** `mode`
* **Payload:** `boolean` (True = Auto, False = Manual)


4. Drag a second `ui_switch` node:
* **Name:** `Manual Light Control`
* **Topic:** `manual`



### Phase 2: Core Logic (Process & Service Specifications)

1. Add a `function` node and name it `Controller Logic`.
2. Connect the output of your slider and both switches to the input of this `function` node.
3. Double-click the `function` node and paste the following JavaScript:

```javascript
// Store values in context
let context = flow.get('homeState') || {ldr: 500, auto: true, manual: false};

if (msg.topic === 'ldr') context.ldr = msg.payload;
if (msg.topic === 'mode') context.auto = msg.payload;
if (msg.topic === 'manual') context.manual = msg.payload;

flow.set('homeState', context);

let lightState = "OFF";

// Check process specifications
if (context.auto) {
    lightState = (context.ldr < 300) ? "ON" : "OFF";
} else {
    lightState = context.manual ? "ON" : "OFF";
}

return { payload: lightState };

```

### Phase 3: Dashboard Visualization (Application Development)

1. Drag a `ui_text` node to the canvas.
2. Name it `Light Status` and set the Value Format to `{{msg.payload}}`.
3. Connect the output of the `Controller Logic` function node to the input of the `Light Status` text node.
4. Click **Deploy**.
5. Navigate to `http://localhost:1880/ui` to test your Smart Home Dashboard.

---

## 🌐 Practical 2: M2M Gateway Protocol Translation & IoT Cloud

**Objective:** Use Node-RED as an M2M Gateway to ingest raw, non-IP data, translate it via a proxy into a structured format, and transmit it over an IP-based IoT protocol (MQTT).

### Phase 1: Simulating the M2M Area Network

This represents a local machine sending raw, proprietary data.

1. Add an `inject` node.
2. Double-click to configure:
* **Name:** `Simulate M2M Node`
* **msg.payload (string):** `TEMP:28|HUM:60`
* Check **Inject once after 0.1 seconds**.
* Set **Repeat** to `interval` every `5` seconds.



### Phase 2: Building the M2M Gateway (Protocol Translation)

This acts as the proxy translating a native protocol to an IP-ready format.

1. Add a `function` node and connect it to the `inject` node.
2. Name it `M2M Gateway Translation`.
3. Paste the following JavaScript code to parse and structure the data:

```javascript
// Parse the non-IP proprietary string format
let raw = msg.payload.split('|');
let tempRaw = raw[0].split(':')[1];
let humRaw = raw[1].split(':')[1];

// Translate it into a standard JSON object for IoT
let iotData = {
    temperature: parseFloat(tempRaw),
    humidity: parseFloat(humRaw)
};

// Set the new JSON object as the payload
msg.payload = iotData;
return msg;

```

### Phase 3: The IoT Network Layer (MQTT Publish)

This sends the translated data to the cloud using an IP-based protocol.

1. Add an `mqtt out` node and wire it to the `function` node output.
2. Configure the MQTT node:
* Click the pencil icon next to **Server** and add `broker.hivemq.com` (Port `1883`).
* **Topic:** `taylors/iot/module/sensorData` (or your own unique topic).
* **Name:** `Publish to IoT Cloud`



### Phase 4: The IoT Cloud Application (Dashboard)

This simulates the cloud application subscribing to and visualizing the data.

1. Drop an `mqtt in` node on the canvas (unconnected to the above flow).
2. Configure it to the same server (`broker.hivemq.com`) and exact same **Topic** (`taylors/iot/module/sensorData`). Set Output to `a parsed JSON object`. Name it `Cloud Subscriber`.
3. Add a new `function` node, connect it to the `mqtt in` node, name it `Extract Temp`, and paste:

```javascript
msg.payload = msg.payload.temperature;
return msg;

```

4. Add a `ui_chart` node. Wire it to the `Extract Temp` node.
5. Configure the chart:
* Create a new UI Group named `IoT Analytics`.
* **Label:** `Live Temperature (°C)`
* **Y-axis Min:** `0`, **Max:** `50`.



### Phase 5: Deploy and Test

1. Click the red **Deploy** button.
2. Navigate to `http://localhost:1880/ui`. You will see a live chart updating with the temperature data successfully translated and transmitted over MQTT.
