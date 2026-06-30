# Cloud-Based Serverless Inventory Management System (AWS)

A secure, fully serverless, and highly scalable inventory management backend built natively on AWS. This architecture exposes a high-performance HTTPS API layer to manage multi-warehouse stock allocations, handle secure media upload orchestration, and process transactional audit trails with a zero-server footprint.

---

## 🏗 System Architecture

The infrastructure relies entirely on AWS-managed serverless resources to guarantee high availability, automated horizontal scaling, and strict cost efficiency[cite: 2].

[ User/Client ] ──( HTTPS Auth )──> [ Amazon Cognito (JWT) ]
│
(Bearer Token)
▼
[ API Gateway (HTTP API) ] ──( Triggers )──> [ AWS Lambda (Node.js 22.x) ]
│
┌───────────────────────┴───────────────────────┐
▼                                               ▼
[ Amazon DynamoDB ]                                  [ Amazon S3 ]
(Items, Balances, Transactions)                      (Encrypted Media Assets)
│                                               │
└───────────────────────┬───────────────────────┘
▼
[ Amazon CloudWatch Logs ]
│
▼
[ AWS Backup ]


### Infrastructure Workflow & Execution Lifecycle
1. **Identity Layer:** Users authenticate through an Amazon Cognito User Pool via a Hosted UI to extract a cryptographically signed JSON Web Token (JWT)[cite: 2].
2. **API Ingress:** Outbound HTTPS requests hit an AWS API Gateway (HTTP API) which utilizes a native Cognito JWT Authorizer to validate claims[cite: 2].
3. **Compute Layer:** Validated requests trigger decoupled Node.js 22.x AWS Lambda execution environments configured with fine-grained IAM roles[cite: 2].
4. **Persistence & Assets:** Microservices interface atomically with Amazon DynamoDB tables or dynamically provision time-limited pre-signed URLs for direct, secure uploads to Amazon S3[cite: 2].
5. **Observability & SRE:** Log aggregates and infrastructure behaviors map to CloudWatch for continuous alerting and system auditability[cite: 2].

---

## 🛠 Tech Stack & Ecosystem

* **Compute:** AWS Lambda (Node.js 22.x runtime execution)[cite: 2]
* **API Management:** AWS API Gateway (HTTP API wrapper optimized for minimal latency)[cite: 2]
* **Identity & SecOps:** Amazon Cognito User Pools, IAM Least-Privilege Policies, Multi-Factor Authentication (MFA)[cite: 2]
* **Databases:** Amazon DynamoDB (NoSQL engine featuring Single-Table Design properties)[cite: 2]
* **Object Storage:** Amazon Simple Storage Service (S3) with Managed Server-Side Encryption (SSE-S3)[cite: 2]
* **Observability:** Amazon CloudWatch Logs, Metrics, and Alarms[cite: 2]
* **Data Resilience:** AWS Backup, Point-in-Time Recovery (PITR)[cite: 2]

---

## 🚀 Microservices Design & API Endpoints

The API surface area map decouples core business components across specific endpoints, protected under explicit HTTP methods[cite: 2]:

| Endpoint | HTTP Method | Target Lambda Function | Subsystem Purpose |
| :--- | :--- | :--- | :--- |
| `/items` | **POST** | `createItem` | Inserts structured inventory assets into DynamoDB[cite: 2]. |
| `/items` | **GET** | `getItems` | Queries globally indexed items from stock[cite: 2]. |
| `/stock/adjust` | **POST** | `adjustStock` | Executes atomic balance drift variations and registers logs[cite: 2]. |
| `/stock/{locationId}/{itemId}` | **GET** | `getStock` | Extracts precise local allocation states[cite: 2]. |
| `/images/presign` | **GET** | `presignUpload` | Evaluates requests and returns private S3 upload vectors[cite: 2]. |

---

## 🔒 Rigorous DevSecOps & Resilience Blueprints

* **Zero Trust Access:** Enforced strict JSON Web Token validation at the gateway boundary[cite: 2]. No unauthenticated data traffic penetrates internal microservices[cite: 2].
* **Least Privilege IAM Enforcement:** Abolished generic access patterns by pinning explicit resource policies and dedicated execution contexts directly onto separate Lambda containers[cite: 2].
* **Data Persistence Isolation:** Engineered data separation by isolating indices into three unique NoSQL schemas (`Items`, `StockBalances`, and `Transactions`) optimized with composite keys[cite: 2].
* **Disaster Recovery Infrastructure:** Enabled native Point-in-Time Recovery (PITR) for all DynamoDB engines alongside automated AWS Backup vault snapshots to insulate production storage against failures[cite: 2].
* **Proactive Guardrails:** Configured CloudWatch telemetry thresholds to trigger immediate warnings upon high Lambda error frequencies or API Gateway `5xx` error trends[cite: 2].

---

## 🧪 Deployment Testing Verification

Secure execution is fully validated via API calls using `Bearer` token verification headers[cite: 2]:

