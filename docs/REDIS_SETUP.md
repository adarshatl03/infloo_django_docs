# Redis Setup for WSL/Linux

Since we have switched to `RedisChannelLayer` for robust WebSocket handling, you need to have a Redis server running.

## 1. Install Redis
Open your WSL terminal and run:
```bash
sudo apt-get update
sudo apt-get install redis-server
```

## 2. Start Redis
```bash
sudo service redis-server start
```

## 3. Verify
Check if it's running:
```bash
redis-cli ping
# Output should be: PONG
```

## 4. Troubleshooting
If you cannot connect, ensure the service is started.
In some WSL configurations, you might need to run it in the foreground for debugging:
```bash
redis-server
```
