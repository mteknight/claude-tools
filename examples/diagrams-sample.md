# Diagrams Sample

Sample document showing the diagrams-generator skill in action: flowchart, pie, and bar, each as a simple ASCII version and a bigger Mermaid version.

## Table of Contents

- [Flowchart](#flowchart)
- [Pie Chart](#pie-chart)
- [Bar Chart](#bar-chart)

---

## Flowchart

### Simple (ASCII)

```
[Start] --> [Validate] --> [Process] --> [End]
                |
                v
            [Error]
```

### Bigger (Mermaid)

```mermaid
flowchart TD
    A[Receive order] --> B{Payment valid?}
    B -->|Yes| C[Reserve stock]
    B -->|No| D[Notify customer]
    C --> E{Stock available?}
    E -->|Yes| F[Charge payment]
    E -->|No| G[Backorder item]
    F --> H[Generate invoice]
    G --> I[Notify customer]
    H --> J[Ship order]
    J --> K[Send tracking email]
    K --> L[End]
    D --> L
    I --> L
```

---

## Pie Chart

### Simple (ASCII)

```
Browser Share
Chrome  [##########........] 50%
Safari  [######............] 30%
Other   [####..............] 20%
```

### Bigger (Mermaid)

```mermaid
pie title Cloud Spend by Service
    "Compute" : 42
    "Storage" : 18
    "Networking" : 15
    "Database" : 14
    "Monitoring" : 6
    "Other" : 5
```

---

## Bar Chart

### Simple (ASCII)

```
Requests per hour
09h [####______] 40
10h [#######___] 70
11h [##########] 100
12h [######____] 60
```

### Bigger (Mermaid)

```mermaid
xychart-beta
    title "Monthly Active Users"
    x-axis [Jan, Feb, Mar, Apr, May, Jun, Jul]
    y-axis "Users (thousands)" 0 --> 120
    bar [45, 52, 61, 68, 79, 95, 110]
```
