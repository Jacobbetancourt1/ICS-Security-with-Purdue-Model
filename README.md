# ICS-Security-with-Purdue-Model
# Industrial Control Systems (ICS) Security Architecture: Purdue Model Design

## Overview
This project showcases an **ICS security architecture** built around the Purdue Enterprise Reference Architecture. The design ensures **segmentation, enforcement boundaries, and protection of critical OT assets**, reducing the attack surface and securing essential industrial processes. It incorporates best practices from standards like NIST SP 800-82 and ISA/IEC 62443 while reflecting core principles from the **GICSP (Global Industrial Cybersecurity Professional)** course.

---

## Purpose of the Diagram
This architecture focuses on:
1. **Segmentation**: Breaking the network into secure zones to limit lateral movement and confine potential breaches.
2. **Enforcement Boundaries**: Implementing clear controls to regulate traffic between zones (e.g., DMZs).
3. **Protection of Critical Assets**: Ensuring that the most sensitive systems—like controllers, sensors, and safety systems—are isolated and only accessible through secure, monitored channels.

---
<p align="center">
<br/>
<img src="https://imgur.com/36cH4eD.png" height="80%" width="80%" alt="GIT SSH ACCESS"/>

## Diagram Breakdown

### 1. Purdue Level 4: Enterprise Zone
**Description**:  
The business-facing IT environment that includes systems like ERP, email, and business reporting tools. This zone interacts with OT systems indirectly via the ICS DMZ.  
- **Key principle**: Enterprise systems **must not** directly access OT devices.

**Traffic**:  
Business tools (e.g., ERP systems) query operational data (e.g., production reports) from Level 3 via the DMZ.

**Recommended Devices**:
- **Enterprise Firewall**: Enforces strict traffic rules between Level 4 and the ICS DMZ.
- **Endpoint Protection**: Prevents malware on enterprise devices.
- **Active Directory (Optional)**: For centralized authentication, replicated to OT if needed.

---

### 2. ICS DMZ
**Description**:  
A critical buffer between enterprise IT (Level 4) and operational OT (Level 3). The DMZ mediates traffic and enforces **segmentation** while ensuring secure communication.

**Divided into Specialized Zones**:
- **Level 4 to Level 3 DMZ**: For enterprise access to OT data.
- **Level 3 to Level 4 DMZ**: For OT systems to securely push reports or alerts to enterprise systems.
- **Cloud Access DMZ**: For secure interactions with external cloud platforms (e.g., monitoring or analytics).
- **Remote Access DMZ**: For secure vendor and administrator access to OT systems.

**Traffic**:  
Allows only necessary traffic between Level 4 and Level 3, such as:
- Querying data historians (Level 3).
- Sending operational alerts to enterprise tools.

**Recommended Devices**:
- **Firewalls**: At each DMZ boundary to control and filter traffic.
- **Unidirectional Gateways**: Ensures one-way data flow from OT to enterprise.
- **Application Gateways**: Handles protocol translation (e.g., OPC UA, MQTT).

---

### 3. Purdue Level 3: Site-Wide Supervisory Zone
**Description**:  
Hosts systems critical for monitoring and managing OT processes:
- SCADA: Supervises the entire process.
- HMIs: Provides operators with interfaces to monitor/control processes.
- Historians: Collects and stores operational data.
- Cybersecurity Tools: Monitors traffic and detects threats (e.g., SIEM, IDS/IPS).
- Testing/Staging: Sandboxes for validating updates and patches.
- Jump Hosts: Controlled access points for vendors or admin roles.

**Traffic**:  
Communicates with:
- DMZs for secure data exchange.
- Level 2 systems for process control.

**Recommended Devices**:
- **Firewalls or Layer-3 Switches**: For role-based segmentation.
- **SIEM and IDS/IPS**: For visibility and anomaly detection.
- **Patch Management Server**: Keeps OT systems updated securely.

---

### 4. Minor Enforcement Boundary
**Description**:  
A smaller barrier between Level 3 (site-wide systems) and the lower process levels (Level 2 and below).  
- **Focus**: Restrict unnecessary communication and reduce risks of lateral movement.

**Recommended Devices**:
- **Routers or Switches with ACLs**: Enforces segmentation.
- **Firewalls**: For environments with stricter requirements.

---

### 5. Process Zones (Levels 2, 1, 0)
**Description**:  
- **Level 2**: Local Supervisory Systems that monitor specific processes (e.g., a production line).
- **Level 1**: Local Controllers (PLCs) that execute logic and control devices.
- **Level 0**: Field Devices (sensors and actuators) that interact directly with the physical environment.  
- Includes safety systems with enforced isolation for emergencies.

**Traffic**:  
Communication is mostly **vertical** (e.g., SCADA to PLCs), with minimal lateral movement between processes.

**Recommended Devices**:
- **VLANs**: To separate processes (e.g., Lines A, B, C, D).
- **Safety System Isolation**: Air gaps or enforced segmentation for critical systems.
- **Firewalls/ACLs**: For additional control.

---

## Why This Design Works
This architecture adheres to several core principles:
1. **Defense-in-Depth**: Multiple layers of security (firewalls, DMZs, VLANs) reduce risks.
2. **Minimal Access**: Traffic flows are tightly controlled, allowing only necessary communication.
3. **Isolation of Critical Assets**: Ensures the safety and integrity of controllers and field devices.
4. **Alignment with Standards**:
   - **NIST SP 800-82**: Focus on secure ICS architecture.
   - **ISA/IEC 62443**: Emphasizes segmentation and secure zones.
   - **GICSP Key Concepts**: Strong enforcement boundaries, protocol mediation, and access control.

---

## Next Steps
This design can serve as a baseline for implementing a secure ICS environment. For further insights:
1. **Simulate**: Use virtual tools like OpenPLC and SCADA simulators to demonstrate traffic flows.
2. **Enhance**: Add specific tools or configurations tailored to your environment.
3. **Document**: Provide detailed logging and monitoring for real-world scenarios.

---

