# Performance Optimization Queries

This README contains SQL queries related to Performance Optimization.

## Hourly Request Counts

```
SELECT SUBSTR(time, 1, 13) AS hour_slot, COUNT(*) AS request_count 
FROM alb_logs 
GROUP BY SUBSTR(time, 1, 13) 
ORDER BY request_count DESC;
```

## Requests with Response Processing Time in the 99th Percentile

```
SELECT * 
FROM alb_logs 
WHERE response_processing_time > (SELECT approx_percentile(response_processing_time, 0.99) FROM alb_logs);
```

## Top URLs by Maximum Response Time

```
SELECT request_url, MAX(response_processing_time) AS max_response_time 
FROM alb_logs 
GROUP BY request_url 
ORDER BY max_response_time DESC 
LIMIT 10;
```

## Average Response Time by Target IP

```
SELECT target_ip, AVG(response_processing_time) AS avg_resp_time 
FROM alb_logs 
GROUP BY target_ip 
ORDER BY avg_resp_time DESC;
```

## Average Connection Time by Client IP

```
SELECT client_ip, AVG(request_processing_time) AS avg_connection_time
FROM alb_logs
GROUP BY client_ip
ORDER BY avg_connection_time ASC;
```

## Average Response Time by Target Group ARN

```
SELECT target_group_arn, AVG(response_processing_time) AS avg_resp_time
FROM alb_logs
GROUP BY target_group_arn
ORDER BY avg_resp_time DESC;
```

## Average Response Time by SSL Cipher

```
SELECT ssl_cipher, AVG(response_processing_time) AS avg_resp_time
FROM alb_logs
GROUP BY ssl_cipher
ORDER BY avg_resp_time DESC;
```

## Average Target Processing Time by Target IP

```
SELECT target_ip, AVG(target_processing_time) AS avg_target_time
FROM alb_logs
GROUP BY target_ip
ORDER BY avg_target_time DESC;
```

## Average Handshake Time by Target IP (502 Status)

```
SELECT target_ip, AVG(request_processing_time) AS avg_handshake_time
FROM alb_logs
WHERE elb_status_code = 502
GROUP BY target_ip
ORDER BY avg_handshake_time DESC;
```

## Average Response Time by ELB

```
SELECT elb, AVG(response_processing_time) AS avg_resp_time
FROM alb_logs
GROUP BY elb
ORDER BY avg_resp_time DESC;
```

## Count of Responses by Target IP and Status Code

```
SELECT target_ip, target_status_code, COUNT(*) AS code_count
FROM alb_logs
WHERE target_status_code != '-'
GROUP BY target_ip, target_status_code
ORDER BY code_count DESC;
```

## This query provides a count of responses grouped by target IP addresses and target status codes. Analyzing the distribution of response status codes for each target IP helps assess the health and performance of backend resources. It aids in identifying issues, such as high error rates or resource-specific problems.

```
SELECT sent_bytes, COUNT(*) AS size_count
FROM alb_logs
WHERE sent_bytes IS NOT NULL
GROUP BY sent_bytes
ORDER BY size_count DESC;
```

## Average Connection Time by Target IP

```
SELECT target_ip, AVG(request_processing_time) AS avg_connection_time
FROM alb_logs
GROUP BY target_ip
ORDER BY avg_connection_time ASC;
```

## Request Count by Target Group ARN

```
SELECT target_group_arn, COUNT(*) AS request_count
FROM alb_logs
GROUP BY target_group_arn
ORDER BY request_count DESC;
```

## Success and Failure Counts by Target IP

```
SELECT target_ip,
    COUNT(CASE WHEN target_status_code = '200' THEN 1 END) AS success_count,
    COUNT(CASE WHEN target_status_code != '200' THEN 1 END) AS failure_count
FROM alb_logs
GROUP BY target_ip
ORDER BY success_count DESC, failure_count ASC;
```

