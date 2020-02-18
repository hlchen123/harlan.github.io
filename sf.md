####



# 会议室

harlanchen result
## 是否能参加所有会议
Given an array of meeting time intervals consisting of start and end times [[s1,e1],[s2,e2],...] (si < ei), determine if a person could attend all meetings.

Example 1:

	Input: [[0,30],[5,10],[15,20]]
	Output: false
	Example 2:

	Input: [[7,10],[2,4]]
	Output: true



```java
/**
 * Definition for an interval.
 * public class Interval {
 *     int start;
 *     int end;
 *     Interval() { start = 0; end = 0; }
 *     Interval(int s, int e) { start = s; end = e; }
 * }
 */
public class Solution {
    public boolean canAttendMeetings(Interval[] intervals) {
        if (intervals == null || intervals.length ==0) {
            return true;
        }

        // Sort according to the start time
        Arrays.sort(intervals, new IntervalComparator());

        Interval prev = intervals[0];
        for (int i = 1; i < intervals.length; i++) {
            Interval curr = intervals[i];
            if (isOverlapped(prev, curr)) {
                return false;
            }
            prev = curr;
        }

        return true;
    }

    public class IntervalComparator implements Comparator<Interval> {
        @Override
        public int compare(Interval a, Interval b) {
            return a.start - b.start;
        }
    }

    private boolean isOverlapped(Interval a, Interval b) {
        return a.end > b.start;
    }
}
//----------------------------------------------
//排序部分
        Arrays.sort(intervals, new Comparator<Interval>() {
            public int compare(Interval i1, Interval i2) {
                return i1.start - i2.start;
            }
        });
```


##  至少需要多少会议室
Given an array of meeting time intervals consisting of start and end times [[s1,e1],[s2,e2],...] (si < ei), find the minimum number of conference rooms required.

	Example 1:

	Input: [[0, 30],[5, 10],[15, 20]]
	Output: 2
	Example 2:

	Input: [[7,10],[2,4]]
	Output: 1

**数组分拆排序**

分别对起始时间和结束时间排序，由于会议之间并无差异（不像skyline问题，不同建筑的高度不一样），所以分别使用两个指针来推进起始时间和结束时间。
<img src=https://img-blog.csdnimg.cn/20191230210747241.png width=80%>
activeMeeting存储的是目前为止的时间段，所需的房价数两。



- 复杂度分析

	- 时间复杂度: O(NlogN)。我们所做的只是将 开始时间 和 结束时间 两个数组分别进行排序。每个数组有 NN 个元素，因为有 N 个时间间隔。

	- 空间复杂度: O(N)。我们建立了两个 N 大小的数组。分别用于记录会议的开始时间和结束时间。

```java
/**
 * Definition for an interval.
 * public class Interval {
 *     int start;
 *     int end;
 *     Interval() { start = 0; end = 0; }
 *     Interval(int s, int e) { start = s; end = e; }
 * }
 */
public class Solution {
    public int minMeetingRooms(Interval[] intervals) {
        int[] starts = new int[intervals.length];
        int[] ends = new int[intervals.length];
        for(int i=0; i<intervals.length; i++) {
            starts[i] = intervals[i].start;
            ends[i] = intervals[i].end;
        }
        Arrays.sort(starts);
        Arrays.sort(ends);
        int rooms = 0;
        int activeMeetings = 0;
        int i=0, j=0;
        while (i < intervals.length && j < intervals.length) {
            if (starts[i] < ends[j]) {
                activeMeetings ++;
                i ++;
            } else {
                activeMeetings --;
                j ++;
            }
            rooms = Math.max(rooms, activeMeetings);
        }
        return rooms;
    }
}
```
**最小堆**

   对起始时间进行排序，使用最小堆来记录当前会议的结束时间，当心会
议的起始时间大于最小堆中的最早结束时间，说明新会议与堆中的最早结束会议不重叠

堆里存的是所有已开的房间此刻的结束日期，堆顶是最小值。

1. 先把intervals按照start从小到大排序。
2. 遍历interval，将每个interval的末尾时间加入到一个最小堆。
	1. 把当前interval的start和堆顶的end对比，如果交叉，说明要重新开一个房间
	2. 如果没有交叉，可以直接把当前加入到堆顶对应的房间里，并将堆顶的元素弹出，因为以后仅考虑当前interval的末尾时间

---
1. 按照 开始时间 对会议进行排序。
2. 初始化一个新的 最小堆，将第一个会议的结束时间加入到堆中。我们只需要记录会议的结束时间，告诉我们什么时候房间会空。
3. 对每个会议，检查堆的最小元素（即堆顶部的房间）是否空闲。
	1. 若房间空闲，则从堆顶拿出该元素，将其改为我们处理的会议的结束时间，加回到堆中。
	2. 若房间不空闲。开新房间，并加入到堆中。
4. 处理完所有会议后，堆的大小即为开的房间数量。这就是容纳这些会议需要的最小房间数。

----
- 复杂度分析

	-	时间复杂度 ：O(NlogN)。
时间开销主要有两部分。第一部分是数组的 排序 过程，消耗 O(NlogN) 的时间。接下来是 最小堆 占用的时间。在最坏的情况下，全部 N 个会议都会互相冲突。在任何情况下，我们都要向堆执行 N 次插入操作。在最坏的情况下，我们要对堆进行 N 次查找并删除最小值操作。总的时间复杂度为 (NlogN)，因为查找并删除最小值操作只消耗 O(logN)的时间。
	- 空间复杂度: O(N)。额外空间用于建立的最小堆。在最坏的情况下，堆需要容纳全部 N 个元素。因此空间复杂度为 O(N)。


	```java
	/**
	 * Definition for an interval.
	 * public class Interval {
	 *     int start;
	 *     int end;
	 *     Interval() { start = 0; end = 0; }
	 *     Interval(int s, int e) { start = s; end = e; }
	 * }
	 */
	public class Solution {
	    public int minMeetingRooms(Interval[] intervals) {
	        Arrays.sort(intervals, new Comparator<Interval>() {
	            @Override
	            public int compare(Interval i1, Interval i2) {
	                return Integer.compare(i1.start, i2.start);
	            }
	        });
	        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
	        int rooms = 0;
	        for(int i=0; i<intervals.length; i++) {
	            minHeap.offer(intervals[i].end);
	            if (intervals[i].start < minHeap.peek()) {
	                rooms ++;
	            } else {
	                minHeap.poll();
	            }
	        }
	        return rooms;
	    }
	}
	```
