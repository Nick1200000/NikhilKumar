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
