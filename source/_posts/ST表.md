# 怀念逝去的 LaTeX

# ST 表基本概念

ST 表（Sparse Table）是一种用于解决静态区间查询问题的数据结构，主要用于处理可重复贡献问题，比如区间最大值（RMQ）、区间最小值、区间 GCD 等。

## 特性
- 静态数据结构，建表后不能修改原始数据
- 预处理时间复杂度：O(n log n)
- 查询时间复杂度：O(1)

## 构建步骤

### 1. 预处理对数数组
```cpp
lg2[1] = 0;
for (int i = 2; i <= n; i++) {
    lg2[i] = lg2[i / 2] + 1;
}
```
这个预处理可以在 O(1) 时间内找到任意区间长度对应的最大幂次。

### 2. 初始化 ST 表
```cpp
for (int i = 1; i <= n; i++) {
    st_max[i][0] = arr[i];  // 区间长度为 1 时的最大值就是元素本身
    st_min[i][0] = arr[i];  // 同理最小值
}
```

### 3. 动态规划填充 ST 表
```cpp
for (int j = 1; j < 20; j++) {
    for (int i = 1; i + (1 << j) - 1 <= n; i++) {
        st_max[i][j] = max(st_max[i][j - 1], st_max[i + (1 << (j - 1))][j - 1]);
        st_min[i][j] = min(st_min[i][j - 1], st_min[i + (1 << (j - 1))][j - 1]);
    }
}
```
使用倍增思想，每个状态 st[i][j] 表示从 i 开始，长度为 2^j 的区间的极值。

## 查询操作
查询区间 [l, r] 的最大值：
```cpp
int query_max(int l, int r) {
    int k = lg2[r - l + 1];  // 找到最大的 k 使得 2^k <= 区间长度
    return max(st_max[l][k], st_max[r - (1 << k) + 1][k]);
}
```

## 正确性原理

ST 表查询的正确性基于两个关键性质：

1. 区间覆盖性：
   - 对于任意区间 [l, r]，我们选择两个子区间 [l, l + 2^k - 1] 和 [r - 2^k + 1, r]
   - 这两个子区间必定完全覆盖原区间 [l, r]
   - 其中 k 是满足 2^k <= r - l + 1 的最大整数

2. 可重复贡献性：
   - 对于最大值运算 max，满足：
     * max(a, a) = a （幂等性）
     * max(a, b) = max(b, a) （交换律）
     * max(a, max(b, c)) = max(max(a, b), c) （结合律）
   - 这使得可以安全地计算重叠区间的最大值而不影响最终结果

## RMQ 模板代码

```cpp
void init(int n) {
    lg2[1] = 0;
    for (int i = 2; i <= n; i++) {
        lg2[i] = lg2[i / 2] + 1;
    }
    for (int j = 1; j <= 20; j++) {
        for (int i = 1; i + (1 << j) - 1 <= n; i++) {
            st[i][j] = min(st[i][j - 1], st[i + (1 << (j - 1))][j - 1]);
        }
    }
}

int query(int l, int r) {
    int k = lg2[r - l + 1];
    return min(st[l][k], st[r - (1 << k) + 1][k]);
}
```