当是二维数组结构时，须改变`Arrays.sort(intervals, new Comparator<Interval>`为`Arrays.sort(intervals, new Comparator<int[]>`

	```python

	# Definition for an interval.
	import heapq
	# class Interval(object):
	#     def __init__(self, s=0, e=0):
	#         self.start = s
	#         self.end = e

	class Solution:
	    def minMeetingRooms(self, intervals):
	        intervals.sort(key=lambda x:x.start)
	        heap = []
	        for interval in intervals:
	            if heap and interval.start >= heap[0]:
	                heapq.heappop(heap)
	                heapq.heappush(heap, interval.end)
	            else:
	                heapq.heappush(heap, interval.end)
	```

**求所有会议时间并发的最大值即可**
```cpp
#define LONGEST_TIME 100000 //这个长度定义提交了几次按用例确定的，如果再大就要考虑其他存储方式

int minMeetingRooms(int** intervals, int intervalsSize, int* intervalsColSize){
	int timeCount[LONGEST_TIME] = {0};
	for (int i = 0; i < intervalsSize; i++) {
		for (int j = intervals[i][0]; j <= intervals[i][1]; j++) {
		timeCount[j]++;
		}
	}
	int max = 0;
	for (int i = 0; i < LONGEST_TIME; i++) {
		max = max>timeCount[i]? max:timeCount[i];
	}
	return max;
}

作者：bigfat
链接：https://leetcode-cn.com/problems/meeting-rooms-ii/solution/jian-dan-suan-fa-qiu-suo-you-hui-yi-shi-jian-bing-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```
**按会议起始位置排序，合并区间即可**

- 以输入[[0, 30],[5, 10],[15, 20],[9, 17]]为例
1. 首先对所有会议时间按照起始时间从小到大进行排序:[[0, 30],[5, 10],[9, 17],[15, 20]]
2. 处理[0, 30]，由于当前没有任何会议室，所以[0, 30]需要消耗一个会议室
3. 处理[5, 10]，当前已经存在一个会议室[0, 30]，但[5, 10]和[0, 30]时间有重叠，所以[5, 10]也需要消耗一个会议室
4. 处理[9, 17]，当前存在两个会议室，但均存在重叠，所以[9, 17]也需要消耗一个会议室
5. 处理[15, 20]，当前已经存在三个会议室[0, 30] [5, 10] [9, 17]，同时[15, 20]可以和[5, 10]共用一个会议室，所以当前也只需要三个会议室，而且由于会议时间是按照起始时间从小到大进行排序的，那么就可以将[5, 10]和[15, 20]合并为[5, 20]
6. 继续循环处理即可，如果后加入的区间和所有当前处理的区间均无法完成合并，那么就需要一个新的会议室。

```cpp
#define MAX_SIZE (100000)
#define INTERVAL_SIZE (2)
#define TRUE (1)
#define FALSE (0)

int g_interval[MAX_SIZE][INTERVAL_SIZE];

void SortInterval(int** intervals, int intervalsSize)
{
    int i;
    int j;
    int tmp;
    for (i = 1; i <= intervalsSize - 1; i++) {
        for (j = 0; j <= intervalsSize - i - 1; j++) {
            if (intervals[j][0] > intervals[j + 1][0]) {
                tmp = intervals[j][0];
                intervals[j][0] = intervals[j + 1][0];
                intervals[j + 1][0] = tmp;

                tmp = intervals[j][1];
                intervals[j][1] = intervals[j + 1][1];
                intervals[j + 1][1] = tmp;
            }
        }
    }
}
int minMeetingRooms(int** intervals, int intervalsSize, int* intervalsColSize)
{
    int i,j;
    int flag;
    int index = 0;
    SortInterval(intervals, intervalsSize);
    /*
    for (i = 0; i <= intervalsSize - 1; i++) {
        printf("s = %d e = %d\n", intervals[i][0], intervals[i][1]);
    }
    */
    for (i = 0; i <= intervalsSize - 1; i++) {
        flag = FALSE;
        for (j = 0; j < index; j++) {
            if (intervals[i][0] >= g_interval[j][1]) {
                flag = TRUE;
                g_interval[j][1] = intervals[i][1];
                break;
            }
        }
        if (flag == FALSE) {
            g_interval[index][0] = intervals[i][0];
            g_interval[index][1] = intervals[i][1];
            index++;
        }
    }
    return index;
}

作者：jiankang
链接：https://leetcode-cn.com/problems/meeting-rooms-ii/solution/an-hui-yi-qi-shi-wei-zhi-pai-xu-he-bing-qu-jian-ji/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```


# 安排会议日程

你是一名行政助理，手里有两位客户的空闲时间表：slots1 和 slots2，以及会议的预计持续时间 duration，请你为他们安排合适的会议时间。

「会议时间」是两位客户都有空参加，并且持续时间能够满足预计时间 duration 的 最早的时间间隔。

如果没有满足要求的会议时间，就请返回一个 空数组。



「空闲时间」的格式是 [start, end]，由开始时间 start 和结束时间 end 组成，表示从 start 开始，到 end 结束。

题目保证数据有效：同一个人的空闲时间不会出现交叠的情况，也就是说，对于同一个人的两个空闲时间 [start1, end1] 和 [start2, end2]，要么 start1 > end2，要么 start2 > end1。



	示例 1：

	输入：slots1 = [[10,50],[60,120],[140,210]], slots2 = [[0,15],[60,70]], duration = 8
	输出：[60,68]
	示例 2：

	输入：slots1 = [[10,50],[60,120],[140,210]], slots2 = [[0,15],[60,70]], duration = 12
	输出：[]


提示：

1 <= slots1.length, slots2.length <= 10^4
slots1[i].length, slots2[i].length == 2
slots1[i][0] < slots1[i][1]
slots2[i][0] < slots2[i][1]
0 <= slots1[i][j], slots2[i][j] <= 10^9
1 <= duration <= 10^6

**端点排序**

可以把所有的端点记录在一个vector里面，之后扫一遍，遇到一个起点+1，遇到一个终点-1，所以只要在+2的区间里，就可以保证是同时有两个区间覆盖的，统计这个区间的大小即可。

