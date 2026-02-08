# AWS Cloud SOC Security Monitoring System

## Project Overview
Built a cloud-native Security Operations Center (SOC) monitoring system in AWS to detect and investigate security incidents in real-time.

## Objective
Demonstrate hands-on cloud security skills including audit logging, threat detection, automated alerting, and incident investigation - critical capabilities for SOC Analyst and Cloud Security Engineer roles.

## Technologies Used
- **AWS CloudTrail**: Audit logging and compliance
- **AWS CloudWatch**: Metrics, alarms, and log analysis
- **AWS SNS**: Alert notifications
- **AWS IAM**: Identity and access management
- **AWS S3**: Storage security
- **CloudWatch Logs Insights**: Log querying and analysis


## Architecture

<img width="1247" height="527" alt="image" src="https://github.com/user-attachments/assets/5d59b404-aad7-4003-a441-ba5d57efde00" />



## Roles & Access Model

### Root Account
- **Used only for initial AWS account setup and CloudTrail configuration**
- **Not used for day-to-day operations or monitoring activities**
- **Any root account usage is treated as a security incident and triggers alerts**

### SOC Administrator (IAM User)
- **Responsible for configuring all security detections and monitoring rules**
- **Investigates alerts and analyzes CloudTrail logs during incidents**
- **Simulates misconfigurations and attack scenarios for SOC testing**
- **Operates under a least-privilege access model with administrative oversight**

<img width="950" height="318" alt="image" src="https://github.com/user-attachments/assets/34c533d8-cc6e-47cb-bb85-273923437000" />





## Root Account Usage Detection

### **Incident Overview**
- **Purpose**: Detect and alert on any usage of the AWS root account
- **Severity**: **CRITICAL**
- **Rationale**: The root account has unrestricted privileges and must never be used for routine operations

---

## Detection Implementation Steps

### **Step 1: CloudTrail Configuration**
- **CloudTrail was created and verified while logged in as the SOC Administrator (IAM user)**
- **Multi-region trail enabled to capture all management events**
- **Root account activity included by default**

<img width="950" height="381" alt="image" src="https://github.com/user-attachments/assets/e2a12760-26df-4fed-a0fd-d9d836570498" />


---

### **Step 2: Log Ingestion to CloudWatch**
- **CloudTrail logs are delivered to a centralized CloudWatch Log Group**
- **Root account events are visible in CloudWatch Logs**
- **Verified presence of `userIdentity.type = "Root"` events**

<img width="950" height="348" alt="image" src="https://github.com/user-attachments/assets/57d483a7-86cc-4677-af19-dfd0784f65c2" />


---

### **Step 3: Metric Filter Creation**
- **CloudWatch metric filter created to detect root account usage**
- **Filter logic matches CloudTrail events where `userIdentity.type = "Root"`**
- **Each matching event increments a custom metric**

<img width="950" height="429" alt="image" src="https://github.com/user-attachments/assets/32933163-6dd6-446d-85b0-7139b0bed84d" />


---

### **Step 4: CloudWatch Alarm Configuration**
- **Alarm created on the root usage metric**
- **Threshold set to trigger on any root activity**
- **Alarm state transitions to ALARM upon detection**

<img width="950" height="427" alt="image" src="https://github.com/user-attachments/assets/4c3177d9-36ab-4cbc-b06a-c57fea463664" />


---

### **Step 5: Alert Notification**
- **SNS topic configured for SOC alerts**
- **Email notification sent immediately when alarm enters ALARM state**
- **SOC analyst receives real-time visibility of root account usage**

<img width="940" height="419" alt="image" src="https://github.com/user-attachments/assets/7b2f6ccc-5135-4db3-bc88-e26c3bd2c566" />


---

## Investigation Outcome
- **Root account activity successfully detected**
- **Events attributed to specific timestamps and source IPs**
- **Alert pipeline validated end-to-end**


## S3 Public Bucket Exposure Detection

### **Incident Overview**
- **Purpose**: Detect and alert on S3 buckets being made publicly accessible
- **Severity**: **HIGH**
- **Rationale**: Public S3 buckets are a leading cause of large-scale data breaches and sensitive data exposure

---

## Detection Implementation Steps

### **Step 1: Monitoring S3 Configuration Changes**
- **CloudTrail records all S3 management API calls**
- **Events related to access control and bucket policies are captured**
- **This includes actions that can expose buckets to the public internet**



### **Step 2: Metric Filter Creation**
- **A CloudWatch metric filter was created to detect public exposure-related API calls**
- **The filter monitors the following CloudTrail events**:
  - `PutBucketAcl`
  - `PutBucketPolicy`
  - `DeletePublicAccessBlock`
- **Each matching event increments a custom security metric**

<img width="950" height="854" alt="image" src="https://github.com/user-attachments/assets/5666eac4-3f42-4770-945d-71db692cac94" />




### **Step 3: CloudWatch Alarm Configuration**
- **An alarm was created on the S3 public exposure metric**
- **Threshold configured to trigger on any single exposure event**
- **Alarm transitions to ALARM state immediately upon detection**

<img width="1562" height="700" alt="image" src="https://github.com/user-attachments/assets/9c642613-edec-4f3f-8e7a-49631e0c4031" />


---

### **Step 4: Alert Notification**
- **SNS topic configured for critical SOC alerts**
- **Email notification sent when the alarm enters ALARM state**
- **Ensures immediate awareness of potential data exposure**

