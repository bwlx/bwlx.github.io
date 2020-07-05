---
layout: post
title:  "分布式锁"
---

```
  public boolean acquire(String lockKey, long expireMsecs) {
    while (true) {
      long expires = System.currentTimeMillis() + expireMsecs + 1;
      String expiresStr = String.valueOf(expires);
      if (redisTemplate.opsForValue().setIfAbsent(lockKey, expiresStr)) {
        LOG.info("acquire lockKey = {} success", lockKey);
        return true;
      } else {
        String currentValueStr = redisTemplate.opsForValue().get(lockKey);
        if (currentValueStr != null && Long.parseLong(currentValueStr) < System.currentTimeMillis()) {
          String oldValueStr = redisTemplate.opsForValue().getAndSet(lockKey, expiresStr);
          if (oldValueStr != null && oldValueStr.equals(currentValueStr)) {
            LOG.info("acquire lockKey = {} success", lockKey);
            return true;
          }
        }
      }
    }
  }

  public void release(String lockKey) {
    redisTemplate.delete(lockKey);
    LOG.info("release lockKey = {} success", lockKey);
  }
```

上面是一个简单的分布式锁的实现，多数场景可以使用，但存在以下问题：
1. 锁误解除
1. 不可重入
1. 超时解锁导致并发

[1]: https://xiaomi-info.github.io/2019/12/17/redis-distributed-lock/