```cpp
int cmp(pair<int,int> a, pair<int,int> b)
{
    return a.first<b.first;
}
class Solution {

public:
    vector<int> minAvailableDuration(vector<vector<int>>& slots1, vector<vector<int>>& slots2, int duration) {
        vector<pair<int,int> > s;
        int s1=0,s2=0,e1=0,e2=0;
        for(auto x:slots1)
        {
            s.push_back(make_pair(x[0],0));
            s.push_back(make_pair(x[1],2));
        }
        for(auto x:slots2)
        {
            s.push_back(make_pair(x[0],1));
            s.push_back(make_pair(x[1],3));
        }
        sort(s.begin(),s.end());
        vector<int> ans;
        int cnt =0;
        for(auto x: s)
        {
            if(x.second==0)s1 = x.first,cnt++;
            if(x.second==1)s2 = x.first,cnt++;
            if(x.second==2 || x.second==3)
            {
                if(cnt==2&&max(s1,s2)+duration<=x.first)
                {
                    ans.push_back(max(s1,s2));
                    ans.push_back(max(s1,s2)+duration);
                    break;
                }
                cnt--;
            }
        }
        return ans;
    }
};

作者：war-war-toe
链接：https://leetcode-cn.com/problems/meeting-scheduler/solution/c-sao-miao-fa-by-war-war-toe/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

# 一个会议室最多安排多少会议

会议安排问题：

（1）对于每个会议i，起始时间bi和结束时间ei，且bi<ei

（2）[bi,ei)与[bj,ej)不相交，则会议i和会议j相容，bi≥ej或bj≥ei

目标：在有限的时间内，尽可能多地安排会议。

关键：
1. 按结束时间非递减排序，开始时间非递增排序
2. 选中第一个具有最早结束时间的会议，用last记录时间。
3. 挑剩下的，若i的开始时间>last，则与会议i与已挑会议相容。

关键代码
```cpp
ans = 1;
int last = meet[0].end;
for (i = 1; i < n; ++i) {
    if (meet[i].beg >= last) {
        ans++;
        last = meet[i].end;
        cout << "  选择第" << meet[i].num << "个会议" << endl;
    }
}

```


# [螺旋矩阵](https://leetcode-cn.com/problems/spiral-matrix/)

给定一个包含 m x n 个元素的矩阵（m 行, n 列），请按照顺时针螺旋顺序，返回矩阵中的所有元素。

示例 1:

	输入:
	[
	 [ 1, 2, 3 ],
	 [ 4, 5, 6 ],
	 [ 7, 8, 9 ]
	]
	输出: [1,2,3,6,9,8,7,4,5]
	示例 2:

	输入:
	[
	  [1, 2, 3, 4],
	  [5, 6, 7, 8],
	  [9,10,11,12]
	]
	输出: [1,2,3,4,8,12,11,10,9,5,6,7]


这里的方法不需要记录已经走过的路径，所以执行用时和内存消耗都相对较小

首先设定上下左右边界
其次向右移动到最右，此时第一行因为已经使用过了，可以将其从图中删去，体现在代码中就是重新定义上边界
判断若重新定义后，上下边界交错，表明螺旋矩阵遍历结束，跳出循环，返回答案
若上下边界不交错，则遍历还未结束，接着向下向左向上移动，操作过程与第一，二步同理
不断循环以上步骤，直到某两条边界交错，跳出循环，返回答案

```java

class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> ans = new ArrayList();
        if (matrix.length == 0) return ans;
        int u = 0;
        int d = matrix.length-1;
        int l = 0;
        int r = matrix[0].length-1;

        while (true) {
            for(int i = l; i<=r; i++) ans.add(matrix[u][i]);
            if (++u > d) break;

            for(int i = u; i<=d; i++) ans.add(matrix[i][r]);
            if (--r < l ) break;

            for(int i = r; i>=l; i--) ans.add(matrix[d][i]);
            if (--d < u ) break;

            for(int i = d; i>=u; i--) ans.add(matrix[i][l]);
            if (++l >r ) break;
        }
        return ans;
    }
}
```

## 螺旋数组2

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int l = 0, r = n - 1, t = 0, b = n - 1;
        int[][] mat = new int[n][n];
        int num = 1, tar = n * n;
        while(num <= tar){
            for(int i = l; i <= r; i++) mat[t][i] = num++; // left to right.
            t++;
            for(int i = t; i <= b; i++) mat[i][r] = num++; // top to bottom.
            r--;
            for(int i = r; i >= l; i--) mat[b][i] = num++; // right to left.
            b--;
            for(int i = b; i >= t; i--) mat[i][l] = num++; // bottom to top.
            l++;
        }
        return mat;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/spiral-matrix-ii/solution/spiral-matrix-ii-mo-ni-fa-she-ding-bian-jie-qing-x/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```


# [LRU缓存机制](https://leetcode-cn.com/problems/lru-cache/)
运用你所掌握的数据结构，设计和实现一个  LRU (最近最少使用) 缓存机制。它应该支持以下操作： 获取数据 get 和 写入数据 put 。

获取数据 get(key) - 如果密钥 (key) 存在于缓存中，则获取密钥的值（总是正数），否则返回 -1。
写入数据 put(key, value) - 如果密钥不存在，则写入其数据值。当缓存容量达到上限时，它应该在写入新数据之前删除最近最少使用的数据值，从而为新的数据值留出空间。

进阶:

你是否可以在 O(1) 时间复杂度内完成这两种操作？

<img src=https://img-blog.csdnimg.cn/20200101235515796.png width=60%>

	示例:

	LRUCache cache = new LRUCache( 2 /* 缓存容量 */ );

	cache.put(1, 1);
	cache.put(2, 2);
	cache.get(1);       // 返回  1
	cache.put(3, 3);    // 该操作会使得密钥 2 作废
	cache.get(2);       // 返回 -1 (未找到)
	cache.put(4, 4);    // 该操作会使得密钥 1 作废
	cache.get(1);       // 返回 -1 (未找到)
	cache.get(3);       // 返回  3
	cache.get(4);       // 返回  4