<img width="950" height="379" alt="image" src="https://github.com/user-attachments/assets/c6b07d47-1798-4ab4-bbe0-5a2d5858e9c2" />


---

## Investigation Outcome
- **Identified the specific S3 bucket that was exposed**
- **Determined the IAM user responsible for the configuration change**
- **Reviewed access settings to assess exposure scope**
- **Public access was removed as part of containment**


## Failed Console Login Detection

### **Incident Overview**
- **Purpose**: Detect repeated failed AWS Management Console login attempts
- **Severity**: **MEDIUM–HIGH**
- **Rationale**: Multiple failed login attempts may indicate brute-force attacks, credential stuffing, or unauthorized access attempts

---

## Detection Implementation Steps

### **Step 1: Monitoring Console Authentication Events**
- **CloudTrail records all AWS Management Console sign-in attempts**
- **Both successful and failed login attempts are captured**
- **Failed attempts are identified by authentication response status**

---

### **Step 2: Metric Filter Creation**
- **A CloudWatch metric filter was created to detect failed console login attempts**
- **The filter matches CloudTrail events with the following attributes**:
  - `eventName = "ConsoleLogin"`
  - `responseElements.ConsoleLogin = "Failure"`
- **Each failed login attempt increments a custom authentication metric**

<img width="940" height="417" alt="image" src="https://github.com/user-attachments/assets/dc98ed2c-a877-424a-83a7-ef864851b25f" />


---

### **Step 3: CloudWatch Alarm Configuration**
- **An alarm was created on the failed login metric**
- **Threshold configured to trigger when multiple failures occur within a short time window**
- **Alarm transitions to ALARM state when the threshold is exceeded**

<img width="1557" height="697" alt="image" src="https://github.com/user-attachments/assets/a5c541c1-8ba9-44d6-97bf-daae7a7facda" />


---

### **Step 4: Alert Notification**
- **SNS topic configured for authentication-related SOC alerts**
- **Email notification sent when the alarm enters ALARM state**
- **Enables rapid response to potential credential abuse attempts**

<img width="940" height="429" alt="image" src="https://github.com/user-attachments/assets/c2687c2e-f581-4f36-9731-49729a5168c2" />


---

## Investigation Outcome
- **Identified the targeted IAM user or root account**
- **Observed repeated authentication failures from a single source IP**
- **Reviewed timestamps and frequency to assess attack pattern**
- **No successful authentication observed during the incident window**

---

## CloudTrail Log Tampering Detection

### **Incident Overview**
- **Purpose**: Detect attempts to disable or delete AWS CloudTrail logging
- **Severity**: **CRITICAL**
- **Rationale**: Disabling audit logs is a common attacker technique to evade detection and obscure malicious activity

---

## Detection Implementation Steps

### **Step 1: Monitoring CloudTrail Management Events**
- **CloudTrail records all management API calls related to logging configuration**
- **Events affecting audit visibility are captured as management events**
- **These events are critical indicators of defense evasion**

---

### **Step 2: Metric Filter Creation**
- **A CloudWatch metric filter was created to detect CloudTrail log tampering attempts**
- **The filter matches the following CloudTrail events**:
  - `StopLogging`
  - `DeleteTrail`
- **Each matching event increments a custom log tampering metric**

<img width="940" height="425" alt="image" src="https://github.com/user-attachments/assets/7f1de205-6e84-4d9f-8134-934e3160d237" />

---

### **Step 3: CloudWatch Alarm Configuration**
- **An alarm was created on the CloudTrail log tampering metric**
- **Threshold configured to trigger on any single occurrence**
- **Alarm immediately transitions to ALARM state upon detection**

<img width="1567" height="700" alt="image" src="https://github.com/user-attachments/assets/d2594d67-d9cf-42d1-adea-e31874a852e1" />


---

### **Step 4: Alert Notification**
- **SNS topic configured for critical SOC alerts**
- **Email notification sent when the alarm enters ALARM state**
- **Ensures immediate awareness of logging disruption attempts**


<img width="940" height="394" alt="image" src="https://github.com/user-attachments/assets/848c9128-223c-4d23-9c06-0c6b434a7d6c" />

---

## Investigation Outcome
- **Identified the IAM user responsible for the tampering attempt**
- **Confirmed the specific action taken against CloudTrail logging**
- **Verified the duration of logging disruption**
- **CloudTrail logging was re-enabled immediately as part of containment**



  ## Skills Demonstrated
- ✅ Cloud security architecture and design
- ✅ SIEM-like detection rule creation
- ✅ Log aggregation and analysis
- ✅ Metric-based alerting and monitoring
- ✅ Incident detection and response
- ✅ CloudTrail forensic investigation
- ✅ Security automation with AWS services
- ✅ False positive reduction strategies
- ✅ Technical documentation and reporting


## Lessons Learned
- Detection rules require business context to minimize false positives
- State-based alarms prevent alert fatigue in high-volume environments
- CloudTrail provides complete audit trail for forensic investigation
- Threshold tuning is critical for pattern-based detections (e.g., 5 failed logins vs. 1)
- Real SOC work requires balancing security visibility with operational efficiency

## Future Enhancements
- Integrate with AWS GuardDuty for ML-based threat detection
- Add automated remediation using Lambda functions
- Implement VPC Flow Logs for network-level monitoring
- Create Slack/PagerDuty integration for alerting
- Add CloudWatch Logs Insights queries for advanced analysis
- Implement SOAR-like playbooks for incident response


















