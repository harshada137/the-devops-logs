# Task 4 – Performance Monitoring and Cost Optimization

## Objective
Analyze an EC2 instance, collect performance metrics, identify optimization opportunities, and recommend a cost-effective solution.

---

# Prerequisites

- AWS Account
- Running EC2 Instance
- IAM permissions for:
  - EC2
  - CloudWatch
  - Cost Explorer
- CloudWatch Agent installed (for Memory and Disk metrics)

---

# Step 1 – Identify the EC2 Instance

1. Open the AWS Console.
2. Navigate to **EC2**.
3. Select the instance to analyze.
4. Note the following:
   - Instance ID
   - Instance Name
   - Instance Type
   - Region
   - Operating System

---

# Step 2 – Collect CPU Utilization

1. Go to **EC2 → Instances**.
2. Select the instance.
3. Open the **Monitoring** tab.
4. View the **CPU Utilization** graph.
5. Observe:
   - Average CPU
   - Peak CPU
   - Idle periods
6. Take a screenshot.

---

# Step 3 – Collect Memory Utilization

> Memory metrics require the CloudWatch Agent.

1. Open **CloudWatch**.
2. Navigate to **Metrics**.
3. Select **CWAgent**.
4. Open the Memory utilization metric.
5. Record:
   - Average Memory Usage
   - Peak Memory Usage
6. Take a screenshot.

---

# Step 4 – Analyze Disk Usage

### Using CloudWatch

1. Open **CloudWatch → Metrics → CWAgent**.
2. View Disk Used Percentage.

### OR Using SSH

```bash
df -h
```

Record:

- Total Storage
- Used Storage
- Available Storage
- Usage Percentage

Take a screenshot.

---

# Step 5 – Review Network Utilization

1. Open **EC2 → Monitoring**.
2. Review:
   - Network In
   - Network Out
3. Identify:
   - High traffic
   - Low traffic
4. Capture screenshots.

---

# Step 6 – Identify Underutilized Resources

Analyze the collected metrics.

Consider:

- CPU consistently below 20%
- Memory utilization is low
- Disk usage is minimal
- Low network traffic

Determine whether the instance is oversized for the workload.

---

# Step 7 – Recommend a Better Instance Type

Compare the current instance with a smaller or more suitable instance.

Example:

| Current | Recommended |
|----------|-------------|
| m6a.xlarge | c6a.large |

Consider:

- vCPU
- Memory
- Workload requirements
- Cost

Provide the reason for the recommendation.

---

# Step 8 – Estimate Monthly Cost Savings

1. Open **AWS Pricing Calculator**.
2. Compare:
   - Current Instance Cost
   - Recommended Instance Cost
3. Calculate:

```
Monthly Savings = Current Cost − Recommended Cost
```

Document the estimated savings.

---

# Step 9 – Prepare the Analysis Report

Include:

- Current instance details
- CPU utilization
- Memory utilization
- Disk usage
- Network usage
- Resource utilization analysis
- Recommended instance type
- Estimated monthly savings
- Justification

---

# Deliverables Checklist

- Monitoring screenshots
- CPU utilization metrics
- Memory utilization metrics
- Disk usage metrics
- Network utilization metrics
- Analysis report
- Cost optimization recommendation
- Instance type comparison
- Estimated monthly cost savings
- Justification for the recommendation

---

# Expected Outcome

- Performance metrics collected
- Resource utilization analyzed
- Underutilized resources identified
- Appropriate instance type recommended
- Estimated monthly cost savings calculated
- Complete optimization report prepared
