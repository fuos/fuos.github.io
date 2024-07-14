---
title: springboot+redis实现滑动窗口限流
tags:
  - redis
  - springboot
categories:
  - spring
date: 2024-07-08 14:22:56
home_cover: https://fuos.github.io/picx-images-hosting/20240708/hdck.6wqlwrws37.webp
home_cover_height: 120
---

滑动窗口限流和固定窗口限流都是常见的限流策略，它们各有特点和适用场景。下面是两者的主要区别：  
![hdck](https://fuos.github.io/picx-images-hosting/20240708/hdck.6wqlwrws37.webp)

### 固定窗口限流（Fixed Window）

固定窗口限流将时间分割成等长的时间段（窗口），每个时间段内都有一个请求计数器。当请求到来时，会根据当前时间找到对应的时间段，并将该时间段内的请求计数器加一。如果在这个时间段内的请求次数超过了预设的阈值，则后续的请求会被限流（即拒绝服务）。每个时间段的计数器是独立的，当进入新的时间段时，计数器会重置。

- **优点**：实现简单，性能较好。
- **缺点**：在窗口切换的瞬间，会出现请求的瞬时双倍峰值，因为前一个窗口的最后一刻和后一个窗口的第一刻都允许通过最大数量的请求。

### 滑动窗口限流（Sliding Window）

滑动窗口限流是对固定窗口限流的一种改进。它通过在固定窗口的基础上增加一个“滑动”的效果，使得每次请求到来时，不仅考虑当前窗口内的请求计数，还要考虑前一个窗口的部分计数。这样，限流的决策就基于过去一段时间内的请求量，而不仅仅是当前时间段内的请求量。

- **优点**：相对于固定窗口限流，滑动窗口限流能更平滑地处理请求，避免了窗口切换时的请求峰值。
- **缺点**：实现相对复杂，对系统资源的消耗也可能更大。  
下面是核心代码，通过在zset中插入时间作为score，每次插入时判断时间窗口内的请求次数：
```java
    public boolean isAllowed(String key, long maxRequest, long windowInSeconds) {
        long currentTimeMillis = System.currentTimeMillis();
        long windowStartMillis = currentTimeMillis - windowInSeconds * 1000;
        String redisKey = "rate_limiter:" + key;

        // 清除窗口开始前的请求记录
        redisTemplate.opsForZSet().removeRangeByScore(redisKey, 0, windowStartMillis);

        // 获取当前窗口内的请求次数
        Long currentCount = redisTemplate.opsForZSet().count(redisKey, windowStartMillis, currentTimeMillis);

        if (currentCount != null && currentCount >= maxRequest) {
            // 如果当前窗口内的请求已达到上限，则不允许请求
            return false;
        } else {
            // 记录当前请求
            redisTemplate.opsForZSet().add(redisKey, currentTimeMillis, currentTimeMillis);
            // 设置过期时间，避免无限增长
            redisTemplate.expire(redisKey, Duration.ofSeconds(windowInSeconds + 1));
            return true;
        }
    }

```

springboot+redis实现滑动窗口限流完整代码：[ratelimit-demo](https://github.com/fuos/springboot3.x-demo/tree/master/ratelimit-demo)  

> 测试下图床，文章内容和代码由GPT生成🚀。