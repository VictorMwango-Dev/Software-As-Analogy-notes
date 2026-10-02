<h3 style="text-align: center;">PROMETHEUS AND GRAFANA</h3>

## Prometheus and Grafana — The Complete Doctor Analogy

Imagine you operate a **large hospital**.

The hospital has hundreds of patients. Some are healthy, some are getting worse, and some can suddenly become critically ill.

You need a system that continuously answers questions like:

- Is the patient alive?
    
- What is their heart rate?
    
- Is their blood pressure increasing?
    
- Are they breathing normally?
    
- When did their condition start getting worse?
    
- Should a doctor be notified?
    
- Can we see what happened over the last 24 hours?
    

Now replace the hospital with a **software system**.

---

# 1. The Big Picture

In the software world:

|Hospital|Software|
|---|---|
|Patient|Application/server|
|Heart rate|CPU usage|
|Blood pressure|Memory usage|
|Breathing rate|Request rate|
|Temperature|Server temperature|
|Medical monitor|Metrics endpoint/exporter|
|Doctor collecting measurements|Prometheus|
|Medical records|Prometheus time-series database|
|ICU dashboard|Grafana|
|Doctor's question|PromQL query|
|Medical alarm|Alert|
|Nurse receiving alarm|Alertmanager|
|Medical history|Historical metrics|
|Regular checkup|Scraping|
|Hospital ward|Infrastructure/environment|

So the basic relationship is:

```text
             SOFTWARE HOSPITAL
                   │
                   ▼
          ┌──────────────────┐
          │    APPLICATION   │
          │    "PATIENT"     │
          └────────┬─────────┘
                   │
              exposes vitals
                   │
                   ▼
          ┌──────────────────┐
          │    PROMETHEUS    │
          │     "DOCTOR"     │
          └────────┬─────────┘
                   │
              stores metrics
                   │
                   ▼
          ┌──────────────────┐
          │  METRIC HISTORY  │
          │  "MEDICAL FILE"  │
          └────────┬─────────┘
                   │
                   ▼
          ┌──────────────────┐
          │     GRAFANA      │
          │  "ICU SCREEN"    │
          └──────────────────┘
```

---

# 2. The Patient = Your Application

Let's say you have an application called:

```text
Online Shopping System
```

It has:

```text
Web Server
Database
Payment Service
Authentication Service
Order Service
```

These are your **patients**.

Just like a human body constantly produces measurable information, your application constantly produces operational information.

For example:

```text
CPU usage       = 72%
Memory usage    = 4.2 GB
Requests/sec    = 850
Errors/sec      = 12
Database connections = 45
Response time   = 120 ms
```

These measurements are called **metrics**.

---

# 3. What Is a Metric?

A metric is simply a **number that tells you something about the condition of your system**.

Think about a patient.

A doctor might record:

```text
Heart rate:      82 bpm
Temperature:     37.1°C
Blood pressure:  120/80
Oxygen:          98%
```

These are measurements.

Your software has its own measurements:

```text
CPU:             72%
Memory:          61%
Requests:        850/sec
Errors:          12/sec
Latency:         120ms
```

Those are metrics.

So:

> **Metrics are the vital signs of software.**

---

# 4. Why Do We Need Metrics?

Imagine a doctor saying:

> "I don't need to measure anything. I'll just look at the patient."

That's obviously dangerous.

The patient might **look fine while something is going wrong internally**.

Software is the same.

Your application may look fine to a user while:

```text
CPU → 95%
Memory → 92%
Database connections → 98%
Error rate → increasing
Latency → increasing
```

Without monitoring, you may discover the problem only when users start complaining.

Metrics allow you to detect problems **before or while they become serious**.

---

# 5. Prometheus = The Doctor

Now we introduce **Prometheus**.

Prometheus is essentially your monitoring doctor.

The doctor periodically checks the patient.

For example:

