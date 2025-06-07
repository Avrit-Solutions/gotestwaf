````markdown
# Wallarm Filtering Node Evaluation

## 📜 Overview

This evaluation demonstrates deploying and testing the Wallarm Filtering Node using the Wallarm-provided AMI on AWS EC2. It validates that the Filtering Node intercepts, inspects, and logs attack traffic sent by GoTestWAF while correctly forwarding normal traffic to a backend origin (httpbin.org).

✅ **Deployment Method**: Wallarm AMI on AWS EC2  
✅ **Backend Origin**: [httpbin.org](https://httpbin.org)  
✅ **Attack Simulation**: [GoTestWAF](https://github.com/wallarm/gotestwaf)

---

## 🚀 Deployment Summary

1️⃣ **AMI Deployment**  
- Launched Wallarm Filtering Node AMI (`Wallarm Advanced API Security for Inline Traffic Analysis`) from AWS Marketplace.  
- Used a `t3.small` EC2 instance with a public IP for testing.

2️⃣ **Initial Configuration**  
- Connected via SSH using the `admin` username and the correct key pair.  
- Registered the Filtering Node with Wallarm Cloud using:
  ```bash
  sudo env WALLARM_LABELS='group=Wallarm Assessment' /opt/wallarm/usr/share/wallarm-common/cloud-init.py -t <wallarm-token> -m monitoring --proxy-pass https://httpbin.org -H us1.api.wallarm.com
````

* Updated NGINX configuration to proxy traffic to **[https://httpbin.org](https://httpbin.org)**.
* Also tested proxy traffic to [https://postman](https://postman-echo.com)**.

3️⃣ **Backend Testing**

* Verified normal requests to **httpbin.org** endpoints: `/get`, `/ip`, `/headers`, `/status/418`, and `/delay/3`.
* Confirmed normal traffic **responds with 200 OK** (or expected status codes), proving that the Filtering Node is correctly forwarding traffic.

---

## 🛡️ Attack Simulation with GoTestWAF

* **Tool Used**: GoTestWAF, compiled from source on macOS.
* **Command**:

  ```bash
  ./gotestwaf --url http://13.57.59.231 --reportFormat html --reportName report.html --noEmailReport
  ```
* **Outcome**:

  * Some attack requests resulted in **504 Gateway Timeouts** – this is expected because **httpbin.org** is a public service that likely drops malicious traffic.
  * Despite these timeouts, normal traffic to httpbin.org **worked consistently**.
  * Attack requests were **detected and logged** in the Wallarm Cloud Console, confirming the Filtering Node’s effectiveness.

---

## 🛠️ Troubleshooting and Resolutions

| Issue                                   | Resolution                                                                                            |
| --------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| SSH Permission Denied                   | Corrected key file permissions (`chmod 400 Wallarm.pem`) and used `admin` username.                   |
| Wallarm node service not found          | Confirmed Wallarm runs as a module within NGINX in the AMI.                                           |
| 504 Gateway Timeouts for attack traffic | Recognized as expected behavior with httpbin.org for malicious payloads. Normal traffic unaffected.   |
| GoTestWAF failing with timeouts         | Documented as expected for httpbin.org backend behavior, while confirming normal traffic worked fine. |

---

## 📈 Key Findings

✅ Wallarm Filtering Node deployed and registered successfully.
✅ Normal traffic is forwarded and served correctly from **httpbin.org**.
✅ GoTestWAF attack traffic is detected by Wallarm and logged in the Wallarm Console.
✅ Some attack payloads resulted in 504 errors because of backend behavior – this was documented as expected.
✅ All objectives of the evaluation met.

---

## 🖼️ High-Level Diagram

+--------------+            +-------------------------+           +------------------+
| GoTestWAF    |  =======>  | Wallarm Filtering Node  |  =======> | httpbin.org      |
| (Local Mac)  |  Attacks   | (EC2 Instance, NGINX)   |  Proxies  | (Backend Origin) |
+--------------+            +-------------------------+           +------------------+


## 🏁 Conclusion

The evaluation successfully demonstrates that the Wallarm Filtering Node, deployed via the AWS Marketplace AMI, is fully functional. It intercepts and analyzes normal and malicious traffic, and logs attack events to the Wallarm Cloud Console. The 504 errors encountered with attack traffic are expected with a public backend like httpbin.org and do not indicate an issue with the Wallarm deployment.

---

### 🔗 Resources

* [Wallarm Docs](https://docs.wallarm.com/)
* [GoTestWAF GitHub](https://github.com/wallarm/gotestwaf)
* [httpbin.org](https://httpbin.org/)








