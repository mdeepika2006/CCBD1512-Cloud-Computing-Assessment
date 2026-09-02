# Cloud-Ready Smart Campus Service Request and Incident Response Platform

**Course:** CSA15 – Cloud Computing and Big Data Analytics  
**Slot:** B | **Faculty:** Dr. C. Rajesh Babu (Employee ID: SSETSCS415)  
**Academic Year:** 2023–2027 | **Course Outcomes:** CO1, CO2 | **Bloom's Level:** L4, L5, L6  
**SDG Alignment:** SDG 9 (Industry, Innovation & Infrastructure), SDG 12 (Responsible Consumption & Production), SDG 13 (Climate Action)

---

## 1. Project Title
**Cloud-Ready Smart Campus Service Request and Incident Response Platform: API Implementation, Workload Sizing and Virtualized Deployment**

---

## 2. Problem Overview
Universities require an accessible, reliable, scalable, and responsive digital mechanism for reporting and managing campus infrastructure, utility, safety, and service requests. Traditional paper-based or ad-hoc communication channels suffer from lack of accountability, delayed triage of hazardous emergencies, unorganized tracking, and poor resource utilization.

This platform provides a centralized, cloud-ready web solution where students and campus users can register service requests (categorized by Electrical, Network, Infrastructure, Water, Cleanliness, Security, and Other) and monitor their resolution status in real-time. Authorized facility staff can view, filter, triage, and progress requests through standard lifecycle phases (`Pending` &rarr; `In Progress` &rarr; `Resolved`).

---

## 3. Key Features
* **Role-Based Workspaces:** Dedicated dashboards for **Campus Users** (request creation, personal tracking) and **Authorized Staff** (operations queue, status transition, triage).
* **Persistent SQLite Storage:** Permanent on-disk database with automated schema creation, indexing, and seed data initialization.
* **Complete REST API Architecture:** JSON-compliant endpoints supporting CRUD operations, input sanitization, query filters, and standard HTTP response status codes (`200 OK`, `201 Created`, `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`, `500 Internal Server Error`).
* **Multi-Parameter Search & Filtering:** Filter service requests instantly by category, priority level, status, or free-text keywords.
* **Role Restriction & Access Control:** Normal campus users are strictly forbidden from modifying request statuses (`403 Forbidden`).
* **Workload Sizing & Cloud Telemetry Panel:** Live in-app telemetry inspector displaying cloud compute (vCPU), RAM, and storage sizing equations mapped directly to the pilot workload.
* **Containerized Deployment:** Fully dockerized for repeatable, isolated, and scalable deployment across cloud nodes or local container engines.

---

## 4. Unique Innovation: Emergency Priority-Based Request Handling
> **Selected Innovation:** *Rule-based Emergency Priority Incident Dispatch*

When a user submits a service request with priority **`Emergency`**:
1. **Critical Incident Detection:** The system automatically flags the record with `is_emergency = 1`.
2. **Top-of-Queue Priority Sorting:** Regardless of submission timestamp, Emergency requests are automatically indexed and sorted to the **very top of the Authorized Staff queue** using a weighted priority sorting algorithm:
   $$\text{Priority Rank: } \text{Emergency (1)} > \text{High (2)} > \text{Medium (3)} > \text{Low (4)}$$
3. **High-Visibility UI Indicators:** 
   - Flashing red **`🚨 EMERGENCY`** pulsing badge.
   - Dedicated red-bordered alert row (`.row-emergency`) with ambient shadow.
   - Prominent **Critical Emergency Incident Banner** at the top of the Staff dashboard displaying count and instant 1-click filter.
4. **Rapid Triage:** Authorized staff can instantly open emergency details and transition the status to `In Progress` with response dispatch notes.

*Note: This mechanism utilizes deterministic rule-based algorithms rather than non-deterministic ML models to eliminate inference latency during life-safety hazards.*

---

## 5. Technology Stack
* **Frontend:** HTML5, CSS3 (Modern Responsive Dashboard, CSS Variables, Keyframe Animations), Vanilla JavaScript (ES6+ Fetch API, DOM Controller).
* **Backend:** Node.js with Express.js REST API framework.
* **Database:** SQLite with persistent disk storage (`database/campus_service.db`).
* **Containerization:** Docker (`Dockerfile`, `.dockerignore`, Alpine Node runtime).
* **API Testing & Compatibility:** Postman (`postman_collection.json`), VS Code, GitHub.

---