```text
Doctor:
"Heart rate?"

Patient:
"82."

Doctor:
"Temperature?"

Patient:
"37.1°C."

Doctor:
"Blood pressure?"

Patient:
"120/80."
```

Prometheus does something similar.

It asks your application:

```text
CPU?
Memory?
Requests?
Errors?
Latency?
```

The application responds with metrics.

---

# 6. Prometheus Does Not Usually Wait for the Application

This is one of the most important concepts.

Prometheus normally uses a **pull model**.

Think of a doctor making rounds.

Every 15 seconds:

```text
Doctor → Patient
        "Give me your vital signs."
```

The patient responds:

```text
CPU = 42
Memory = 61
Requests = 850
Errors = 3
```

Prometheus then records them.

This process is called **scraping**.

---

# 7. Scraping = Taking Vital Signs

Imagine the doctor visits a patient every 15 seconds.

At:

```text
12:00:00
12:00:15
12:00:30
12:00:45
12:01:00
```

At every visit, the doctor records the patient's condition.

Prometheus does the same.

For example:

```text
12:00:00 → CPU 40%
12:00:15 → CPU 43%
12:00:30 → CPU 48%
12:00:45 → CPU 65%
12:01:00 → CPU 82%
```

Now Prometheus knows:

> "The patient's CPU condition is getting worse."

This repeated collection is **scraping**.

---

# 8. Where Does Prometheus Get the Metrics?

Here's an important detail.

Your application needs to **expose its measurements**.

Imagine a patient wearing a medical monitor.

The monitor displays:

```text
Heart Rate: 82
Blood Pressure: 120/80
Oxygen: 98%
```

In software, an application can expose a special HTTP endpoint such as:

```text
/metrics
```

For example:

```text
http://application:8080/metrics
```

Prometheus visits that endpoint.

```text
Prometheus
     │
     │ GET /metrics
     ▼
Application
     │
     │ metrics
     ▼
Prometheus
```

---

# 9. The `/metrics` Endpoint

Imagine the patient's monitor has a screen containing:

```text
heart_rate 82
temperature 37.1
oxygen_saturation 98
```

Your application might expose something like:

```text
http_requests_total 15320
http_errors_total 42
process_cpu_seconds_total 1832
```

Prometheus reads these values.

So:

```text
Application
     ↓
/metrics
     ↓
Prometheus
```

---

# 10. But What If the Patient Can't Speak?

This is where **Exporters** become important.

Imagine an unconscious patient.

The doctor can't simply ask:

> "What's your heart rate?"

Instead, the patient is connected to medical equipment.

The equipment measures the vital signs.

Software has the same problem.

Some systems don't natively expose Prometheus metrics.

For example:

```text
Linux server
Windows server
MySQL
PostgreSQL
Nginx
Apache
Redis
```

You can use **exporters**.

---

# 11. Exporter = Medical Monitoring Device

Imagine a patient connected to:

- heart monitor
    
- blood pressure monitor
    
- oxygen monitor
    

These devices translate physical conditions into measurements a doctor can understand.

An exporter does something similar.

For example:

```text
Linux server
      ↓
Node Exporter
      ↓
Prometheus
```

Node Exporter exposes things such as:

```text
CPU
Memory
Disk
Network
Filesystem
Load
```

Prometheus then scrapes the exporter.

So:

```text
Linux Server
     │
     ▼
Node Exporter
"Medical Device"
     │
     ▼
Prometheus
"Doctor"
```

---

# 12. Multiple Patients

Now imagine the hospital has 100 patients.

Prometheus doesn't monitor just one.

It might monitor:

```text
Patient 1 → Web server
Patient 2 → Database
Patient 3 → Redis
Patient 4 → Payment service
Patient 5 → Authentication service
...
```

Prometheus periodically visits them.

```text
                PROMETHEUS
                  /  |  \
                 /   |   \
                ▼    ▼    ▼
             App   DB   Redis
             ↓     ↓     ↓
          metrics metrics metrics
```