[LRU 算法设计](https://leetcode-cn.com/problems/lru-cache/solution/lru-ce-lue-xiang-jie-he-shi-xian-by-labuladong/)
分析上面的操作过程，要让 put 和 get 方法的时间复杂度为 O(1)O(1)，我们可以总结出 cache 这个数据结构必要的条件：查找快，插入快，删除快，有顺序之分。

因为显然 cache 必须有顺序之分，以区分最近使用的和久未使用的数据；而且我们要在 cache 中查找键是否已存在；如果容量满了要删除最后一个数据；每次访问还要把数据插入到队头。

那么，什么数据结构同时符合上述条件呢？哈希表查找快，但是数据无固定顺序；链表有顺序之分，插入删除快，但是查找慢。所以结合一下，形成一种新的数据结构：哈希链表。

LRU 缓存算法的核心数据结构就是哈希链表，双向链表和哈希表的结合体。这个数据结构长这样：
<img src=https://img-blog.csdnimg.cn/20200102001113150.png width=60%>

**代码实现**

很多编程语言都有内置的哈希链表或者类似 LRU 功能的库函数，但是为了帮大家理解算法的细节，我们用 Java 自己造轮子实现一遍 LRU 算法。

首先，我们把双链表的节点类写出来，为了简化，key 和 val 都认为是 int 类型：

Java

```java
class Node {
    public int key, val;
    public Node next, prev;
    public Node(int k, int v) {
        this.key = k;
        this.val = v;
    }
}
// 实现几个api addFirst, Removelast, remove
class DoubleList {
    private Node head, tail; // 头尾虚节点
    private int size; // 链表元素数

    public DoubleList() {
        head = new Node(0, 0);
        tail = new Node(0, 0);
        head.next = tail;
        tail.prev = head;
        size = 0;
    }

    // 在链表头部添加节点 x
    public void addFirst(Node x) {
        x.next = head.next;
        x.prev = head;
        head.next.prev = x;
        head.next = x;
        size++;
    }

    // 删除链表中的 x 节点（x 一定存在）
    public void remove(Node x) {
        x.prev.next = x.next;
        x.next.prev = x.prev;
        size--;
    }

    // 删除链表中最后一个节点，并返回该节点
    public Node removeLast() {
        if (tail.prev == head)
            return null;
        Node last = tail.prev;
        remove(last);
        return last;
    }

    // 返回链表长度
    public int size() { return size; }
}
```
实现逻辑
```
// key 映射到 Node(key, val)
HashMap<Integer, Node> map;
// Node(k1, v1) <-> Node(k2, v2)...
DoubleList cache;

int get(int key) {
    if (key 不存在) {
        return -1;
    } else {
        将数据 (key, val) 提到开头；
        return val;
    }
}

void put(int key, int val) {
    Node x = new Node(key, val);
    if (key 已存在) {
        把旧的数据删除；
        将新节点 x 插入到开头；
    } else {
        if (cache 已满) {
            删除链表的最后一个数据腾位置；
            删除 map 中映射到该数据的键；
        }
        将新节点 x 插入到开头；
        map 中新建 key 对新节点 x 的映射；
    }
}
```
 代码：

 ```java
 class LRUCache {
    // key -> Node(key, val)
    private HashMap<Integer, Node> map;
    // Node(k1, v1) <-> Node(k2, v2)...
    private DoubleList cache;
    // 最大容量
    private int cap;

    public LRUCache(int capacity) {
        this.cap = capacity;
        map = new HashMap<>();
        cache = new DoubleList();
    }

    public int get(int key) {
        if (!map.containsKey(key))
            return -1;
        int val = map.get(key).val;
        // 利用 put 方法把该数据提前
        put(key, val);
        return val;
    }

    public void put(int key, int val) {
        // 先把新节点 x 做出来
        Node x = new Node(key, val);

        if (map.containsKey(key)) {
            // 删除旧的节点，新的插到头部
            cache.remove(map.get(key));
            cache.addFirst(x);
            // 更新 map 中对应的数据
            map.put(key, x);
        } else {
            if (cap == cache.size()) {
                // 删除链表最后一个数据
                Node last = cache.removeLast();
                map.remove(last.key);
            }
            // 直接添加到头部
            cache.addFirst(x);
            map.put(key, x);
        }
    }
}
```


# [格雷编码](https://leetcode-cn.com/problems/gray-code)

格雷编码是一个二进制数字系统，在该系统中，两个连续的数值仅有一个位数的差异。

给定一个代表编码总位数的非负整数 n，打印其格雷编码序列。格雷编码序列必须以 0 开头。

	示例 1:

	输入: 2
	输出: [0,1,3,2]
	解释:
	00 - 0
	01 - 1
	11 - 3
	10 - 2

	对于给定的 n，其格雷编码序列并不唯一。
	例如，[0,2,3,1] 也是一个有效的格雷编码序列。

	00 - 0
	10 - 2
	11 - 3
	01 - 1

## 回溯
<img src=https://img-blog.csdnimg.cn/20200107001809946.png width=60%>

```java
class Solution {
    public List<Integer> grayCode(int n) {
        List<Integer> res = new ArrayList();
        res.add(0);
        int head = 1;
        for (int i=0; i<n; i++) {
            for (int j= res.size()-1; j>=0; j--) {
                res.add(head + res.get(j));
            }
            head = head << 1;
        }
        return res;
    }
}
```

# [链表随机节点](https://leetcode-cn.com/problems/linked-list-random-node)
## 蓄水池抽样

给定一个单链表，随机选择链表的一个节点，并返回相应的节点值。保证每个节点被选的概率一样。

	进阶:
	如果链表十分大且长度未知，如何解决这个问题？你能否使用常数级空间复杂度实现？

	示例:

	// 初始化一个单链表 [1,2,3].
	ListNode head = new ListNode(1);
	head.next = new ListNode(2);
	head.next.next = new ListNode(3);
	Solution solution = new Solution(head);

	// getRandom()方法应随机返回1,2,3中的一个，保证每个元素被返回的概率相等。
	solution.getRandom();



思路：从左向右遍历链表，每遇到一个节点，计数器cnt加1。然后生成随机数j在范围[0,…,cnt-1]中。如果j < 1 那么，取此位置。如果不是，则不取。

概率证明：第cnt个节点被选中的概率为1/cnt。蓄水池中被选中要替换的节点的概率为1/1。综合看第cnt个节点在蓄水池中的概率为1/cnt * 1/1 = 1/cnt。



```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {

    /** @param head The linked list's head.
        Note that the head is guaranteed to be not null, so it contains at least one node. */
    private ListNode head;
    public Solution(ListNode head) {
        this.head = head;

    }

    /** Returns a random node's value. */
    public int getRandom() {
        int cnt = 2;
        ListNode cur = head.next;
        Random rdm = new Random();
        int res = head.val;
        while (cur != null) {
            int tmp = rdm.nextInt(cnt);
            if (tmp ==0) res = cur.val;
            cnt++;
            cur = cur.next;
        }
        return res;
    }
}

/**
 * Your Solution object will be instantiated and called as such:
 * Solution obj = new Solution(head);
 * int param_1 = obj.getRandom();
 */
```


# [随机数索引](https://leetcode-cn.com/problems/random-pick-index/)

给定一个可能含有重复元素的整数数组，要求随机输出给定的数字的索引。 您可以假设给定的数字一定存在于数组中。

注意：
数组大小可能非常大。 使用太多额外空间的解决方案将不会通过测试。

示例:

	int[] nums = new int[] {1,2,3,3,3};
	Solution solution = new Solution(nums);

	// pick(3) 应该返回索引 2,3 或者 4。每个索引的返回概率应该相等。
	solution.pick(3);

	// pick(1) 应该返回 0。因为只有nums[0]等于1。
	solution.pick(1);

要产生一个[a,b]之间的整数的方法是(int)(Math.random()*(b-a+1))+a

蓄水池采样方法

```
class Solution {
    private int[] nums;
    public Solution(int[] nums) {
       this.nums = nums;
    }

    public int pick(int target) {
        Random r = new Random();
        int n = 0;
        int index = 0;
        for(int i = 0;i < nums.length;i++)
            if(nums[i] == target){
            //我们的目标对象中选取。
                n++;
                //我们以1/n的概率留下该数据
                if(r.nextInt() % n == 0) index = i;
            }
        return index;
    }
}

作者：an-xin-9
链接：https://leetcode-cn.com/problems/random-pick-index/solution/xu-shui-chi-chou-yang-wen-ti-by-an-xin-9/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

# [在圆内随机生成点](https://leetcode-cn.com/problems/generate-random-point-in-a-circle/)
给定圆的半径和圆心的 x、y 坐标，写一个在圆中产生均匀随机点的函数 randPoint 。

说明:

输入值和输出值都将是浮点数。
圆的半径和圆心的 x、y 坐标将作为参数传递给类的构造函数。
圆周上的点也认为是在圆中。
randPoint 返回一个包含随机点的x坐标和y坐标的大小为2的数组。
示例 1：

	输入:
	["Solution","randPoint","randPoint","randPoint"]
	[[1,0,0],[],[],[]]
	输出: [null,[-0.72939,-0.65505],[-0.78502,-0.28626],[-0.83119,-0.19803]]

## rejection sampling

```java
class Solution {
    double rad, xc, yc;
    public Solution(double radius, double x_center, double y_center) {
        rad = radius;
        xc = x_center;
        yc = y_center;
    }

    public double[] randPoint() {
        double x0 = xc - rad; // 中心改为从左下角开始。
        double y0 = yc - rad;

        while(true) {
        	// 在四边形内是否
            double xg = x0 + Math.random() * rad * 2;
            double yg = y0 + Math.random() * rad * 2;
            if (Math.sqrt(Math.pow((xg - xc) , 2) + Math.pow((yg - yc), 2)) <= rad)
                return new double[]{xg, yg};
        }
    }
}
作者：LeetCode
链接：https://leetcode-cn.com/problems/generate-random-point-in-a-circle/solution/zai-yuan-nei-sui-ji-sheng-cheng-dian-by-leetcode/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 极坐标
```java
class Solution {
    double rad, xc, yc;
    public Solution(double radius, double x_center, double y_center) {
        rad = radius;
        xc = x_center;
        yc = y_center;
    }

    public double[] randPoint() {
    	// 极坐标采样
        double d = rad * Math.sqrt(Math.random());
        double theta = Math.random() * 2 * Math.PI;
        return new double[]{d * Math.cos(theta) + xc, d * Math.sin(theta) + yc};
    }
}
链接：https://leetcode-cn.com/problems/generate-random-point-in-a-circle/solution/zai-yuan-nei-sui-ji-sheng-cheng-dian-by-leetcode/
```


# [最小因式分解]()


```java
public class Solution {
    public int smallestFactorization(int a) {
        if (a < 2)
            return a;
        long res = 0, mul = 1;
        // 从大到小得到约数
        for (int i = 9; i >= 2; i--) {
        // 穷尽所有到值
            while (a % i == 0) {
                a /= i;
                res = mul * i + res;
                mul *= 10;
            }
        }
        return a < 2 && res <= Integer.MAX_VALUE ? (int)res : 0;
    }
}
链接：https://leetcode-cn.com/problems/minimum-factorization/solution/zui-xiao-yin-shi-fen-jie-by-leetcode/
```


# [接雨水](https://leetcode-cn.com/problems/trapping-rain-water/)
给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

<img src=https://img-blog.csdnimg.cn/2020011216570543.png  width=100%>

上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 感谢 Marcos 贡献此图。

	示例:

	输入: [0,1,0,2,1,0,1,3,2,1,2,1]
	输出: 6
方法一，备忘录法：
<img src=https://img-blog.csdnimg.cn/20200112170101879.png width=80%>
方法二，双指针法：
<img src=https://img-blog.csdnimg.cn/20200112175912775.png width=80%>


```java
class Solution {
    public int trap1(int[] height) {
        int length = height.length;
        if (length == 0) return 0;
        int[] l_max = new int[length];
        int[] r_max = new int[length];
        l_max[0] = height[0];
        r_max[length-1] = height[length-1];
        int ans = 0;
        for (int i=1; i<length; i++) {
            l_max[i] = Math.max(l_max[i-1], height[i]);
        }
        for (int i=length-2; i>=0; i--) {
            r_max[i] = Math.max(r_max[i+1], height[i]);
        }
        for (int i=1; i<length-1; i++) {
            int tmp = Math.min(l_max[i-1], r_max[i+1]);

            if (tmp > height[i]) {
                ans += tmp-height[i];
            }
            // System.out.printf("%d, %d, %d;", height[i],tmp, ans);
        }
        return ans;
    }
    public int trap(int[] height) {
            int length = height.length;
            if (length <= 2) return 0;

            int l_max = height[0], r_max=height[length-1];
            int ans = 0;
            int left = 0, right = length-1;


            while (left <= right) {
                l_max = Math.max(l_max, height[left]);
                r_max = Math.max(r_max, height[right]);

                if (l_max < r_max) {
                    ans += l_max-height[left];
                    left ++;
                }
                else {
                    ans += r_max - height[right];
                    right --;
                }
            }
            return ans;
        }
}
```


# [Nim 游戏](https://leetcode-cn.com/problems/nim-game)
你和你的朋友，两个人一起玩 Nim 游戏：桌子上有一堆石头，每次你们轮流拿掉 1 - 3 块石头。 拿掉最后一块石头的人就是获胜者。你作为先手。

你们是聪明人，每一步都是最优解。 编写一个函数，来判断你是否可以在给定石头数量的情况下赢得游戏。

	示例:

	输入: 4
	输出: false
	解释: 如果堆中有 4 块石头，那么你永远不会赢得比赛；
	     因为无论你拿走 1 块、2 块 还是 3 块石头，最后一块石头总是会被你的朋友拿走。

```java
class Solution {
    public boolean canWinNim(int n) {
        return n%4 != 0;
    }
}
```


# [旋转图像](https://leetcode-cn.com/problems/rotate-image/)

给定一个 n × n 的二维矩阵表示一个图像。

将图像顺时针旋转 90 度。

说明：

你必须在原地旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要使用另一个矩阵来旋转图像。

	示例 1:

	给定 matrix =
	[
	  [1,2,3],
	  [4,5,6],
	  [7,8,9]
	],

	原地旋转输入矩阵，使其变为:
	[
	  [7,4,1],
	  [8,5,2],
	  [9,6,3]
	]
	示例 2:

	给定 matrix =
	[
	  [ 5, 1, 9,11],
	  [ 2, 4, 8,10],
	  [13, 3, 6, 7],
	  [15,14,12,16]
	],

	原地旋转输入矩阵，使其变为:
	[
	  [15,13, 2, 5],
	  [14, 3, 4, 1],
	  [12, 6, 8, 9],
	  [16, 7,10,11]
	]

## [一次性旋转](https://leetcode-cn.com/problems/rotate-image/solution/yi-ci-xing-jiao-huan-by-powcai/)
如下图所示, 图像旋转本质就是这4个数的相互交换
<img src=https://img-blog.csdnimg.cn/20200115205825727.png  width=50%>
所以, 我们找出这几个数索引之间的关系(规律).

即:任意一个`(i,j) , (j, n-i-1), (n-i-1, n-j-1), (n -j-1, i)`就是这四个索引号上的数交换.

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
         for (int i = 0; i < n/2; i++ ) {
             for (int j = i; j < n - i - 1; j ++ ){
                 int tmp = matrix[i][j];
                 matrix[i][j] = matrix[n-j-1][i];
                 matrix[n-j-1][i] = matrix[n-i-1][n-j-1];
                 matrix[n-i-1][n-j-1] = matrix[j][n-i-1];
                 matrix[j][n-i-1] = tmp;
             }
         }
    }
}
```

## [翻转](https://leetcode-cn.com/problems/rotate-image/solution/yi-ci-xing-jiao-huan-by-powcai/)
直接举例子:

翻转整个数组,再按正对角线交换两边的数

	[                    [                  [
	  [1,2,3],             [7,8,9],            [7,4,1],
	  [4,5,6],    ---->    [4,5,6], ----->     [8,5,2],
	  [7,8,9]              [1,2,3]             [9,6,3]
	]                    ]                  ]


```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        // 上下翻转，两种写法
        // 第一种
        //  for (int i =0; i < n /2 ; i++ ){
        //      for (int j =0; j < n; j ++){
        //          int tmp = matrix[i][j];
        //          matrix[i][j] = matrix[n-i-1][j];
        //          matrix[n-i-1][j] = tmp;
        //      }
        //  }
        // 第二种
        for (int i = 0; i < n / 2; i ++){
            int[] tmp = matrix[i];
            matrix[i] = matrix[n - i - 1];
            matrix[n - i - 1] = tmp;
        }
        //System.out.println(Arrays.deepToString(matrix));
        // 对角翻转
        for (int i = 0; i < n; i ++){
            for (int j= i + 1; j < n; j++){
                int tmp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = tmp;
            }
        }
    }
}
```



# [二叉树中的最大路径和](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum)
给定一个非空二叉树，返回其最大路径和。

本题中，路径被定义为一条从树中任意节点出发，达到任意节点的序列。该路径至少包含一个节点，且不一定经过根节点。

	示例 1:

	输入: [1,2,3]

	       1
	      / \
	     2   3

	输出: 6
	示例 2:

	输入: [-10,9,20,null,null,15,7]

	   -10
	   / \
	  9  20
	    /  \
	   15   7

	输出: 42


解法：

		    a
		   / \
		  b   c

b + a + c。
b + a + a 的父结点。
a + c + a 的父结点。

```java
class Solution {
    int val = Integer.MIN_VALUE;
    public int gain_max(TreeNode root) {
        if (root == null) return 0;
        int left = Math.max(gain_max(root.left), 0);
        int right = Math.max(gain_max(root.right), 0);
        int tmp = root.val + left + right;
        val = Math.max(val, tmp);
        return root.val + Math.max(left, right);
    }
    public int maxPathSum(TreeNode root) {
        gain_max(root);
        return val;
    }
}
```


# [鸡蛋掉落](https://leetcode-cn.com/problems/super-egg-drop)

	你将获得 K 个鸡蛋，并可以使用一栋从 1 到 N  共有 N 层楼的建筑。

	每个蛋的功能都是一样的，如果一个蛋碎了，你就不能再把它掉下去。

	你知道存在楼层 F ，满足 0 <= F <= N 任何从高于 F 的楼层落下的鸡蛋都会碎，从 F 楼层或比它低的楼层落下的鸡蛋都不会破。

	每次移动，你可以取一个鸡蛋（如果你有完整的鸡蛋）并把它从任一楼层 X 扔下（满足 1 <= X <= N）。

	你的目标是确切地知道 F 的值是多少。

	无论 F 的初始值如何，你确定 F 的值的最小移动次数是多少？


## [动态规划](https://leetcode-cn.com/problems/super-egg-drop/solution/ji-ben-dong-tai-gui-hua-jie-fa-by-labuladong/)

	def dp(K, N):
	    for 1 <= i <= N:
	        # 最坏情况下的最少扔鸡蛋次数
	        res = min(res,
	                  max(
	                        dp(K - 1, i - 1), # 碎
	                        dp(K, N - i)      # 没碎
	                     ) + 1 # 在第 i 楼扔了一次
	                 )
	    return res



代码

```java
def superEggDrop(K: int, N: int):

    memo = dict()
    def dp(K, N) -> int:
        # base case
        if K == 1: return N
        if N == 0: return 0
        # 避免重复计算
        if (K, N) in memo:
            return memo[(K, N)]

        res = float('INF')
        # 穷举所有可能的选择
        for i in range(1, N + 1):
            res = min(res,
                      max(
                            dp(K, N - i),
                            dp(K - 1, i - 1)
                         ) + 1
                  )
        # 记入备忘录
        memo[(K, N)] = res
        return res

    return dp(K, N)
