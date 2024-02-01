# Security & Compliance Queries

This README contains SQL queries related to Security & Compliance.

## Large Outgoing Responses

```
SELECT *
FROM alb_logs
WHERE sent_bytes > (SELECT APPROX_PERCENTILE(sent_bytes, 0.99) FROM alb_logs);
```

## Suspiciously Large Request Sizes

```
SELECT *
FROM alb_logs
WHERE received_bytes > (SELECT APPROX_PERCENTILE(received_bytes, 0.99) FROM alb_logs);
```

## User Agents Associated with Bots and Crawlers

```
SELECT user_agent, COUNT(*) AS user_count
FROM alb_logs
WHERE user_agent LIKE '%bot%' OR user_agent LIKE '%crawler%' OR user_agent LIKE '%spider%'
GROUP BY user_agent
ORDER BY user_count DESC
LIMIT 10;
```

## URLs with the Most Redirects (HTTP/HTTPS)

```
SELECT redirect_url, COUNT(*) AS redirect_count
FROM alb_logs
WHERE redirect_url != '-' AND (redirect_url LIKE 'http:%' OR redirect_url LIKE 'https:%')
GROUP BY redirect_url
ORDER BY redirect_count DESC
LIMIT 10;
```

## Cross-site Scripting (XSS) Attempts

```
SELECT COUNT(*) AS xss_attempts
FROM alb_logs
WHERE request_url LIKE '%<script>%' OR request_url LIKE '%javascript:%';
```

## Count of HTTP Requests

```
SELECT COUNT(*) AS http_requests
FROM alb_logs
WHERE type = 'http';
```

## User Agents with Single Occurrence

```
SELECT user_agent, COUNT(*) AS user_count
FROM alb_logs
GROUP BY user_agent
HAVING COUNT(*) = 1
ORDER BY user_count DESC
LIMIT 10;
```

## Requests with 4xx ELB Status Codes

```
SELECT request_url, elb_status_code
FROM alb_logs
WHERE elb_status_code >= 400 AND elb_status_code < 500;
```

## Count of Failed Authentication Attempts (401 Status)

```
SELECT COUNT(*) AS failed_auth_attempts
FROM alb_logs
WHERE elb_status_code = 401;
```

## Count of Requests by HTTP Request Methods

```
SELECT request_verb, COUNT(*) AS method_count
FROM alb_logs
GROUP BY request_verb
ORDER BY method_count ASC;
```

## Suspicious Target IPs

```
SELECT target_ip, COUNT(*) AS request_count
FROM alb_logs
WHERE target_ip != '-'
GROUP BY target_ip
HAVING COUNT(*) > 500
ORDER BY request_count DESC;
```

## Missing User Agent

```
SELECT COUNT(*) AS no_user_agent
FROM alb_logs
WHERE user_agent = '-';
```

## Count of Access Denied Requests (403 Status)

```
SELECT request_url, COUNT(*) AS access_count
FROM alb_logs
WHERE elb_status_code = 403
GROUP BY request_url
ORDER BY access_count DESC
LIMIT 10;
```

## Count of Failed Requests to Target Groups

```
SELECT target_group_arn, COUNT(*) AS failed_count
FROM alb_logs
WHERE target_status_code = '-'
GROUP BY target_group_arn
ORDER BY failed_count DESC;
```

## Suspicious Redirect Chains

```
SELECT request_url, redirect_url, COUNT(*) AS redirect_count
FROM alb_logs
WHERE elb_status_code IN (301, 302)
GROUP BY request_url, redirect_url
ORDER BY redirect_count DESC;
```

## Abnormal Status Code Sequences

```
SELECT client_ip, elb_status_code, LAG(elb_status_code) OVER (PARTITION BY client_ip ORDER BY time) AS prev_status
FROM alb_logs
WHERE client_ip IS NOT NULL AND elb_status_code IS NOT NULL;
```

## Count of SSL Ciphers in Use

```
SELECT ssl_cipher, COUNT(*) AS cipher_count
FROM alb_logs
WHERE ssl_cipher IS NOT NULL
GROUP BY ssl_cipher
ORDER BY cipher_count ASC;
```

