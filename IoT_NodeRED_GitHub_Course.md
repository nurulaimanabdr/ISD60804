# 🌐 Interaction Design for IoT: A Node-RED Practical Guide (Simulated)

Welcome to the Interaction Design (IxD) module for the Internet of Things (IoT). This repository contains a 4-week, step-by-step practical guide designed specifically for designers. 

We will use **Node-RED**, a visual programming tool, to learn how data flows, how systems make decisions, and how to design interfaces for smart environments—**all without needing physical hardware**.

---

## 🛠️ Prerequisites & Setup (Before Week 1)
To keep things accessible, we are using cloud-hosted Node-RED. 
1. Go to [FlowFuse](https://flowfuse.com/) (or your university's provided Node-RED link).
2. Create a free account and start your Node-RED instance.
3. **Install the UI Palette:** - Click the hamburger menu (top right) `≡` -> `Manage palette`.
   - Go to the `Install` tab.
   - Search for `node-red-dashboard` and click install.

---

## 📅 Week 1: The Basics of Data Flow & "Hello World"
**Learning Outcome:** Understand the core concept of IoT—Triggers (Inputs) and Responses (Outputs), and build your first user interface.

### Part A: Hello World (Behind the Scenes)
1. **The Input:** Drag an `Inject` node from the left menu onto the canvas. 
2. **Configure:** Double-click the `Inject` node. Change the payload from "timestamp" to "string" (text). Type: `Hello, World!`. Click **Done**.
3. **The Output:** Drag a `Debug` node onto the canvas.
4. **Wire them up:** Click and drag a wire from the right port of the `Inject` node to the left port of the `Debug` node.
5. **Run it:** Click the red **Deploy** button (top right).
6. **Test it:** - Open the Debug panel (the little bug 🐞 icon on the far right).
   - Click the small square button on the left side of your `Inject` node.
   - You should see "Hello, World!" appear in the debug panel.

> **💡 Troubleshooting:** Didn't work? Make sure you clicked Deploy, ensure the wire is connected, and check that the Debug node isn't muted (the square on its right side should stick out).

### Part B: Your First User Interface
*Why this matters for IxD: Users don't look at debug panels; they look at interfaces.*
1. Drag a `Text` node (from the Dashboard section at the bottom of the left menu).
2. Wire your `Inject` node into this new `Text` node.
3. Double-click the `Text` node. 
   - Next to "Group", click the pencil icon to create a new UI group (Name it "My Smart Home"). Click Add.
   - Next to "Tab", click the pencil icon to create a new Tab (Name it "Home"). Click Add.
   - Set the Label to "System Status". Click Done.
4. **Deploy.**
5. **View the UI:** Open a new browser tab and add `/ui` to the end of your Node-RED URL. Click your Inject node again, and watch the webpage update instantly!

---

## 📅 Week 2: Designing with Invisible Data (APIs)
**Learning Outcome:** Learn how to use public data as a design material to make systems "smart" without asking the user for input.

### Practical: The Live Weather Dashboard
1. **The Trigger:** Drag an `Inject` node. Double-click it and set it to repeat at an interval of every `10 seconds`.
2. **The Sensor (API):** Drag an `HTTP Request` node. Wire the Inject node to it.
   - Double-click it. Set the Method to `GET`.
   - Paste this URL: `https://api.open-meteo.com/v1/forecast?latitude=51.51&longitude=-0.13&current_weather=true` (This gets London's weather).
   - Set "Return" to **a parsed JSON object**. Click Done.
3. **The Filter:** We only want the temperature, not all the other data.
   - Drag a `Change` node. Wire the HTTP Request to it.
   - Double-click it. Set the rule to: `Set msg.payload` to `msg.payload.current_weather.temperature`.
4. **The UI Design:** - Drag a `Gauge` node (Dashboard section). Wire the Change node to it.
   - Double-click the Gauge. Assign it to your UI Group. Set Min to `0` and Max to `40`. Change the label to "London Temperature".
5. **Deploy & View:** Go to your `/ui` page. You now have a live, updating weather widget. Try altering the gauge colors (e.g., blue for cold, red for hot) to improve the user's quick comprehension.

---

## 📅 Week 3: Tangible Interfaces & Emulating Hardware
**Learning Outcome:** Build two-way interactions. Learn affordances—giving users control and providing immediate feedback.

### Practical: The Smart Light Controller
1. **The User Input:** Drag a `Switch` node (from the Dashboard palette, *not* the logic palette). 
   - Double-click it. Assign it to your UI group. Set the label to "Living Room Light". 
   - Ensure the "On Payload" is a boolean `true` and "Off Payload" is a boolean `false`.
2. **The Logic Loop:** We need the system to react based on the user's switch.
   - Drag a `Switch` node (from the core Logic palette). Wire your UI Switch to it.
   - Create two rules (click the `+` button at the bottom):
     - `== true` (Top output)
     - `== false` (Bottom output)
3. **The Output (Simulated Feedback):** - Add two `Change` nodes. 
   - Wire the top output of the Logic Switch to the first Change node. Set its payload to `Light is ON 💡`.
   - Wire the bottom output to the second Change node. Set its payload to `Light is OFF 🌑`.
4. **The UI Display:**
   - Wire both Change nodes into a new Dashboard `Text` node. Label it "Light Status".
5. **Deploy & View:** Go to your `/ui` page. Toggle the switch. Notice how the status text instantly updates. *Discussion point: Is text the best feedback, or would a color change on the screen be better?*

---

## 📅 Week 4: Calm Technology & Automated Notifications
**Learning Outcome:** Understand thresholds and proactive system design. When should a system interrupt a user?

### Practical: The Plant Monitoring Alert System
We will combine Week 2 (Data) and Week 3 (Logic) to create an alert. Let's pretend our weather data is actually a soil moisture sensor.
1. **Get the Data:** Copy your Inject -> HTTP Request -> Change node sequence from Week 2 (this is your "sensor").
2. **Set a Threshold:** Drag a `Switch` (Logic) node. 
   - Rule 1: `<= 10` (If moisture/temp drops to 10 or below).
3. **Format the Alert:** Drag a `Change` node. Wire it to the Logic Switch.
   - Set payload to: `🪴 Warning: Your plant needs water immediately!`
4. **The UI Notification:** Drag a `Notification` node (Dashboard palette).
   - Wire the Change node into it.
   - Double-click it. Change the layout to "Top Right". Set the color to Red.
5. **Deploy:** Now, whenever the simulated sensor drops below the threshold, the user gets a toast notification on their screen.

---

## 🎓 Week 5: Mini-Project Assessment
**The Brief:** Design your own "Smart Dashboard" using the skills learned in Weeks 1-4.
* **Requirements:** 1. Pull data from at least one public API (e.g., transit data, crypto prices, air quality).
  2. Include at least one user control (button, slider, text input).
  3. Include an automated logic rule (e.g., "If air quality is bad, show a red warning").
* **Focus:** As IxD students, you will be graded on the **layout, color choices, clear labeling, and user experience** of your `/ui` dashboard, not just the logic!

---
