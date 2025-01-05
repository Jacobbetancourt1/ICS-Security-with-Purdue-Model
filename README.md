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
</p>

*The diagram above illustrates the Purdue Model for ICS security, highlighting segmentation and enforcement boundaries to protect critical OT assets.*
## Diagram Breakdown

### 🏢 1. Purdue Level 4: Enterprise Zone
**Description**:  
The business-facing IT environment that includes systems like ERP, email, and business reporting tools. This zone interacts with OT systems indirectly via the ICS DMZ.  
- **Key principle**: Enterprise systems **must not** directly access OT devices.

**Traffic**:  
Business tools (e.g., ERP systems) query operational data (e.g., production reports) from Level 3 via the DMZ.

**Recommended Devices**:
- **Enterprise Firewall**: Enforces strict traffic rules between Level 4 and the ICS DMZ.
- **Endpoint Protection**: Prevents malware on enterprise devices.
- **Active Directory (Optional)**: For centralized authentication, replicated to OT if needed.

⚠️ **Caution**: Extending enterprise AD into OT can introduce risks, such as:
1. Shared attack surfaces between IT and OT.
2. Misconfigured access controls leading to over-permissioned OT access.

**Mitigation**:
- Use a dedicated AD instance or a read-only proxy for OT systems.
- Apply strict trust configurations and RBAC to limit access.

---

### 🛡️ 2. ICS DMZ
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

**Why These Devices Are Important**:
1. **Firewalls**:  
   - Firewalls act as the first layer of defense, filtering traffic entering or exiting the DMZ.  
   - Example: A firewall might allow an enterprise dashboard to query an OT gateway but block all unsolicited traffic into Level 3.

2. **Unidirectional Gateways**:  
   - These devices enforce a **one-way flow** of data, ensuring that no commands or threats propagate back into OT systems.  
   - Example: Alarm summaries or production metrics are sent from Level 3 to Level 4, but no Level 4 commands can affect Level 3 systems.

3. **Application Gateways**:  
   - Converts industrial protocols (e.g., OPC UA, Modbus) used by OT systems into formats readable by enterprise systems (e.g., HTTPS or JSON).  
   - Example: A gateway securely translates SCADA status data into a format accessible by business analytics tools.

---

### 💻 3. Purdue Level 3: Site-Wide Supervisory Zone
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

### 🧱 4. Minor Enforcement Boundary
**Description**:  
The minor enforcement boundary separates **Level 3 (Site-Wide Supervisory)** from **Level 2 and below (Processes and Local Control)**. Its purpose is to provide basic segmentation and restrict unnecessary communication, but it is not the primary focus for heavy security measures.

**Key Principles**:
- Keep security configurations simple and focused.
- Apply lightweight controls (e.g., ACLs) to restrict communication between levels.
- Minimize time spent here so resources can focus on major enforcement boundaries.

**Recommended Actions**:
1. **Apply Basic ACLs**:
   - Block unnecessary traffic between Level 3 and Level 2 while allowing essential communications (e.g., SCADA to PLC traffic).
   - Example ACL Rules:
     - Allow traffic from SCADA systems to specific PLCs only on required ports (e.g., TCP 502 for Modbus).
     - Deny all other traffic to Level 2 devices by default.

2. **Ensure Protocol Restrictions**:
   - Allow only OT protocols (e.g., Modbus, OPC UA) and block IT protocols like SMB or HTTP unless required for specific operations.

3. **Avoid Over-Engineering**:
   - The goal is not to create complex configurations here. A few targeted ACLs or rules are sufficient.

**Focus Area**:
- The minor enforcement boundary is not where most security resources should be spent. Instead, focus on securing **major enforcement boundaries** (e.g., DMZs) where the highest risks exist.

**Why It’s Important**:
- Provides a lightweight layer of segmentation to reduce risks of unnecessary communication or accidental disruptions.
- Saves resources and time for addressing critical security risks at major enforcement boundaries.

---

### ⚙️ 5. Process Zones (Levels 2, 1, 0)
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
   - [**NIST SP 800-82**](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-82r2.pdf): Focus on secure ICS architecture.
   - **ISA/IEC 62443**: Emphasizes segmentation and secure zones.
   - **GICSP Key Concepts**: Strong enforcement boundaries, protocol mediation, and access control.

---

## Next Steps
This design can serve as a baseline for implementing a secure ICS environment. For further insights:
1. **Simulate**: Use virtual tools to design and test ICS environments:
   - [OpenPLC](https://www.openplcproject.com/): Simulate programmable logic controllers and test control logic.
   - [SCADASim](https://github.com/cmu-sei/SCADASim): Practice monitoring and managing industrial processes.

2. **Enhance**: Build on this architecture with additional tools:
   - [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer): Design and simulate network segmentation for OT environments.
   - [GRASSMARLIN](https://github.com/nsacyber/GRASSMARLIN): Analyze and visualize OT networks for better segmentation and visibility.

3. **Document**: Provide detailed logging and monitoring configurations for real-world scenarios:
   - Log critical events (e.g., unauthorized access attempts).
   - Document traffic flows and justify rules for enforcement boundaries.

4. **Explore Standards**:
   - [NIST SP 800-82](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-82r2.pdf): A U.S.-centric guide to securing ICS environments, emphasizing practical risk management and best practices.
   - **ISA/IEC 62443**: A globally recognized set of standards focused on lifecycle security, segmentation, and stakeholder collaboration in industrial automation.

---

## 🌟 Future Improvements
- Secure vendor remote access to Level 3 systems with enhanced configurations.
- I want to learn how to simulate traffic flows within the ICS DMZ to demonstrate enforcement boundaries in action.
- Add detailed use cases for safety system isolation (Level 0).
- I want to learn how to include a broader analysis of OT network monitoring tools beyond GRASSMARLIN.

---

## 💡 Final Touch
This project reflects my ongoing commitment to mastering ICS/OT cybersecurity and contributing to secure industrial environments.
