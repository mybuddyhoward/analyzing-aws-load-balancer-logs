# Error Analysis Queries

This README contains SQL queries related to Error Analysis.

## Error Count by Hour Slot Excluding HTTP 200 Status

```
SELECT SUBSTR(time, 1, 13) AS hour_slot, COUNT(*) AS error_count 
FROM alb_logs 
WHERE elb_status_code != 200 
GROUP BY SUBSTR(time, 1, 13) 
ORDER BY hour_slot ASC
```

## Error Count for 5xx HTTP Status Codes

```
SELECT elb_status_code, COUNT(*) AS error_count 
FROM alb_logs 
WHERE CAST(elb_status_code AS VARCHAR) LIKE '5%' 
GROUP BY elb_status_code 
ORDER BY error_count DESC
```

## Error Count by Status Code and Hour Slot Excluding HTTP 200 Status

```
SELECT elb_status_code, SUBSTR(time, 1, 13) AS hour_slot, COUNT(*) AS error_count 
FROM alb_logs 
WHERE elb_status_code != 200 
GROUP BY elb_status_code, SUBSTR(time, 1, 13) 
ORDER BY hour_slot ASC
```

## Success and Failure Counts by Status Code

```
SELECT 
  elb_status_code, 
  SUM(CASE WHEN CAST(elb_status_code AS VARCHAR) = '200' THEN 1 ELSE 0 END) AS success_count, 
  SUM(CASE WHEN CAST(elb_status_code AS VARCHAR) != '200' THEN 1 ELSE 0 END) AS failure_count 
FROM alb_logs 
GROUP BY elb_status_code 
ORDER BY success_count DESC, failure_count ASC
```

## Count of Non-HTTP 200 Responses with Sent Bytes by Hour Slot

```
SELECT SUBSTR(time, 12, 2) AS hour_slot, COUNT(*) AS size_count 
FROM alb_logs 
WHERE elb_status_code != 200 AND sent_bytes IS NOT NULL 
GROUP BY SUBSTR(time, 12, 2) 
ORDER BY hour_slot ASC;
```

## Error Count by Target IP and Hour Slot for Unavailable Targets

```
SELECT target_ip, SUBSTR(time, 12, 2) AS hour_slot, COUNT(*) AS error_count 
FROM alb_logs 
WHERE target_status_code = '-' 
GROUP BY target_ip, SUBSTR(time, 12, 2) 
ORDER BY hour_slot ASC;
```

## Top Client IPs by Error Count

```
SELECT client_ip, COUNT(*) AS error_count 
FROM alb_logs 
WHERE elb_status_code != 200 
GROUP BY client_ip 
ORDER BY error_count DESC 
LIMIT 10;
```

## Error Count by Target Group ARN for Unavailable Targets

```
SELECT target_group_arn, COUNT(*) AS error_count 
FROM alb_logs 
WHERE target_status_code = '-' 
GROUP BY target_group_arn 
ORDER BY error_count DESC;
```

## Count of Sent Bytes for Non-HTTP 200 Responses

```
SELECT sent_bytes, COUNT(*) AS size_count 
FROM alb_logs 
WHERE elb_status_code != 200 AND sent_bytes IS NOT NULL 
GROUP BY sent_bytes 
ORDER BY size_count DESC;
```

## Top Error Counts by Request URL and ELB Status Code

```
SELECT request_url, elb_status_code, COUNT(*) AS error_count 
FROM alb_logs 
WHERE elb_status_code != 200 
GROUP BY request_url, elb_status_code 
ORDER BY error_count DESC 
LIMIT 10;
```

## Top Target IPs by Unavailability Count

```
SELECT target_ip, COUNT(*) AS error_count 
FROM alb_logs 
WHERE target_status_code = '-' 
GROUP BY target_ip 
ORDER BY error_count DESC 
LIMIT 10;
```

## Failed SSL Handshakes

```
SELECT COUNT(*) AS handshake_failures 
FROM alb_logs 
WHERE elb_status_code = 502;
```

## Count of Target Status Codes for Non-Hyphen (-) Codes

```
SELECT target_status_code, COUNT(*) AS error_count 
FROM alb_logs 
WHERE target_status_code != '-' 
GROUP BY target_status_code 
ORDER BY error_count DESC;
```

## Top URLs by Maximum Response Time (Excluding 200 Status)

```
SELECT request_url, MAX(response_processing_time) AS max_response_time 
FROM alb_logs 
WHERE elb_status_code != 200 
GROUP BY request_url 
ORDER BY max_response_time DESC 
LIMIT 10;
```

## Count of Requests with HTTP/2 (h2) Type and Non-200 Status

```
SELECT type, COUNT(*) AS error_count 
FROM alb_logs 
WHERE type = 'h2' AND elb_status_code != 200 
GROUP BY type;
```

## Count of ELB Status Codes for Non-200 Status

```
SELECT elb_status_code, COUNT(*) AS error_count 
FROM alb_logs 
WHERE elb_status_code != 200 
GROUP BY elb_status_code 
ORDER BY error_count DESC;
```

## Top Client IPs by Handshake Failures (502 Status)

```
SELECT client_ip, COUNT(*) AS handshake_failures 
FROM alb_logs 
WHERE elb_status_code = 502 
GROUP BY client_ip 
ORDER BY handshake_failures DESC 
LIMIT 10;
```

## Top Client IPs and ELB Status Codes by Error Count (Excluding 200 Status)

```
SELECT client_ip, elb_status_code, COUNT(*) AS error_count 
FROM alb_logs 
WHERE elb_status_code != 200 
GROUP BY client_ip, elb_status_code 
ORDER BY error_count DESC 
LIMIT 10;
```

