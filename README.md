# Wazuh-Suricata-IDS-Integration
A hands-on lab demonstrating the integration of Suricata IDS with a Wazuh SIEM manager to capture and analyze network telemetry from a Kali Linux attack machine.

# SIEM Integration: Suricata IDS and Wazuh Manager

This project demonstrates a technical integration between **Suricata Intrusion Detection System (IDS)** and **Wazuh SIEM**. The lab simulates a real-world Security Operations Center (SOC) scenario where network-based telemetry is ingested into a centralized manager for analysis.

---

## 🛠️ Environment Overview
* **SIEM Manager**: Wazuh OVA (Virtual Machine)
* **IDS Engine**: Suricata 7.0.2
* **Attacker Machine**: Kali Linux (IP: 192.168.1.5)
* **Target Machine**: Windows 11 (IP: 192.168.1.10)

---

## 🚀 Phase 1: Suricata Installation & Environment Setup
The initial phase involved deploying Suricata 7.0.2 on the Wazuh server. This allows the IDS to act as a local sensor monitoring the network interface for incoming traffic.

<img width="1280" height="800" alt="suricata_install" src="https://github.com/user-attachments/assets/779654bb-52a6-409c-9632-50e2b527dfea" />


---

## ⚙️ Phase 2: Wazuh Manager Configuration
To bridge the gap between Suricata's logs and the Wazuh engine, the `ossec.conf` file was modified to enable full archive logging. 

**Key Configurations:**
* Enabled `<logall>` and `<logall_json>` to "yes" to capture every event regardless of alert status.
* Adjusted `<log_alert_level>` to 1 to ensure a broad range of telemetry was processed by the manager.

![config_ossec](https://github.com/user-attachments/assets/acf23de8-c541-40e8-a59b-ac4652b1c4aa)


---

## 🛰️ Phase 3: Backend Data Pipeline (Filebeat)
After configuring the manager, the Filebeat module was initialized. This ensures that the communication channel between the Wazuh Manager and the Indexer (the database) is correctly mapped to receive Suricata's EVE JSON logs.

![filebeat_setup](https://github.com/user-attachments/assets/8896c879-c197-43bc-a1f5-8b374c8af0b5)


---

## ✅ Phase 4: Verification & Real-Time Monitoring
The final verification was performed by monitoring the `archives.json` log stream. By executing a `tail -f` command, I successfully confirmed that traffic originating from the Kali Linux attacker (**192.168.1.5**) was being captured, decoded, and stored by the Wazuh Manager in real-time.

**Traffic Captured:**
* Source IP: 192.168.1.5 (Kali Linux)
* Destination IP: 192.168.1.10 (Windows 11)
* Format: EVE JSON

![terminal_logs](https://github.com/user-attachments/assets/817ecfdf-b81a-4c44-a4c1-08490a5b59b4)


---

## 🛠️ Challenges & Troubleshooting: The "CLI vs. Dashboard" Gap

While the backend integration was successful, a known limitation was encountered regarding the Wazuh Dashboard visualization. 

* **The Issue**: Despite the Wazuh Manager correctly ingesting and decoding Suricata logs, the Dashboard did not automatically render the `wazuh-archives-*` index pattern.
* **The Diagnosis**: Verified via `ossec.log` that no manager-level errors were present. Further investigation confirmed the issue resided in the Indexer's mapping/template phase, likely due to memory constraints in the virtualized lab environment.
* **The Validation**: To ensure the data pipeline was intact, I pivoted to CLI-based verification. Running `tail -f /var/ossec/logs/archives/archives.json` provided definitive proof of successful log ingestion and JSON decoding.

> **Engineer's Note**: In a production SOC environment, dashboard failures occur. Demonstrating the ability to bypass the UI and verify the raw data stream is a critical skill for ensuring continuous security monitoring.

---

## 🧠 Skills Demonstrated
* **Linux Administration**: Managing system services and navigating protected log directories.
* **SIEM/IDS Integration**: Mapping external tool logs (Suricata) to a SIEM ingest pipeline.
* **Log Analysis**: Reading and interpreting raw JSON security telemetry.
* **Troubleshooting**: Managing file permissions and service restarts to ensure data flow.

---

## 🏆 Final Result
The integration successfully creates a unified security monitoring environment. By leveraging Suricata's deep packet inspection and Wazuh's centralized management, the system provides an exhaustive audit trail of all network activity, even for events that do not trigger standard high-severity alerts.
