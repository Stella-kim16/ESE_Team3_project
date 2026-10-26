# Task 1: Truck Platooning Specification

## 1. Data (Telemetry Shared via V2V)
* **`truck_speed` & `truck_acceleration`**: Shared continuously so follower trucks can match the lead truck's pace.
* **`gps_position` (Coordinates)**: Broadcast to synchronize the route and path.
* **`following_distance`**: Measured continuously via Radar to track the real-time gap between vehicles[cite: 5].

---

## 2. Signals (Active Control Directives)
* **Steering & Direction Control Signals**: Manages lateral motion to keep follower trucks centered in the lane.
* **Acceleration Signal**: Controls engine throttle percentage to match the lead vehicle.
* **Braking / Deceleration Signal**: Transmitted over the V2V link to apply brakes simultaneously[cite: 5].
* **Safety Signals**: Instant broadcast of an **Emergency Braking Demand Signal** if the lead truck brakes hard or radar detects an obstacle[cite: 5].

---

## 3. Events (State-Changing Triggers)
* **`evt_request_coupling`**: Triggered when a driver or system initiates platoon formation.
* **`evt_coupling_accept`**: Fired when the follower truck acknowledges connection and synchronizes.
* **`evt_request_decouple`**: Triggered to break formation (e.g., allowing external vehicles to cross between trucks).
* **`evt_emergency`**: Fired when sensors detect an immediate hazard or obstacle.

---

## 4. Protocol Specification
* **Continuous Telemetry**: Trucks continuously stream `truck_speed`, `truck_acceleration`, and `gps_position` over the V2V link to synchronize route and pace. Radar continuously monitors `following_distance` to measure the physical gap[cite: 5].
* **Active Control Directives**: Follower trucks act on lateral steering signals for lane centering and acceleration/deceleration signals to match speed and braking[cite: 5].
* **Safety Overrides**: Emergency braking demands bypass standard loops and instantly broadcast over the low-latency V2V link[cite: 5].

---

## 5. State Machine
* **STANDALONE**: The truck operates independently until platoon formation is initiated via `evt_request_coupling`[cite: 3, 4].
* **COUPLING**: The trailing truck processes the join request and synchronizes speed and position[cite: 3, 4]. The state transitions to platooning when `evt_coupling_accept` is received[cite: 3, 4].
* **PLATOONING**: The core operational state where telemetry and control signals are continuously exchanged[cite: 3, 4].
  * **Transition A**: If external traffic needs to pass, `evt_request_decouple` transitions the system to `DECOUPLING`[cite: 3, 4].
  * **Transition B**: If an obstacle is detected, `evt_emergency` triggers an emergency response[cite: 3, 4].
* **DECOUPLING**: Triggered by `evt_request_decouple`, the automated system adjusts braking signals to increase `following_distance` and open a safe gap[cite: 3, 4].
* **EMERGENCY**: Triggered by `evt_emergency`, safety signals immediately command emergency braking to bring the platoon to a safe stop[cite: 3, 4].