```

算法的总时间复杂度是 O(K*N^2), 空间复杂度 O(KN)。

# 相交链表
编写一个程序，找到两个单链表相交的起始节点。

如下面的两个链表：

<img src=https://img-blog.csdnimg.cn/20200120203623320.png width=80%>

 A和B两个链表长度可能不同，但是A+B和B+A的长度是相同的，所以遍历A+B和遍历B+A一定是同时结束。 如果A,B相交的话A和B有一段尾巴是相同的，所以两个遍历的指针一定会同时到达交点 如果A,B不相交的话两个指针就会同时到达A+B（B+A）的尾节点

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB == null) return null;
        ListNode preA = headA, preB = headB;
        while(preA != preB) {
            preA = preA == null ? headB:preA.next;
            preB = preB == null ? headA:preB.next;
        }
        return preA;
    }
}
```

# [部门工资前三高的所有员工](https://leetcode-cn.com/problems/department-top-three-salaries/)
```mysql
SELECT e1.Salary
FROM Employee AS e1
WHERE 3 >
		(SELECT  count(DISTINCT e2.Salary)
		 FROM	Employee AS e2
	 	 WHERE	e1.Salary < e2.Salary 	AND e1.DepartmentId = e2.DepartmentId) ;

作者：little_bird
链接：https://leetcode-cn.com/problems/department-top-three-salaries/solution/185-bu-men-gong-zi-qian-san-gao-de-yuan-gong-by-li/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```