---

# 13. Prometheus Stores the Medical Records

A doctor doesn't simply look at today's heart rate.

They want history.

For example:

```text
Monday:
Heart rate = 75

Tuesday:
Heart rate = 80

Wednesday:
Heart rate = 90

Thursday:
Heart rate = 105
```

Now the doctor can see a trend.

Prometheus also stores historical metric values.

For example:

```text
12:00 → CPU 40%
12:05 → CPU 45%
12:10 → CPU 52%
12:15 → CPU 70%
12:20 → CPU 90%
```

This is called **time-series data**.

---

# 14. What Is Time-Series Data?

A time series is basically:

> **A measurement + its value + when it happened.**

For example:

```text
CPU = 40% at 12:00
CPU = 45% at 12:01
CPU = 52% at 12:02
CPU = 70% at 12:03
```

Think:

```text
        PATIENT MEDICAL HISTORY

12:00 ── 70 bpm
12:01 ── 72 bpm
12:02 ── 75 bpm
12:03 ── 82 bpm
12:04 ── 95 bpm
```

Prometheus stores the equivalent for software.

---

# 15. Labels = Identifying Which Patient

Suppose Prometheus sees:

```text
http_requests_total 1000
```

Which application generated those 1000 requests?

That's where **labels** come in.

Imagine a hospital record saying:

```text
Heart Rate: 82
```

but not telling you **which patient**.

Useless.

Instead:

```text
Patient: Victor
Ward: ICU
Heart Rate: 82
```

Prometheus uses labels.

For example:

```text
http_requests_total{
    service="payment",
    method="GET",
    status="200"
}
```

Now we know what the measurement belongs to.

---

# 16. Labels Make Metrics Powerful

Suppose you have:

```text
payment
authentication
orders
products
```

Prometheus can distinguish them using labels.

For example:

```text
http_requests_total{service="payment"}
http_requests_total{service="orders"}
http_requests_total{service="authentication"}
```

It's like a hospital having:

```text
Heart rate — Patient A
Heart rate — Patient B
Heart rate — Patient C
```

---

# 17. PromQL = The Doctor Asking Questions

Now we get to one of Prometheus's most important features:

**PromQL — Prometheus Query Language.**

Think of PromQL as the doctor's language for interrogating medical records.

The doctor might ask:

> "Show me this patient's heart rate over the last 24 hours."

PromQL lets you ask similar questions.

For example:

```text
http_requests_total
```

means roughly:

> "Show me this metric."

You can ask much more complicated questions.

---

# 18. Example: CPU Usage

Imagine the doctor asks:

> "How much of the patient's brain activity is currently being used?"

You might query CPU-related metrics.

Prometheus can return:

```text
40%
45%
51%
62%
80%
```

Grafana can then turn that into a graph.

---

# 19. Grafana = The Hospital Dashboard

Now we introduce **Grafana**.

Prometheus is excellent at:

- collecting metrics
    
- storing metrics
    
- querying metrics
    
- evaluating alert rules
    

But Prometheus isn't primarily designed to give you beautiful dashboards.

That's where Grafana comes in.

Imagine an ICU.

There is a large screen showing:

```text
┌─────────────────────────────────────┐
│           PATIENT STATUS            │
│                                     │
│ Heart Rate       82 BPM             │
│ Blood Pressure   120/80             │
│ Oxygen           98%                │
│ Temperature      37.1°C             │
│                                     │
│ Heart Rate Trend                    │
│     ╱╲                              │
│  ──╯  ╲──────                      │
└─────────────────────────────────────┘
```

That's Grafana.

---

# 20. Grafana Doesn't Normally Collect the Data

This distinction is extremely important.

Think:

**Prometheus = doctor**

**Grafana = dashboard screen**

The screen isn't taking the patient's blood pressure.

It is **displaying information obtained from the medical records**.

Similarly:

