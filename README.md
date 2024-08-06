# Log Data Generation and Analysis

This project involves generating a large dataset of log entries, aggregating the logs, and detecting anomalies using Python.

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
