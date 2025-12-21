# Infrastructure Guide

## 1. Redis (Essential for WebSockets)
Redis is required for the `RedisChannelLayer` used in Chat and Notifications.

### Installation (WSL/Ubuntu)
```bash
sudo apt-get update
sudo apt-get install redis-server
sudo service redis-server start
```
### Verification
```bash
redis-cli ping
# Output: PONG
```

## 2. PostgreSQL
Configure your database URL in `config.py` in the format:
`postgres://USER:PASSWORD@HOST:PORT/NAME`

## 3. AWS S3 (Optional)
To use S3 for Static/Media files:
1. Set `USE_S3 = True` in `config.py`.
2. Provide `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, and `AWS_STORAGE_BUCKET_NAME`.