```text
Application
     ↓
Prometheus
     ↓
Grafana
```

Prometheus collects.

Grafana visualizes.

---

# 21. Grafana Asks Prometheus Questions

Grafana connects to Prometheus as a **data source**.

Then Grafana says:

> "Prometheus, give me CPU usage for the last 24 hours."

Prometheus responds with the data.

Grafana turns it into:

```text
CPU
100% |                       ╭─╮
 80% |                 ╭─────╯ ╰
 60% |          ╭──────╯
 40% |──────────╯
     +--------------------------
       12  16  20  00  04  08
```

---

# 22. Dashboard = Patient's Medical Chart

A Grafana dashboard can contain many panels.

For example:

```text
┌────────────────┬────────────────┐
│ CPU            │ Memory         │
│ 72%            │ 64%            │
│ 📈             │ 📈             │
├────────────────┼────────────────┤
│ Requests/sec   │ Error rate     │
│ 1,250          │ 0.8%           │
│ 📈             │ 📈             │
├────────────────┴────────────────┤
│ Response Time                    │
│ 📈📈📈📈📈                         │
└──────────────────────────────────┘
```

This gives an engineer a quick view of the system's health.

---

# 23. Why Grafana Is So Useful

Imagine a doctor having to read:

```text
82
84
79
91
93
95
102
108
...
```

from a notebook.

That's difficult.

A graph makes the trend obvious:

```text
Heart Rate

110 |                 ╭──
100 |             ╭───╯
 90 |         ╭───╯
 80 |─────────╯
    +---------------------
       Time →
```

The doctor immediately sees:

> "Something started changing around here."

Grafana does exactly this for software.

---

# 24. Alerts = The Hospital Alarm

Now imagine the patient's heart rate reaches:

```text
180 BPM
```

The hospital shouldn't wait for the doctor to notice the dashboard.

An alarm should trigger.

Software monitoring works similarly.

You can define rules such as:

```text
IF CPU > 90%
FOR 5 minutes
THEN alert
```

or:

```text
IF error rate > 5%
THEN alert
```

or:

```text
IF service is down
THEN alert
```

---

# 25. Alerting Is Like an Emergency Alarm

Think:

```text
Patient condition
       ↓
Measurement
       ↓
Prometheus
       ↓
Alert rule
       ↓
Condition exceeded
       ↓
🚨 ALERT
```

For example:

```text
CPU > 90%
```

Prometheus checks the rule.

If it remains true for the specified period:

```text
🚨 HIGH CPU USAGE
Server: production-web-01
CPU: 96%
Duration: 5 minutes
```

---

# 26. Alertmanager = The Hospital Notification Department

There is another important component:

**Alertmanager.**

Think of Prometheus as the doctor who says:

> "This patient is in serious condition."

Alertmanager handles **what happens next**.

It can route notifications.

For example:

```text
Prometheus
    ↓
Alert
    ↓
Alertmanager
   / | \
  /  |  \
 ▼   ▼   ▼
Email Slack PagerDuty
```

So:

**Prometheus detects the problem.**

**Alertmanager manages the notification.**

---

# 27. Example of the Complete Flow

Suppose your production application suddenly receives huge traffic.

The sequence might be:

```text
Users
  ↓
Application
  ↓
CPU increases
  ↓
/metrics
  ↓
Prometheus scrapes
  ↓
Prometheus stores:
CPU = 95%
  ↓
Alert rule evaluates
  ↓
🚨 CPU > 90%
  ↓
Alertmanager
  ↓
Engineer receives notification
  ↓
Engineer opens Grafana
  ↓
Investigates the graphs
```

That's the entire monitoring cycle.

---

# 28. Grafana Helps Diagnose the Problem

Suppose you receive:

> 🚨 CPU usage above 90%

You open Grafana.

You might see:

```text
CPU
100% |                    ╭──────
 80% |              ╭─────╯
 60% |──────────────╯
     +---------------------------
        10:00  11:00  12:00
```