# [分发糖果](https://leetcode-cn.com/problems/candy)

老师想给孩子们分发糖果，有 N 个孩子站成了一条直线，老师会根据每个孩子的表现，预先给他们评分。

你需要按照以下要求，帮助老师给这些孩子分发糖果：

每个孩子至少分配到 1 个糖果。
相邻的孩子中，评分高的孩子必须获得更多的糖果。
那么这样下来，老师至少需要准备多少颗糖果呢？

	示例 1:

	输入: [1,0,2]
	输出: 5
	解释: 你可以分别给这三个孩子分发 2、1、2 颗糖果。
	示例 2:

	输入: [1,2,2]
	输出: 4
	解释: 你可以分别给这三个孩子分发 1、2、1 颗糖果。
	     第三个孩子只得到 1 颗糖果，这已满足上述两个条件。


## [两个数组](https://leetcode-cn.com/problems/candy/solution/fen-fa-tang-guo-by-leetcode/)
<img src=https://img-blog.csdnimg.cn/20200120213514928.png width=100%>

```java
public class Solution {
    public int candy(int[] ratings) {
        int sum = 0;
        int[] left2right = new int[ratings.length];
        int[] right2left = new int[ratings.length];
        Arrays.fill(left2right, 1);
        Arrays.fill(right2left, 1);
        for (int i = 1; i < ratings.length; i++) {
            if (ratings[i] > ratings[i - 1]) {
                left2right[i] = left2right[i - 1] + 1;
            }
        }
        for (int i = ratings.length - 2; i >= 0; i--) {
            if (ratings[i] > ratings[i + 1]) {
                right2left[i] = right2left[i + 1] + 1;
            }
        }
        for (int i = 0; i < ratings.length; i++) {
            sum += Math.max(left2right[i], right2left[i]);
        }
        return sum;
    }
}
```

