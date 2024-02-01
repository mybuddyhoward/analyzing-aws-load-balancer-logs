# Cost Optimization Queries

This README contains SQL queries related to Cost Optimization.

## Request Count for Unused Target Groups

```
SELECT target_group_arn, COUNT(*) AS request_count 
FROM alb_logs 
WHERE target_group_arn NOT IN (
    SELECT DISTINCT target_group_arn 
    FROM alb_logs 
    WHERE target_ip != '-'
) 
GROUP BY target_group_arn 
ORDER BY request_count DESC
```

## Request Count for HTTP/2 (h2) Traffic

```
SELECT type, COUNT(*) AS request_count 
FROM alb_logs 
WHERE type = 'h2' 
GROUP BY type
```

## Count of Redirects by Redirect URL

```
SELECT redirect_url, COUNT(*) AS redirect_count 
FROM alb_logs 
WHERE elb_status_code = 302 OR elb_status_code = 301 
GROUP BY redirect_url 
ORDER BY redirect_count DESC
```

## Top Target IPs by Request Count

```
SELECT target_ip, COUNT(*) AS request_count 
FROM alb_logs 
GROUP BY target_ip 
ORDER BY request_count DESC 
LIMIT 10
```

## Usage Count of Outdated SSL Protocols

```
SELECT ssl_protocol, COUNT(*) AS protocol_count 
FROM alb_logs 
WHERE ssl_protocol IN ('TLSv1', 'SSLv3') 
GROUP BY ssl_protocol 
ORDER BY protocol_count DESC
```

## Count of Unavailable Targets by Target IP

```
SELECT target_ip, SUM(CASE WHEN target_status_code = '-' THEN 1 ELSE 0 END) AS unavailable_count 
FROM alb_logs 
GROUP BY target_ip 
ORDER BY unavailable_count DESC
```

## Count of Healthy Targets by Target Group ARN

```
SELECT target_group_arn, COUNT(*) AS healthy_targets 
FROM alb_logs 
WHERE target_status_code != '-' 
GROUP BY target_group_arn 
ORDER BY healthy_targets DESC
```

## Target IPs with Fewer than 10 Requests

```
SELECT target_ip, COUNT(*) AS request_count 
FROM alb_logs 
GROUP BY target_ip 
HAVING COUNT(*) < 10 
ORDER BY request_count ASC
```

## SSL Protocol Usage Count

```
SELECT ssl_protocol, COUNT(*) AS protocol_count 
FROM alb_logs 
WHERE ssl_protocol IS NOT NULL 
GROUP BY ssl_protocol 
ORDER BY protocol_count DESC
```

## Clients with More than 1000 Requests

```
SELECT client_ip, COUNT(*) AS request_count 
FROM alb_logs 
GROUP BY client_ip 
HAVING COUNT(*) > 1000 
ORDER BY request_count DESC
```

## Total Number of HTTPS Redirects

```
SELECT COUNT(*) AS https_redirects 
FROM alb_logs 
WHERE elb_status_code = 301 OR elb_status_code = 302
```

## Total Request Count by Target IP

```
SELECT target_ip, COUNT(*) AS request_count 
FROM alb_logs 
GROUP BY target_ip 
ORDER BY request_count DESC
```

## Average Response Time for Redirects by Redirect URL

```
SELECT redirect_url, AVG(response_processing_time) AS avg_resp_time 
FROM alb_logs 
WHERE elb_status_code IN (301, 302) 
GROUP BY redirect_url 
ORDER BY avg_resp_time DESC
```

## Average Target Processing Time by Target Group ARN

```
SELECT target_group_arn, AVG(target_processing_time) AS avg_routing_time 
FROM alb_logs 
GROUP BY target_group_arn 
ORDER BY avg_routing_time ASC
```

