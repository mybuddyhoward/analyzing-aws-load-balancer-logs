# Business Insights Queries

This README contains SQL queries related to Business Insights.

## Identifying Outlier Request Sizes

```
SELECT * 
FROM alb_logs 
WHERE received_bytes > (
    SELECT approx_percentile(received_bytes, 0.99) AS percentile 
    FROM alb_logs
)
```

## Analyzing Request Processing Time

```
SELECT 
    approx_percentile(request_processing_time, 0.5) AS median_request_duration, 
    approx_percentile(request_processing_time, 0.95) AS p95_request_duration, 
    approx_percentile(request_processing_time, 0.99) AS p99_request_duration 
FROM alb_logs
```

## Counting Unique Client IPs

```
SELECT client_ip, COUNT(DISTINCT client_ip) AS num_clients 
FROM alb_logs 
GROUP BY client_ip
```

## Top Accessed URLs

```
SELECT request_url, COUNT(*) AS access_count 
FROM alb_logs 
GROUP BY request_url 
ORDER BY access_count DESC 
LIMIT 10
```

## Hourly Traffic Distribution

```
SELECT SUBSTRING(time, 12, 2) AS hour, COUNT(*) AS request_count 
FROM alb_logs 
GROUP BY SUBSTRING(time, 12, 2) 
ORDER BY hour
```

## Top Requesting Client IPs

```
SELECT client_ip, COUNT(*) AS requests 
FROM alb_logs 
GROUP BY client_ip 
ORDER BY requests DESC 
LIMIT 10
```

## Most Common Request Methods

```
SELECT request_verb, COUNT(*) AS request_count 
FROM alb_logs 
GROUP BY request_verb 
ORDER BY request_count DESC
```

## Top User Agents

```
SELECT user_agent, COUNT(*) AS user_count 
FROM alb_logs 
GROUP BY user_agent 
ORDER BY user_count DESC 
LIMIT 10
```

## Most Common Load Balancer Status Codes

```
SELECT elb_status_code, COUNT(*) AS status_count 
FROM alb_logs 
GROUP BY elb_status_code 
ORDER BY status_count DESC
```

## Top Target IPs

```
SELECT target_ip, COUNT(*) AS target_count 
FROM alb_logs 
GROUP BY target_ip 
ORDER BY target_count DESC 
LIMIT 10
```

## Average Target Processing Time by IP

```
SELECT target_ip, AVG(target_processing_time) AS avg_processing_time 
FROM alb_logs 
GROUP BY target_ip 
ORDER BY avg_processing_time DESC
```

## Request Count by Hour

```
SELECT SUBSTRING(time, 1, 13) AS hour, COUNT(*) AS request_count 
FROM alb_logs 
GROUP BY SUBSTRING(time, 1, 13) 
ORDER BY hour
```

## Request Count by Type

```
SELECT type, COUNT(*) AS request_count 
FROM alb_logs 
GROUP BY type
```

## Most Common Redirect URLs

```
SELECT redirect_url, COUNT(*) AS redirect_count 
FROM alb_logs 
WHERE redirect_url != '-' 
GROUP BY redirect_url 
ORDER BY redirect_count DESC 
LIMIT 10
```

## Average, Maximum, and Minimum Request Size

```
SELECT 
    AVG(received_bytes) AS avg_request_size, 
    MAX(received_bytes) AS max_request_size, 
    MIN(received_bytes) AS min_request_size 
FROM alb_logs
```

## Average, Maximum, and Minimum Response Size

```
SELECT 
    AVG(sent_bytes) AS avg_response_size, 
    MAX(sent_bytes) AS max_response_size, 
    MIN(sent_bytes) AS min_response_size 
FROM alb_logs
```

## Client Error Status Codes (4xx)

```
SELECT 
    elb_status_code, COUNT(*) AS status_count 
FROM alb_logs 
WHERE elb_status_code >= 400 AND elb_status_code < 500 
GROUP BY elb_status_code 
ORDER BY status_count DESC
```

## Server Error Status Codes (5xx)

```
SELECT 
    elb_status_code, COUNT(*) AS status_count 
FROM alb_logs 
WHERE elb_status_code >= 500 AND elb_status_code < 600 
GROUP BY elb_status_code 
ORDER BY status_count DESC
```

## Count of Unavailable Targets

```
SELECT COUNT(*) AS unavailable_count 
FROM alb_logs 
WHERE target_status_code = '-'
```