# 统计词频

```
cat words.txt | xargs -n 1 | sort | uniq -c | sort -nr | awk '{print $2" "$1}'
```


# [直线上最多的点数](https://leetcode-cn.com/problems/max-points-on-a-line)
给定一个二维平面，平面上有 n 个点，求最多有多少个点在同一条直线上。

	示例 1:

	输入: [[1,1],[2,2],[3,3]]
	输出: 3
	解释:
	^
	|
	|        o
	|     o
	|  o
	+------------->
	0  1  2  3  4

## [斜率汇总](https://leetcode-cn.com/problems/max-points-on-a-line/solution/yong-xie-lu-by-powcai/)
一句话解释: 固定一点, 找其他点和这个点组成直线, 统计他们的斜率!

这里有一个重点: 求斜率.用两种方法

用最大约数方法(gcd), 把他化成最简形式, 3/6 == 2/4 == 1/2
除数(不太精确, 速度快!)


```java
class Solution {
    public int maxPoints(int[][] points) {
        int n = points.length;
        if (n == 0) return 0;
        if (n == 1) return 1;
        int res = 0;
        //对于第i个点只需考虑它和后面点的组合即可，因为和前面的组合已经在更小的i时候讨论了
        for (int i = 0; i < n - 1; i++) {
            Map<String, Integer> slope = new HashMap<>();
            //repeat记录和第i个点重复点的个数
            int repeat = 0;
            int tmp_max = 0;
            for (int j = i + 1; j < n; j++) {
            	//dy为第j个点和第i个点之间y的距离
                int dy = points[i][1] - points[j][1];
                //dx为第j个点和第i个点之间x的距离
                int dx = points[i][0] - points[j][0];
                //dy=0=dx表明第j个点和第i个点重合了
                if (dy == 0 && dx == 0) {
                    repeat++;
                    continue;
                }
                //求公约数，因为如果采用dy/dx的方式求斜率，如果是一条平行于y轴的线会造成除法错误
                int g = gcd(dy, dx);
                //这一步不仅是进行简单的约分
				//对于特殊例子[0,0]的两个点[1,-1]、[-1,1]两者公约数分别为-1、1，但除去公约数后则为[-1,1]、[-1,1]则相同了
                if (g != 0) {
                    dy /= g;
                    dx /= g;
                }
                //String.valueOf(int)是将int数据类型直接转为stirng，eg:int为10则结果为"10",C++用to_string()
                String tmp = String.valueOf(dy) + "/" + String.valueOf(dx);
                //put为insert意思
				//HashMap.getOrDefault(a,b)为如果在哈希表中找到key为a的则返回a的映射value，如果没找到a则返回b
				//slope中更新斜率一样的点的个数
                slope.put(tmp, slope.getOrDefault(tmp, 0) + 1);
                tmp_max = Math.max(tmp_max, slope.get(tmp));
            }
            //重合的点也算在一条直线上，且重合的点可以说在任意一条直线上
            res = Math.max(res, repeat + tmp_max + 1);
        }
        return res;
    }
    private int gcd(int dy, int dx) {
        if (dx == 0) return dy;
        else return gcd(dx, dy % dx);
    }
}
```


 # [串联所有单词的子串](https://leetcode-cn.com/problems/substring-with-concatenation-of-all-words)
给定一个字符串 s 和一些长度相同的单词 words。找出 s 中恰好可以由 words 中所有单词串联形成的子串的起始位置。

注意子串要与 words 中的单词完全匹配，中间不能有其他字符，但不需要考虑 words 中单词串联的顺序。



	示例 1：

	输入：
	  s = "barfoothefoobarman",
	  words = ["foo","bar"]
	输出：[0,9]
	解释：
	从索引 0 和 9 开始的子串分别是 "barfoo" 和 "foobar" 。
	输出的顺序不重要, [9,0] 也是有效答案。
	示例 2：



	输入：
	  s = "wordgoodgoodgoodbestword",
	  words = ["word","good","best","word"]
	输出：[]

