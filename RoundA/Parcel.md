```python
from collections import deque

MOD = 10**9+7

dx,dy = [0,0,-1,1],[-1,1,0,0]

def solve(R,C,lines):
    dis = []
    bfs = deque()
    for i,line in enumerate(lines):
        dis.append([])
        for j in range(C):
            if line[j] == '0':
                dis[-1].append(-1)
            else:
                dis[-1].append(0)
                bfs.append((i, j))

    def issafe(i, j):
        return i >= 0 and j >= 0 and i < R and j < C

    r = 0
    while bfs:
        i, j = bfs.popleft()
        for d in range(4):
            ni, nj = i + dx[d], j + dy[d]
            if issafe(ni, nj) and dis[ni][nj] == -1:
                dis[ni][nj] = dis[i][j] + 1
                r = max(r, dis[ni][nj])
                bfs.append((ni, nj))

    def isable(k):
        maxA,maxM,minA,minM = -R-C,-R-C,R+C,R+C
        n = 0
        for i in range(R):
            for j in range(C):
                if dis[i][j]>k:
                    n += 1
                    maxA = max(maxA, i+j)
                    maxM = max(maxM, i-j)
                    minA = min(minA, i+j)
                    minM = min(minM, i-j)
        if n==0:
            return True
        for i in range(R):
            for j in range(C):
                A,M = i+j,i-j
                if dis[i][j]>0 and max(abs(maxA-A),abs(minA-A),abs(maxM-M),abs(minM-M))<=k:
                    return True
        return False

    l = 0
    while l < r:
        mid = (l + r) >> 1
        if isable(mid):
            r = mid
        else:
            l = mid+1
    return r


if __name__ == '__main__':
    t = int(input())
    for _ in range(1, t + 1):
        R,C = map(int,input().split())
        lines = []
        for i in range(R):
            lines.append(input())
        print("Case #{}: {}".format(_,solve(R,C,lines)))
```
