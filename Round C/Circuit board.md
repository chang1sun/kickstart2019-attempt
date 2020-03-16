# Circuit Board
> *This solution use sparse-table to solve RMQ, which lead it to O(n^2logn),*
---
> *But TLE occurrd when I summit although the time complexity is accepted by offical explanation*
---
> *Still, I haven't found what's going wrong yet.*
---
***here is my code in python-3.7:***
```python
from math import log # maybe this cause a big cost?

def func(hist, r): # find the max board by using histogram, details in leetcode 84, 85 discussion.
    res = 0
    for row in hist:
        rowmax = 0
        stack = [-1]
        row.append(0)
        for i in range(r+1):
            while row[i] < row[stack[-1]]:
                h = row[stack.pop()]
                lb = stack[-1]
                rowmax = max(rowmax, h*(i - lb - 1))
            stack.append(i)
        row.pop()
        res = max(res, rowmax)
    return res

def con_st(row, c): # constuct sparse-table(max), sparse-table(min) respectively
    n = int(log(c, 2))
    stmax = [[0 for j in range(n+1)] for i in range(c)]
    stmin = [[0 for j in range(n+1)] for i in range(c)]
    for i in range(c):
        stmax[i][0] = row[i]
        stmin[i][0] = row[i]
    for j in range(1, n+1):
        for i in range(c-(1<<j)+1):
            stmax[i][j] = max(stmax[i][j-1], stmax[i+(1<<(j-1))][j-1])
            stmin[i][j] = min(stmin[i][j-1], stmin[i+(1<<(j-1))][j-1])
    return (stmax, stmin)

def maxq(st, i, j):
    k = int(log(j-i+1, 2))
    return max(st[i][k], st[j-(1<<k)+1][k])

def minq(st, i, j):
    k = int(log(j-i+1, 2))
    return min(st[i][k], st[j-(1<<k)+1][k])

def solve(nums, r, c, k):
    hist = [[0 for i in range(r)] for j in range(c)]
    for no, row in enumerate(nums):
        stmax, stmin = con_st(row, c)
        for j in range(c):
            left, right = j, c-1
            while left < right:
                mid = (left+right)//2
                if maxq(stmax, j, mid) - minq(stmin, j, mid) <= k:
                    left = mid+1
                else:
                    right = mid
            if maxq(stmax, j, left) - minq(stmin, j, left) <= k:
                hist[j][no] = left-j+1
            else:
                hist[j][no] = left-j
    res = func(hist, r)
    return res
if __name__ == '__main__':
    T = int(input())
    for t in range(1, T+1):
        r, c, k = map(int, input().split())
        nums = []
        for i in range(r):
            nums.append(list(map(int, input().split())))
        print("Case #%d: %d" % (t, solve(nums, r, c, k)))
```