## 6. Folder Structure
```text
smart-campus-service-platform/
├── database/
│   ├── db.js                   # SQLite database connection, schema migration & seed data
│   └── campus_service.db       # Persistent SQLite binary database file
├── middleware/
│   ├── auth.js                 # Authentication, role verification & access control
│   └── validator.js            # Input validation & schema sanitization
├── routes/
│   ├── auth.js                 # User login, session profile & demo credentials routes
│   └── requests.js             # Service request REST API endpoints (CRUD, filters, status)
├── public/
│   ├── index.html              # Single Page Application UI with Student & Staff views
│   ├── css/
│   │   └── style.css           # Styling, emergency glow effects, badges, responsive layout
│   └── js/
│       └── app.js              # Frontend controller, API integration & state management
├── tests/
│   └── api.test.js             # Automated 16-point end-to-end verification test suite
├── Dockerfile                  # Container build instructions (Node Alpine)
├── .dockerignore               # Container build exclusions
├── package.json                # Project dependencies and script definitions
├── postman_collection.json     # Ready-to-import Postman API collection
└── README.md                   # Complete academic documentation & deployment guide
```

---

## 7. Cloud Workload Sizing & Infrastructure Estimation
*(Calculations from Assignment Section D & E)*

### 1. Workload Parameters
* **Registered Users ($N$):** 3,000 users
* **Daily Active Users (DAU):** $3,000 \times 25\% = 750\text{ users/day}$
* **Peak Active Users:** $750 \times 20\% = 150\text{ concurrent users}$
* **User Request Rate:** $1.5\text{ actions/user/min}$
* **Peak Factor:** $2.0\text{ (bursts / emergency events)}$

### 2. Compute (vCPU) Sizing
$$\text{Peak Request Rate} = \frac{150 \times 1.5 \times 2.0}{60} = 7.5\text{ requests/second}$$
$$\text{CPU Demand} = 7.5 \times 0.10\text{ s} = 0.75\text{ CPU-seconds/second}$$
$$\text{Required vCPU} = \frac{0.75}{0.65\text{ (target utilization)}} \approx 1.15\text{ vCPU}$$
$$\text{With 25\% Headroom} = 1.15 \times 1.25 = 1.44\text{ vCPU} \implies \mathbf{2\text{ vCPUs Selected}}$$

### 3. Memory (RAM) Sizing
$$\text{Base Allocation} = 2.0\text{ GB (OS + Express + DB buffers)}$$
$$\text{With 25\% Headroom} = 2.0 \times 1.25 = 2.5\text{ GB} \implies \mathbf{4\text{ GB RAM Selected}}$$

### 4. Storage Sizing (365-Day Retention)
* **Business Data:** $750\text{ users} \times 3\text{ req/day} \times 3\text{ KB} \times 365\text{ days} = 2.35\text{ GB} \xrightarrow{+30\%\text{ DB overhead}} 3.06\text{ GB}$
* **Evidence Uploads:** $750 \times 0.10 \times 1\text{ MB} \times 365 = 26.73\text{ GB}$
* **Log Storage (30-day retention):** $450\text{ req/min} \times 60 \times 24 \times 2\text{ KB} \times 30\text{ days} = 37.2\text{ GB}$
* **Total Estimated Storage:** $3.06 + 26.73 + 37.2 = 66.99\text{ GB}$
$$\text{With 25\% Headroom} = 66.99 \times 1.25 = 83.74\text{ GB} \implies \mathbf{100\text{ GB SSD Selected}}$$

---

## 8. Installation Instructions & npm Commands

