1. Data (constantly shared between the trucks to keep them synchronized):

truck_speed & truck_acceleration : So the follower trucks can match the lead truck's pace

gps_position (Coordinates): To synchronize the path they are taking

following_distance : Calculated via Radar, the real-time gap measured between the trucks

2. Signals (active commands used by the autonomous driving technology to manage the follower trucks):

Steering & Direction Control Signals : Controls left to right motion to keep the follower trucks centered in the lane

Acceleration Signal: Tells the engine computer to increase/decrease throttle percentage to match the lead vehicle.   

Braking/Deceleration Signal : Command sent over the V2V wireless link to apply brakes simultaneously 

Safety Signals: If the lead truck hits the brakes or radar detects an obstacle, an Emergency Braking Demand Signal is broadcast instantly over the wireless link

3. Events (State-changing triggers):

evt_request_coupling: The driver initiates the platoon formation.

evt_coupling_accept: The follower truck acknowledges and syncs.

evt_request_decouple: An event fired to break the platoon (e.g., allowing other road users to cross between the vehicles) 

evt_emergency: An emergency event if a truck's sensors detect an unexpected object in the road 

4. Protocol: 

Continuous Telemetry: Trucks constantly share truck_speed, truck_acceleration, and gps_position to synchronize their route and match the lead truck's pace. The following_distance is continuously calculated via radar to measure the real-time gap between vehicles.

Active Control Directives: The autonomous technology manages follower trucks by transmitting Steering & Direction Control Signals for lateral lane centering and an Acceleration Signal to adjust engine throttle percentage. Synchronized stopping is managed via a Braking/Deceleration Signal sent over the V2V link.

Safety Overrides: If the lead truck brakes or radar detects an obstacle, Safety Signals instantly broadcast an Emergency Braking Demand Signal over the wireless link. 

5. State Machine: 

    ┌──────────────────┐   JOIN_REQ    ┌──────────────────┐  DECOUPLE_REQ  ┌──────────────────┐
    │   STANDALONE     │──────────────►│    COUPLING      │───────────────►│   DE-COUPLING    │
    └──────────────────┘               └──────────────────┘                └──────────────────┘
              ▲                                 │                                    │
              │                                 ▼                                    │
              │                        ┌──────────────────┐                          │
              └────────────────────────│  SYNCHRONIZED    │◄─────────────────────────┘
                   EMERGENCY_BRAKE     │    PLATOONING    │     Re-coupling Complete
                                       └──────────────────┘

-STANDALONE: The truck operates independently until a driver initiates platoon formation by triggering evt_request_coupling.   

-COUPLING: The trailing truck processes the join request and begins synchronizing speed and position. The state transitions when evt_coupling_accept is fired, indicating the follower truck acknowledges and syncs.  

-PLATOONING: The core operational state where truck_speed, gps_position, and steering/acceleration signals are continuously exchanged to maintain formation.        
    Transition A: If other road users need to cross between vehicles, evt_request_decouple is fired to break the platoon.  
    Transition B: If sensors detect an unexpected object in the road, evt_emergency is triggered.  

-DECOUPLING: Triggered by evt_request_decouple, the automated system adjusts the Braking/Deceleration Signal to increase the following_distance and safely break the formation.   

-EMERGENCY: Triggered by evt_emergency, the system instantly broadcasts Safety Signals to halt the platoon and avoid collisions.