Then you inspect:

```text
Requests/sec
```

and see:

```text
500 → 1,000 → 5,000 → 10,000
```

Then:

```text
Latency
```

shows:

```text
100ms → 150ms → 400ms → 2s
```

Now you have evidence that traffic caused the CPU problem.

---

# 29. Four Important Prometheus Metric Types

Prometheus has four major metric types.

The medical analogy makes them easy to understand.

---

## 29.1 Counter = Total Number of Events

Imagine a hospital receptionist counting:

```text
Patients admitted today: 1,250
```

The number generally goes upward.

It doesn't normally decrease.

Software example:

```text
http_requests_total
```

means:

> Total number of HTTP requests.

It might go:

```text
100
200
300
400
500
```

This is a **Counter**.

Other examples:

```text
errors_total
login_attempts_total
orders_created_total
bytes_sent_total
```

---

# 30. Why Would a Counter Reset?

Suppose the hospital computer is restarted.

The receptionist's electronic counter may start again.

Similarly, an application restart can cause a counter to reset.

For example:

```text
Before restart:

http_requests_total = 50,000

Application restarts

After restart:

http_requests_total = 0
```

Prometheus is designed to handle this when calculating rates.

---

# 31. Gauge = Current Condition

Now consider:

```text
Patient's temperature = 38°C
```

It can go:

```text
37
38
39
38
37
```

It can increase or decrease.

That's a **Gauge**.

Software examples:

```text
memory_usage
cpu_temperature
active_connections
queue_length
```

For example:

```text
active_connections = 45
```

Then:

```text
active_connections = 72
```

Then:

```text
active_connections = 30
```

That's a gauge.

---

# 32. Histogram = Distribution of Measurements

Suppose a doctor doesn't only care about one patient's temperature.

They want to know:

> "How long does it take for patients to receive treatment?"

They might categorize treatment times:

```text
< 1 minute
< 5 minutes
< 10 minutes
< 30 minutes
```

A Prometheus **Histogram** does something similar.

For example, you could measure HTTP request duration.

```text
Request duration:

0–100ms     → 500 requests
100–500ms   → 300 requests
500ms–1s    → 100 requests
1–5s        → 50 requests
>5s         → 10 requests
```

This helps you understand latency distribution.

---

# 33. Summary = Statistical Summary

A **Summary** also tracks observations and can calculate quantiles.

Think of a hospital administrator asking:

> "What are typical patient waiting times?"

A summary can provide information such as:

```text
50th percentile
90th percentile
95th percentile
99th percentile
```

For example:

```text
p50 = 100ms
p95 = 400ms
p99 = 1.2s
```

This tells you much more than simply:

```text
Average = 200ms
```

---

# 34. Pull vs Push

This is another important concept.

Prometheus normally uses:

> **Pull**

Think of a doctor visiting patients.

```text
Doctor
  ↓
"Give me your vitals."
  ↓
Patient
```

Prometheus:

```text
Prometheus
    ↓
GET /metrics
    ↓
Application
```

---

# 35. Pushgateway

Sometimes you have short-lived jobs that disappear before Prometheus can scrape them.

Think about a patient who enters the hospital for only 10 seconds and leaves.

The doctor might miss them.

A system called **Pushgateway** can help certain batch jobs push metrics somewhere Prometheus can scrape.

Conceptually:

```text
Short-lived Job
      ↓
Push metrics
      ↓
Pushgateway
      ↓
Prometheus
```

But Pushgateway should **not** be treated as the default replacement for Prometheus's pull model.

---

# 36. Service Discovery = Finding Patients

Imagine a huge hospital.

Every morning, new patients arrive.

You don't want the doctor to manually type:

```text
Patient 1
Patient 2
Patient 3
...
Patient 10,000
```

You need a system that tells the doctor:

> "Here are today's patients."

Prometheus has **service discovery** mechanisms.

