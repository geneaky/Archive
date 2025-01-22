
redis-cli -h {{host}} -p {{port}} keys '{{key patt}}' | xargs redis-cli -h waiker-app-live-redis.1bxd8o.ng.0001.apn2.cache.amazonaws.com -p 6379 del