
redis-cli -h {{host}} -p {{port}} keys '{{key pattern}}' | xargs redis-cli -h {{host}} -p {{port}} del