```bash
curl -X POST "https://<api-id>[.execute-api.eu-west-3.amazonaws.com/stock/adjust](https://.execute-api.eu-west-3.amazonaws.com/stock/adjust)" \
  -H "Authorization: Bearer $ID_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"itemId":"item-001","locationId":"warehouse-1","qtyDelta":10}'
📈 Key Project Takeaways
Architectural Shifts: Opting for API Gateway's newer HTTP API spec rather than legacy REST blueprints yielded decreased latency overheads, cost savings, and direct CORS mapping capabilities[cite: 2].

NoSQL Atomicity: Handled the synchronization between inventory levels and historical audits by enforcing atomic updates on complex multi-warehouse balance schemas[cite: 2].


---

## 🛡 Option 2: For the Secure Network & SIEM Monitoring Environment
*(Reference file: `Project Report - Hamza Bin Saleem.pdf`)*

```markdown
# Secure Network Architecture & SIEM Monitoring Environment

A local, production-grade network isolation and security information and event management (SIEM) deployment[cite: 1]. This framework establishes a hardened Demilitarized Zone (DMZ), enforces zero-trust traffic interception through forward proxies, handles certificate management via a private PKI, and uses the ELK stack for centralized telemetry ingestion[cite: 1].

---

## 🏗 Network & Monitoring Architecture

The architecture enforces strict boundary defenses and structured data pipelines to map system behaviors[cite: 1].

[ External/Public Ingress ] ──( Ports 80 / 443 Only )──> [ Hardened DMZ (iptables) ]
│
▼
[ Apache Web Server ]
(hamza.local SSL)
│
(Traffic Proxy)
▼
[ Kibana Analytics ] <── [ Elasticsearch ] <── [ Logstash ] <── [ Squid Proxy ]


### Data Pipeline & Packet Lifecycle
1. **Perimeter Hardening:** Network packets contact an `iptables` perimeter filter configured with a strict default-drop policy, dropping unauthorized external connection attempts[cite: 1].
2. **TLS Termination:** Valid inbound HTTP/HTTPS connections route directly to an Apache2 reverse web server configured via custom X.509 keys signed by a local private Certificate Authority (CA)[cite: 1].
3. **Regulated Forward Proxying:** Client application traffic routes directly through a localized Squid Proxy server to audit and isolate client connection streams[cite: 1].
4. **Structured SIEM Ingestion:** Active log feeds generated by Apache and Squid pull into a Logstash engine, streaming the logs through specialized `grok` parser regex formats[cite: 1].
5. **Analytics Engine:** Parsed log objects index natively within an Elasticsearch data tier and visually map onto centralized Kibana dashboards for active security threat discovery[cite: 1].

---

## 🛠 Tech Stack & Ecosystem

* **SIEM & Monitoring Layer:** Elasticsearch, Logstash, Kibana (ELK Stack v7.x)[cite: 1]
* **Web Services Engine:** Apache2 Server (Virtual Host config module)[cite: 1]
* **Traffic Proxy Controls:** Squid Proxy Daemon[cite: 1]
* **Boundary Layer Security:** Linux Netfilter Architecture (`iptables` persistent rules)[cite: 1]
* **Cryptographic PKI Engine:** OpenSSL (Self-Provisioned Root Certificate Authority)[cite: 1]
* **Operating Environment:** Ubuntu Linux Enterprise Shell[cite: 1]

---

## 🔒 SecOps Hardening & Boundary Blueprints

### 1. Private PKI Infrastructure & TLS Termination
Avoided the browser `SSL_ERROR_BAD_CERT_DOMAIN` restriction error by enforcing specialized Subject Alternative Name (SAN) extension blocks during local Certificate Signing Request (CSR) processing[cite: 1]:
* Root CA private key paired via 3DES encryption algorithms[cite: 1].
* Generated local webserver RSA keys up to 4096-bit cryptographic complexity[cite: 1].
* Established server parameters supporting strict HTTPS validation boundaries on local domain layers (`hamza.local`)[cite: 1].

### 2. Linux Netfilter Framework (`iptables` Policies)
A rigorous firewall script isolates production components, leaving only key web paths open[cite: 1]:
* **INPUT/FORWARD/OUTPUT DROP:** Enforced explicit drop parameters for all raw inbound and outbound packets across default interface tables[cite: 1].
* **Loopback Exemption:** Opened isolated internal routing interfaces (`lo`) to maintain safe communications between the ELK database engines[cite: 1].
* **Interface Filtering:** Confined open listening routes strictly to ports 80 (HTTP) and 443 (HTTPS) on the active internet interface (`ens33`), completely blinding internal telemetry systems from the public web[cite: 1].

### 3. Decoupled Log Processing Pipelines
Configured separate Logstash pipeline filters (`/etc/logstash/conf.d/`) to index raw streams[cite: 1]:
* Apache access records map cleanly against standard `%`{`COMBINEDAPACHELOG`} grok patterns[cite: 1].
* Squid proxy access strings decompose through a custom key string parser mapping timestamps, connection times, client IPs, action statuses, and destination servers[cite: 1].

---

## 🧪 Testing Verification Runbooks

### Checking Cryptographic Certificate Integration
```bash
openssl verify -CAfile /etc/ssl/certs/myCA.pem /etc/ssl/certs/hamza.local.crt
# Output Response: hamza.local.crt: OK