## [固定长度](https://leetcode-cn.com/problems/substring-with-concatenation-of-all-words/solution/chuan-lian-suo-you-dan-ci-de-zi-chuan-by-powcai/)
因为单词长度固定的，我们可以计算出截取字符串的单词个数是否和 words 里相等，所以我们可以借用哈希表。

一个是哈希表是 words，一个哈希表是截取的字符串，比较两个哈希是否相等！

因为遍历和比较都是线性的，所以时间复杂度：$O(n^2)$

```java
class Solution {
    public List<Integer> findSubstring(String s, String[] words) {
        List<Integer> res = new ArrayList<>();
        if (s == null || s.length() == 0 || words == null || words.length == 0) return res;
        HashMap<String, Integer> map = new HashMap<>();
        int one_word = words[0].length();
        int word_num = words.length;
        int all_len = one_word * word_num;
        for (String word : words) {
            map.put(word, map.getOrDefault(word, 0) + 1);
        }
        for (int i = 0; i < s.length() - all_len + 1; i++) {
            String tmp = s.substring(i, i + all_len);
            HashMap<String, Integer> tmp_map = new HashMap<>();
            for (int j = 0; j < all_len; j += one_word) {
                String w = tmp.substring(j, j + one_word);
                tmp_map.put(w, tmp_map.getOrDefault(w, 0) + 1);
            }
            if (map.equals(tmp_map)) res.add(i);
        }
        return res;
    }
}

```

## [滑动方法](https://leetcode-cn.com/problems/substring-with-concatenation-of-all-words/solution/chuan-lian-suo-you-dan-ci-de-zi-chuan-by-powcai/)

```java
class Solution {
    public List<Integer> findSubstring(String s, String[] words) {
        List<Integer> res = new ArrayList<>();
        if (s == null || s.length() == 0 || words == null || words.length == 0) return res;
        HashMap<String, Integer> map = new HashMap<>();
        int one_word = words[0].length();
        int word_num = words.length;
        int all_len = one_word * word_num;
        for (String word : words) {
            map.put(word, map.getOrDefault(word, 0) + 1);
        }
        for (int i = 0; i < one_word; i++) {
            int left = i, right = i, count = 0;
            HashMap<String, Integer> tmp_map = new HashMap<>();
            while (right + one_word <= s.length()) {
                String w = s.substring(right, right + one_word);
                tmp_map.put(w, tmp_map.getOrDefault(w, 0) + 1);
                right += one_word;
                count++;
                while (tmp_map.getOrDefault(w, 0) > map.getOrDefault(w, 0)) {
                    String t_w = s.substring(left, left + one_word);
                    count--;
                    tmp_map.put(t_w, tmp_map.getOrDefault(t_w, 0) - 1);
                    left += one_word;
                }
                if (count == word_num) res.add(left);

            }
        }

        return res;
    }
}
```




# 数字 1 的个数


```
《编程之美》上这样说:
设N = abcde ,其中abcde分别为十进制中各位上的数字。
如果要计算百位上1出现的次数，它要受到3方面的影响：百位上的数字，百位以下（低位）的数字，百位以上（高位）的数字。
	如果百位上数字为0，百位上可能出现1的次数由更高位决定。比如：12013，则可以知道百位出现1的情况可能是：100~199，1100~1199,2100~2199，，...，11100~11199，一共1200个。可以看出是由更高位数字（12）决定，并且等于更高位数字（12）乘以 当前位数（100）。注意：高位数字不包括当前位
	如果百位上数字为1，百位上可能出现1的次数不仅受更高位影响还受低位影响。比如：12113，则可以知道百位受高位影响出现的情况是：100~199，1100~1199,2100~2199，，....，11100~11199，一共1200个。和上面情况一样，并且等于更高位数字（12）乘以 当前位数（100）。但同时它还受低位影响，百位出现1的情况是：12100~12113,一共14个，等于低位数字（13）+1。 注意：低位数字不包括当前数字
	如果百位上数字大于1（2~9），则百位上出现1的情况仅由更高位决定，比如12213，则百位出现1的情况是：100~199,1100~1199，2100~2199，...，11100~11199,12100~12199,一共有1300个，并且等于更高位数字+1（12+1）乘以当前位数（100）
```


```java
class Solution {
public:
	int countDigitOne(int n) {
        if (n <= 0){
            return 0;
        }
		int num = n, result = 0;
		long long i = 1;//当前访问位的权
		while (num) {//分别计算个、十、百......千位上1出现的次数，再求和
			if (num % 10 == 0) {//当前位可能出现1的次数由更高位决定
				result += (num / 10) * i;
			}
			else if (num % 10 == 1) {//当前位可能出现1的次数不仅受更高位影响还受低位影响
				result += (num / 10) * i + (n % i) + 1;
			}
			else {//出现1的情况仅由更高位决定
				result += (n / i / 10 + 1) * i;
			}
			num = num / 10;
			i = i * 10;
		}
		return result;
	}
};

```


# [打乱数组](https://leetcode-cn.com/problems/shuffle-an-array)

打乱一个没有重复元素的数组。

	示例:

	// 以数字集合 1, 2 和 3 初始化数组。
	int[] nums = {1,2,3};
	Solution solution = new Solution(nums);

	// 打乱数组 [1,2,3] 并返回结果。任何 [1,2,3]的排列返回的概率应该相同。
	solution.shuffle();

	// 重设数组到它的初始状态[1,2,3]。
	solution.reset();

	// 随机返回数组[1,2,3]打乱后的结果。
	solution.shuffle();


## [Fisher-Yates 洗牌算法 【通过】](https://leetcode-cn.com/problems/shuffle-an-array/solution/da-luan-shu-zu-by-leetcode/)

```java
class Solution {
    private int[] array;
    private int[] original;

    Random rand = new Random();

    private int randRange(int min, int max) {
        return rand.nextInt(max - min) + min;
    }

    private void swapAt(int i, int j) {
        int temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }

    public Solution(int[] nums) {
        array = nums;
        original = nums.clone();
    }

    public int[] reset() {
        array = original;
        original = original.clone();
        return original;
    }

    public int[] shuffle() {
        for (int i = 0; i < array.length; i++) {
            swapAt(i, randRange(i, array.length));
        }
        return array;
    }
}
```
时间复杂度O(n)，空间复杂度O(n)