It can discover targets from environments such as:

```text
Kubernetes
AWS
Docker
Consul
EC2
file-based configurations
```

So:

```text
Service Discovery
       ↓
Find targets
       ↓
Prometheus
       ↓
Scrape targets
```

---

# 37. Static Configuration = Hospital Patient List

For a small hospital, you might simply give the doctor a list:

```text
web-server:9090
database:9100
redis:9121
```

Prometheus can similarly be configured with targets.

Example conceptually:

```yaml
scrape_configs:
  - job_name: "servers"
    static_configs:
      - targets:
          - "server1:9100"
          - "server2:9100"
```

This tells Prometheus:

> "These are the patients you should visit."

---

# 38. Jobs = Groups of Patients

Prometheus organizes scrape targets into **jobs**.

Imagine:

```text
Ward: Web Servers
Ward: Databases
Ward: Redis
Ward: Payment Services
```

Prometheus might have:

```text
job="web"
job="database"
job="redis"
job="payment"
```

This makes it easier to organize and query your monitoring data.

---

# 39. Instance = Specific Patient

Suppose the ward contains:

```text
Web Server 1
Web Server 2
Web Server 3
```

The **job** might be:

```text
web
```

while the **instance** identifies the specific server:

```text
server1:9100
server2:9100
server3:9100
```

Think:

```text
Job      = Ward
Instance = Specific patient
```

---

# 40. Uptime = Is the Patient Alive?

One of the simplest but most important questions:

> "Is the patient alive?"

Prometheus has the concept of whether a target was successfully scraped.

You might see:

```text
up = 1
```

meaning:

> Prometheus successfully reached the target.

And:

```text
up = 0
```

meaning:

> Prometheus couldn't successfully scrape it.

So:

```text
up = 1 → Patient responding
up = 0 → Patient may be unavailable
```

---

# 41. Monitoring vs Logging

This distinction is extremely important.

Imagine a hospital.

### Metrics

The monitor says:

```text
Heart rate = 120
Temperature = 39
Oxygen = 92
```

These are **metrics**.

### Logs

A nurse writes:

```text
14:32 — Patient complained of chest pain.
14:34 — Doctor administered medication.
14:40 — Patient reported improvement.
```

That's a **log**.

Metrics tell you:

> **What is happening?**

Logs often tell you:

> **What happened in detail?**

Prometheus is primarily a **metrics monitoring system**, not a general log-management system.

---

# 42. Monitoring vs Tracing

Now imagine you want to know:

> "Why did this particular patient experience a delay?"

You trace their journey:

```text
Reception
   ↓
Nurse
   ↓
Laboratory
   ↓
Doctor
   ↓
Pharmacy
```

In distributed software, **tracing** follows a request through multiple services.

For example:

```text
User
 ↓
API Gateway
 ↓
Order Service
 ↓
Payment Service
 ↓
Database
```

Prometheus provides metrics.

Tools such as **Jaeger** or **Grafana Tempo** are used for distributed tracing.

---

# 43. The Three Pillars

A common observability model is:

```text
        OBSERVABILITY
       /      |       \
      /       |        \
 Metrics     Logs     Traces
    |          |         |
Prometheus   Loki      Tempo
    \          |         /
     \         |        /
          Grafana
```

Grafana can visualize information from different observability systems.

---

# 44. Prometheus + Grafana in Microservices

Now imagine you have:

```text
                 USERS
                   │
                   ▼
              API Gateway
             /     |      \
            ▼      ▼       ▼
        Auth     Orders   Products
          │        │         │
          ▼        ▼         ▼
         DB       DB        Redis
```

Each service can expose metrics.

Prometheus collects them.

```text
Auth ────────┐
Orders ──────┤
Products ────┤
Gateway ─────┤
Database ────┤
Redis ───────┘
       ↓
  PROMETHEUS
       ↓
    GRAFANA
```

Now you can monitor the entire architecture.

---

