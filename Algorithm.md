# 刷题Tips

原则： 
1. 题目看清楚
2. 思路理顺：全面，完备，目标是AC，不要有投机心态
3. **保持做题的感觉**

## 小的积累点

### 用流Stream的方法来得到数组最大/小值

```java
Arrays.stream(dp[A.length]).min().getAsInt();
```

但是相比用max来轮询搜索，Runtime暴涨