### Prerequisites
* [Node.js](https://nodejs.org/) (version 18 or higher)
* [npm](https://www.npmjs.com/) (version 9 or higher)

### Installation
Clone or navigate to the project directory:
```bash
cd smart-campus-service-platform
```

Install the required npm packages:
```bash
npm install
```

---

## 9. How to Run Locally

### Start Server
```bash
npm start
```
*The server will start on port `3000`.*

### Access in Web Browser
Open your browser and navigate to:
```text
http://localhost:3000
```

---

## 10. Demo Login Credentials

For demonstration and grading convenience, pre-configured demo users are provided on the login page with 1-click autofill buttons:

| Role | Username | Password | Full Name & Department | Permissions |
| :--- | :--- | :--- | :--- | :--- |
| **Campus User** | `student` | `student123` | Alex Johnson (CSE Dept) | Create requests, view my requests, view status |
| **Authorized Staff** | `staff` | `staff123` | Dr. Rajesh Staff Admin (Facilities) | View all requests, filter/search, update status |
| **Campus User 2** | `student2` | `student123` | Priya Sharma (ECE Dept) | Create requests, view my requests |

> **Security Note:** In production enterprise environments, passwords must be securely hashed using `bcrypt`/`Argon2`, and session tokens generated via asymmetric RSA/ECDSA JWTs with SSL/TLS encryption.

---

## 11. REST API Specification

### Base URL: `http://localhost:3000/api`

| Method | Endpoint | Description | Auth Required | Expected Status Code |
| :--- | :--- | :--- | :--- | :--- |
| `POST` | `/api/auth/login` | Authenticate user & get session token | No | `200 OK` / `401 Unauthorized` |
| `GET` | `/api/auth/me` | Fetch authenticated user profile | Yes | `200 OK` / `401 Unauthorized` |
| `POST` | `/api/requests` | Create a new service request | Yes (Any role) | `201 Created` / `400 Bad Request` |
| `GET` | `/api/requests` | Retrieve requests (supports category, priority, status, search, mine filters) | Optional | `200 OK` |
| `GET` | `/api/requests/:id` | Retrieve single request by ID or Code (e.g. `REQ-1001`) | Optional | `200 OK` / `404 Not Found` |
| `PUT` | `/api/requests/:id/status` | Update request status & staff work notes | **Staff Only** | `200 OK` / `400 Bad Request` / `403 Forbidden` / `404 Not Found` |
| `GET` | `/api/requests/stats/summary` | Summary counters for dashboard KPI cards | Optional | `200 OK` |
| `GET` | `/api/system/workload` | Cloud infrastructure sizing calculations & telemetry | No | `200 OK` |
| `GET` | `/api/health` | Service health status | No | `200 OK` |

### Sample API Requests (cURL)

#### 1. Create a Service Request
```bash
curl -X POST http://localhost:3000/api/requests \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <STUDENT_TOKEN>" \
  -d '{
    "category": "Network",
    "location": "Library 2nd Floor",
    "description": "WiFi router blinking red, no internet connectivity.",
    "priority": "High"
  }'
```

#### 2. Create an Emergency Request
```bash
curl -X POST http://localhost:3000/api/requests \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <STUDENT_TOKEN>" \
  -d '{
    "category": "Security",
    "location": "Science Block Ground Floor",
    "description": "Smoke alarm sounding near laboratory power room.",
    "priority": "Emergency"
  }'
```

#### 3. Authorized Staff Updates Request Status
```bash
curl -X PUT http://localhost:3000/api/requests/REQ-1001/status \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <STAFF_TOKEN>" \
  -d '{
    "status": "In Progress",
    "staffNotes": "Emergency electrician dispatched."
  }'
```

---

## 12. Docker Build & Run Procedure

### 1. Build the Docker Image
```bash
docker build -t smart-campus-service .
```

### 2. Run the Container
```bash
docker run -d -p 3000:3000 --name campus-platform smart-campus-service
```

### 3. Check Container Health
```bash
docker ps
```

### 4. Stop and Remove Container
```bash
docker stop campus-platform
docker rm campus-platform
```

---

## 13. Testing and Results Validation

### Automated Test Suite
An automated verification test script is included in `tests/api.test.js` validating all 16 assignment rubric criteria:
```bash
npm test
```

### Validation Matrix

| Test Case | Input / Action | Expected Result | Actual Result | Status |
| :--- | :--- | :--- | :--- | :--- |
| **Valid Request Creation** | Enter valid category, location, description, priority | Request created with `REQ-XXXX` & stored in SQLite | HTTP 201 Created, ID returned | ✅ PASS |
| **Invalid Input** | Submit request with empty category/description | System returns validation error | HTTP 400 Bad Request with details | ✅ PASS |
| **Emergency Priority** | Create request with `priority: "Emergency"` | Flagged `is_emergency=1`, sorted to top of staff list | Prominent red badge & rank #1 | ✅ PASS |
| **Request Retrieval** | Search using valid ID `REQ-1001` | Correct request details returned | HTTP 200 OK with full fields | ✅ PASS |
| **Invalid Request ID** | Search for `REQ-9999` | System returns 404 error | HTTP 404 Request Not Found | ✅ PASS |
| **Status Update (Staff)** | Staff updates status to `In Progress` | New status and staff notes saved in SQLite | HTTP 200 OK, updated record | ✅ PASS |
| **Role Restriction** | Student attempts to update status | System blocks update with 403 error | HTTP 403 Access Denied / Forbidden | ✅ PASS |
| **Priority Filtering** | Query `/api/requests?priority=Emergency` | Only Emergency requests returned | HTTP 200 OK filtered list | ✅ PASS |
| **Data Persistence** | Restart Node.js application server | Previous requests remain intact in SQLite DB | Data preserved across restarts | ✅ PASS |

---

## 14. Postman API Testing
To test the API using Postman:
1. Open **Postman**.
2. Click **Import** and select `postman_collection.json`.
3. The collection will load pre-configured requests for Authentication, Request Creation, Emergency Priority, Status Updates, and Cloud Telemetry.

---

## 15. Future Improvements
* **Automated Escalation Rules:** Auto-escalate `High` priority requests to `Emergency` if left in `Pending` status beyond a configured SLA threshold (e.g., 2 hours).
* **Push Notification Service:** WebSockets / WebPush integration for real-time sound alerts when an Emergency request is raised.
* **Geographic Mapping (GIS):** Interactive 2D/3D campus map with live pins indicating incident locations and hotspots.
* **Evidence Upload Microservice:** Cloud Object Storage (e.g. AWS S3 / MinIO) for attaching photo/video evidence to incident tickets.