# 45. Monitoring a Microservice

Suppose the Order Service exposes:

```text
orders_created_total
orders_failed_total
http_requests_total
http_request_duration
```

Prometheus collects them.

Grafana can show:

```text
Orders Created
      📈

Orders Failed
      📈

Requests/sec
      📈

Response Time
      📈
```

You can immediately see whether the service is healthy.

---

# 46. The Golden Signals

When monitoring applications, four particularly useful categories are often called the **Four Golden Signals**:

### 1. Latency

How long does the patient take to receive treatment?

Software:

```text
How long does a request take?
```

---

### 2. Traffic

How many patients are arriving?

Software:

```text
How many requests are arriving?
```

---

### 3. Errors

How many patients are experiencing problems?

Software:

```text
How many requests are failing?
```

---

### 4. Saturation

How close is the hospital to its capacity?

Software:

```text
How close are CPU, memory, disk, connections, etc. to capacity?
```

These four provide an excellent starting point for monitoring services.

---

# 47. Example Production Dashboard

Imagine Grafana showing:

```text
┌─────────────────────────────────────────────┐
│             PRODUCTION SYSTEM               │
├───────────────┬───────────────┬─────────────┤
│ CPU           │ Memory        │ Requests    │
│ 72%           │ 64%           │ 8,200/sec   │
├───────────────┼───────────────┼─────────────┤
│ Error Rate    │ Latency       │ Instances   │
│ 0.4%          │ 180ms         │ 12          │
├───────────────┴───────────────┴─────────────┤
│ CPU Over Time                                │
│          ╭──╮                                │
│     ╭────╯  ╰────╮                           │
│ ────╯             ╰────                      │
├─────────────────────────────────────────────┤
│ Request Rate                                 │
│ ─────╮    ╭────────────                      │
│      ╰────╯                                   │
└─────────────────────────────────────────────┘
```

An engineer can understand the system's condition within seconds.

---

# 48. The Complete Architecture

Put everything together:

```text
                         USERS
                           │
                           ▼
                    ┌─────────────┐
                    │ APPLICATION │
                    │   PATIENT   │
                    └──────┬──────┘
                           │
                       /metrics
                           │
                           ▼
                    ┌─────────────┐
                    │ PROMETHEUS  │
                    │   DOCTOR    │
                    └──────┬──────┘
                           │
                     stores data
                           │
                           ▼
                  ┌─────────────────┐
                  │ TIME-SERIES DB  │
                  │ MEDICAL RECORDS │
                  └────────┬────────┘
                           │
                           ▼
                    ┌─────────────┐
                    │   GRAFANA   │
                    │ ICU SCREEN  │
                    └─────────────┘
                           │
                           │
                    ┌──────┴──────┐
                    │             │
                    ▼             ▼
               Dashboards      Analysis
```

And alerts:

```text
Application
     │
     ▼
Prometheus
     │
     ├──────────────► Grafana
     │                 │
     │                 ▼
     │             Dashboard
     │
     ▼
 Alert Rule
     │
     ▼
Alertmanager
     │
 ┌───┼────┐
 ▼   ▼    ▼
Email Slack PagerDuty
```

---

# 49. The Most Important Difference

If you remember only one thing, remember this:

### Prometheus

**Collects, stores, queries and evaluates metrics.**

Think:

> **The doctor + medical records.**

### Grafana

**Visualizes data from Prometheus and other data sources.**

Think:

> **The hospital's monitoring screen.**

### Alertmanager

**Handles and routes alerts.**

Think:

> **The hospital emergency notification system.**

### Exporter

**Exposes metrics from systems that don't natively expose them.**

Think:

> **The medical monitoring equipment.**

---

# 50. A Real-Life Example

Imagine you run an e-commerce website.

At 10:00:

```text
Requests: 5,000/sec
CPU: 45%
Memory: 50%
Errors: 0.2%
Latency: 100ms
```

