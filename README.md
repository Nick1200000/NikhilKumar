# Log Data Generation and Analysis
## Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Setup](#setup)
- [Usage](#usage)
  - [Generating Log Data](#generating-log-data)
  - [Aggregating Log Data](#aggregating-log-data)
  - [Detecting Anomalies](#detecting-anomalies)
- [Examples](#examples)
- [License](#license)

## Overview

The project consists of three main parts:
1. Generating log data
2. Aggregating the log data
3. Detecting anomalies in the log data

## Features
- **Log Data Generation**: Creates realistic log entries using `faker`.
- **Log Aggregation**: Summarizes the log data.
- **Anomaly Detection**: Identifies spikes in error logs.

## Prerequisites
- Python 3.x
- `faker` library

## Setup

1. **Clone the repository**:
    ```bash
    git clone https://github.com/yourusername/log-data-analysis.git
    cd log-data-analysis
    ```

2. **Install dependencies**:
    ```bash
    pip install faker
    ```

## Usage

### Generating Log Data

Run the following script to generate log data:

```python
import json
import random
from faker import Faker
from datetime import datetime, timedelta

fake = Faker()
random.seed(42)

NUM_LOGS = 50000
LOG_SERVICES = ['auth', 'payment']
LOG_LEVELS = ['INFO', 'WARN', 'ERROR']
USER_IDS = list(range(100, 200))
ORDER_IDS = list(range(500, 600))

def generate_log_entry(timestamp):
    service = random.choice(LOG_SERVICES)
    level = random.choice(LOG_LEVELS)
    
    if service == 'auth':
        user_id = random.choice(USER_IDS)
        message = 'User login' if level == 'INFO' else 'Failed login'
        context = {"user_id": user_id}
    else:
        order_id = random.choice(ORDER_IDS)
        message = 'Payment processed' if level == 'INFO' else 'Payment delay'
        context = {"order_id": order_id}
    
    return {
        "timestamp": timestamp.isoformat() + "Z",
        "service": service,
        "level": level,
        "message": message,
        "context": context
    }

def generate_logs(filename, num_logs):
    start_time = datetime(2024, 8, 3, 12, 0, 0)
    
    with open(filename, 'w') as f:
        for i in range(num_logs):
            timestamp = start_time + timedelta(seconds=i)
            log_entry = generate_log_entry(timestamp)
            f.write(json.dumps(log_entry) + '\n')

generate_logs('logs.json', NUM_LOGS)
```

### Aggregating Log Data

Run the following script to generate log data:

```python
import json
from collections import defaultdict
from datetime import datetime

def aggregate_logs(filename):
    log_summary = {
        "total_logs": 0,
        "levels": defaultdict(int),
        "unique_users": set(),
        "unique_orders": set()
    }

    with open(filename, 'r') as f:
        for line in f:
            log = json.loads(line)
            log_summary["total_logs"] += 1
            log_summary["levels"][log["level"]] += 1

            context = json.loads(log["context"])
            if "user_id" in context:
                log_summary["unique_users"].add(context["user_id"])
            if "order_id" in context:
                log_summary["unique_orders"].add(context["order_id"])

    log_summary["total_unique_users"] = len(log_summary["unique_users"])
    log_summary["total_unique_orders"] = len(log_summary["unique_orders"])
    return log_summary

summary = aggregate_logs('logs.json')
print("Log Summary Report:")
print(f"- Total Logs: {summary['total_logs']}")
print(f"- INFO: {summary['levels']['INFO']}")
print(f"- WARN: {summary['levels']['WARN']}")
print(f"- ERROR: {summary['levels']['ERROR']}")
print(f"- Total Unique Users: {summary['total_unique_users']}")
print(f"- Total Unique Orders: {summary['total_unique_orders']}")

```

### Detecting Anomalies

Run the following script to generate log data:

```python
import json
from collections import defaultdict
from datetime import datetime, timedelta

def detect_anomalies(filename):
    log_counts = defaultdict(lambda: defaultdict(lambda: defaultdict(int)))
    timestamp_format = "%Y-%m-%dT%H:%M:%SZ"
    one_minute = timedelta(minutes=1)

    # Read the log file and count logs
    with open(filename, 'r') as f:
        for line in f:
            log = json.loads(line)
            timestamp = datetime.strptime(log["timestamp"], timestamp_format)
            minute = timestamp.replace(second=0, microsecond=0)
            log_counts[minute][log["service"]][log["level"]] += 1

    # Detect anomalies
    anomalies = []
    for minute, services in log_counts.items():
        for service, levels in services.items():
            if levels["ERROR"] > 10:  # Example threshold for anomalies
                anomalies.append(f"{minute.isoformat()}Z: Spike in ERROR logs from '{service}' service")

    return anomalies

anomalies = detect_anomalies('logs.json')
print("Anomalies Detected:")
for anomaly in anomalies:
    print(f"- {anomaly}")

```

## Log Summary Report
- Total Logs: 50000
- INFO: 16914
- WARN: 16664
- ERROR: 16422
- Total Unique Users: 100
- Total Unique Orders: 100

## Anomalies Detected

- 2024-08-03T12:00:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:02:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:02:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:05:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:06:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:08:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:09:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:10:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:11:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:12:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:13:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:15:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:17:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:17:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:18:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:22:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:23:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:23:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:24:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:25:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:25:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:26:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:27:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:28:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:29:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:32:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:32:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:38:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:38:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:39:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:41:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:43:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:44:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:44:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:45:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:46:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:48:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:50:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:52:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:55:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:56:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T12:57:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:58:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T12:59:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:00:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:03:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:04:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:06:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:09:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:12:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:13:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:14:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:14:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:16:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:17:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:18:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:19:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:20:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:20:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:21:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:22:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:22:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:23:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:24:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:26:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:27:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:28:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:30:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:31:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:31:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:33:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:33:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:34:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:35:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:36:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:36:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:37:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:37:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:40:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:41:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:42:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:43:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:45:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:46:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:49:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:50:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:51:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:53:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:55:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T13:56:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:57:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:59:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T13:59:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:01:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:01:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:02:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:03:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:04:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:04:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:05:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:10:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:11:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:12:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:12:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:13:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:13:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:15:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:16:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:20:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:21:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:22:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:24:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:25:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:26:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:27:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:28:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:29:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:29:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:30:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:32:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:33:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:36:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:37:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:38:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:39:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:40:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:45:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:50:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:51:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:51:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:52:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:53:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:54:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:55:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:56:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:57:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:57:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:58:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T14:59:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T14:59:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:02:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:03:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:03:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:04:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:05:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:06:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:08:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:09:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:12:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:13:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:14:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:16:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:17:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:18:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:18:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:19:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:19:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:20:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:21:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:21:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:23:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:24:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:25:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:28:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:29:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:30:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:31:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:31:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:32:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:33:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:33:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:34:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:34:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:35:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:36:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:37:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:39:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:41:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:43:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:44:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:45:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:47:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:48:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:50:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:50:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:51:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:51:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:55:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:56:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:56:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T15:57:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T15:59:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T16:01:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T16:02:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T16:03:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T16:04:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T16:07:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T16:08:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T16:09:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T16:10:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T16:11:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T16:12:00Z: Spike in ERROR logs from 'auth' service
- 2024-08-03T16:12:00Z: Spike in ERROR logs from 'payment' service
- 2024-08-03T16:13:00Z: Spike in ERROR logs from 'auth' service