Everything looks healthy.

At 10:15, a viral social-media post sends huge traffic.

Now:

```text
Requests: 20,000/sec
CPU: 95%
Memory: 91%
Errors: 4%
Latency: 2.5 seconds
```

Prometheus keeps collecting these measurements.

Grafana displays the deterioration.

Your alert rule says:

```text
CPU > 90%
```

for 5 minutes.

Prometheus detects the condition.

Then:

```text
Prometheus
     ↓
🚨 CPU HIGH
     ↓
Alertmanager
     ↓
Engineer receives alert
     ↓
Engineer opens Grafana
     ↓
Sees traffic spike
     ↓
Investigates application
```

That's **observability in action**.

---

# 51. The Entire Analogy in One Story

Imagine Victor is a doctor responsible for a huge hospital.

The hospital contains hundreds of patients.

Each patient represents a software component.

Every patient has vital signs.

```text
Heart rate       → CPU
Blood pressure   → Memory
Breathing rate   → Requests/sec
Temperature      → System temperature
Pain level       → Error rate
Recovery time    → Request latency
```

Victor's job is to keep checking them.

**Prometheus is Victor's monitoring system.**

Every few seconds, it goes around the hospital asking:

> "Give me your current vital signs."

The applications expose those vital signs through `/metrics`.

Prometheus collects them and stores them with timestamps.

If a server doesn't know how to expose its own vital signs, an **exporter** acts like a medical device that measures them.

Prometheus can then ask questions using **PromQL**:

> "What is the CPU usage of the payment service?"

> "How many requests failed during the last five minutes?"

> "What is the request rate?"

> "Which server is down?"

**Grafana is the giant screen in the ICU.**

It takes the medical records collected by Prometheus and turns them into:

```text
graphs
charts
numbers
tables
gauges
heatmaps
alerts
```

Victor can look at the screen and immediately understand:

> "This patient is healthy."

or:

> "This patient's condition is deteriorating."

If something becomes dangerous, **Prometheus evaluates an alert rule**.

For example:

```text
CPU > 90% for 5 minutes
```

The alarm is triggered.

**Alertmanager** receives the alert and decides where to send it:

```text
Slack
Email
PagerDuty
etc.
```

The engineer receives the notification, opens Grafana, examines the graphs, and investigates the underlying problem.

---

# 52. The Mental Model You Should Keep

When learning Prometheus and Grafana, keep this picture in your head:

```text
                 🏥 SOFTWARE HOSPITAL

                   APPLICATION
                    "PATIENT"
                        │
                        │ exposes vitals
                        ▼
                   /metrics
                        │
                        ▼
              ┌───────────────────┐
              │    PROMETHEUS     │
              │     "DOCTOR"      │
              │                   │
              │ • Scrapes         │
              │ • Stores          │
              │ • Queries         │
              │ • Evaluates rules │
              └─────────┬─────────┘
                        │
                        │ data
                        ▼
              ┌───────────────────┐
              │      GRAFANA      │
              │   "ICU SCREEN"    │
              │                   │
              │ • Graphs          │
              │ • Dashboards      │
              │ • Tables          │
              │ • Visualization   │
              └───────────────────┘
                        
               🚨 WHEN SOMETHING
                    GOES WRONG
                        │
                        ▼
                 Alertmanager
               "EMERGENCY DESK"
                        │
                 ┌──────┼──────┐
                 ▼      ▼      ▼
               Email  Slack  PagerDuty
```

### In one sentence:

> **Prometheus is the doctor that continuously collects and analyzes the patient's vital signs; Grafana is the hospital dashboard that turns those medical records into visual information that humans can understand; exporters provide measurements for systems that cannot expose them directly; and Alertmanager delivers the emergency notifications when something goes wrong.**

That mental model will carry you through most of the Prometheus/Grafana concepts you'll encounter in **DevOps, SRE, Kubernetes, microservices, distributed systems, and production monitoring**.