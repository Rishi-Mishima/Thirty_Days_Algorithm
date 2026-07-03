

```
OUTLINE: 

数组 
	- 动态数组
	- 静态数组

链表
	- 链表的基本原理
	
ACM做题模板
```

![image-20260628135001147](/Users/ruixue/Library/Application Support/typora-user-images/image-20260628135001147.png)

# 数组 (顺序存储) 基本原理

## 静态数组

**「静态数组」就是一块连续的内存空间，我们可以通过索引来访问这块内存空间中的元素，这才是数组的原始形态**。

JAVA有, Python🈚️

- 实际开发中很少用

```java
int[] arr = new int[10];

// 使用索引赋值
arr[0] = 1;
arr[1] = 2;

// 使用索引取值
int a = arr[0];
```

**我们获得了数组的超能力「随机访问」：**

- **只要给定任何一个数组索引，我可以在 O(1) 的时间内直接获取到对应元素的值**

### 静态数组: 增删改查

#### 增

##### 在数组末尾添加 (append)

```java
// 大小为 10 的数组已经装了 4 个元素
int[] arr = new int[10]; 

for(int i = 0; i < 4; i++){
  arr[i] = i; 
}

// 现在想在数组末尾追加一个元素 4
arr[4] = 4;

// 再在数组末尾追加一个元素 5
arr[5] = 5;
```

python 

```python 
# 大小为 10 的数组已经装了 4 个元素
arr = [0] * 10 
for i in range(4):
  arr[i] = i
  
# 现在想在数组末尾追加一个元素 4
arr[4] = 4

# 再在数组末尾追加一个元素 5
arr[5] = 5

```



##### 在数组中间insert

涉及「数据搬移」，给新元素腾出空位，然后再才能插入新元素

- 需要==倒着遍历==

> 易错点: 
>
> - size vs arr.length : size是数组中的数据, 而arr.length 是数组长度
> - 是 `>= index` 

```java
// 大小为 10 的数组已经装了 4 个元素
int[] arr = new int[10];

for(int i = 0; i < 4; i ++){
  arr[i] = i; 
}


int size = 4; 
// 在位置2的地方添加一个数字
for(int i = size - 1; i >= index; i --){
  arr[i + 1] = arr[i]; 
}

//赋值
arr [2] = 666; 

```

> **在数组中间插入元素的时间复杂度是 O(N)\*O\*(\*N\*)，因为涉及到数据搬移，给新元素腾地方**



##### 数组空间已满 (扩容)

因为==内存必须一次性分配==

- 所以需要扩容
- 然后复制数组到新数组

```python 
arr = [0] * 10

for i in range(10):
  arr[i] = i; 
  
arrNew = [0] * 20
for i in range(20):
  arrNew[i] = arr[i]
```

##### 

> **数组的扩容操作会涉及到新数组的开辟和数据的复制，时间复杂度是 O(N)**。



#### 删

##### 删除末尾元素

**删除数组尾部元素的本质就是进行一次随机访问，时间复杂度是 O(1)**

```python 
arr = [0] * 10

arr = [0] * 10
for i in range(5):
    arr[i] = i

# 删除末尾元素，暂时用 -1 代表元素已删除
arr[4] = -1
  
```



##### 删除中间元素

也要涉及「数据搬移」，把被删元素后面的元素都往前移动一位，保持数组元素的连续性。

- 也是O(N)

```python 
# 大小为 10 的数组已经装了 5 个元素
arr = [0] * 10
for i in range(5):
    arr[i] = i
    
# 删除 arr[1]
# 需要把 arr[1] 之后的元素都往前移动一位
for i in range (1, 4):
  arr[i] = arr[i + 1]
  
# 最后一个元素置为 -1 代表已删除
arr[4] = -1
```



> - 查, 改：给定指定索引，查询索引对应的元素的值，时间复杂度 O(1)。

## 动态数组

增加常用API

- **动态数组底层还是静态数组，只是自动帮我们进行数组空间的扩缩容，并把增删查改操作进行了封装，让我们使用起来更方便而已**。

java

```java 
// 创建动态数组
// 不用显式指定数组大小，它会根据实际存储的元素数量自动扩缩容

ArrayList<Integer> arr = new ArrayList<>(); 

for (int i = 0; i < 10; i++) {
    // 在末尾追加元素，时间复杂度 O(1)
    arr.add(i);
}

// 在中间插入元素，时间复杂度 O(N)
// 在索引 2 的位置插入元素 666
arr.add(2, 666);

// 在头部插入元素，时间复杂度 O(N)
arr.add(0, -1);

// 删除末尾元素，时间复杂度 O(1)
arr.remove(arr.size() - 1); 

// 删除中间元素，时间复杂度 O(N)
// 删除索引 2 的元素
arr.remove(2);

// 根据索引查询元素，时间复杂度 O(1)
int a = arr.get(0);

// 根据索引修改元素，时间复杂度 O(1)
arr.set(0, 100);

// 根据元素值查找索引，时间复杂度 O(N)
int index = arr.indexOf(666);
```



python 

```python 
arr = []

for i in range(10):
  # 在末尾追加元素，时间复杂度 O(1)
    arr.append(i)

# 在中间插入元素，时间复杂度 O(N)
# 在索引 2 的位置插入元素 666
arr.insert(2, 666)

# 在头部插入元素，时间复杂度 O(N)
arr.insert(0, -1)

# 删除末尾元素，时间复杂度 O(1)
arr.pop()

# 删除中间元素，时间复杂度 O(N)
# 删除索引 2 的元素
arr.pop(2)

# 根据索引查询元素，时间复杂度 O(1)
a = arr[0]

# 根据索引修改元素，时间复杂度 O(1)
arr[0] = 100

# 根据元素值查找索引，时间复杂度 O(N)
index = arr.index(666)
```



---

## 动态数组的代码实现

关键点: 

1. 自动扩容缩容
2. 索引越界的检查
3. 删除元素谨防内存泄露
4. 其他优化: e.g. 复制



##### 扩缩容原则

我们这里就实现一个简单的扩缩容的策略：

- 当数组元素个数达到底层静态数组的容量上限时，扩容为原来的 2 倍；
- 当数组元素个数缩减到底层静态数组的容量的 1/4 时，缩容为原来的 1/2。

### 

##### 索引越界? 

 `checkElementIndex` 🆚 `checkPositionIndex`

checkElementIndex 

- "访问一个已经存在的元素"
- 所以 `index < size`



checkPositionIndex 

它检查的是：

> **"我要把新元素放在哪里？"**

注意，这时候不是找元素。

而是在找**插入的位置**。

```
位置:

0   1   2   3   4
| 5 | 6 | 7 | 8 |
```

总共有 **5 个插入位置**。

而元素只有 **4 个**。

所以允许 `index == size`



##### 删除元素谨防内存泄露

- 设置为NULL
- 如果你不执行 `data[size - 1] = null` 这行代码，那么 `data[size - 1]` 这个引用就会一直存在，你可以通过 `data[size - 1]` 访问这个对象，所以这个对象被认为是可达的，它的内存就一直不会被释放，进而造成内存泄漏。
- Java 的垃圾回收机制是基于 [图算法](https://labuladong.online/zh/algo/data-structure-basic/graph-basic/) 的可达性分析，如果一个对象再也无法被访问到，那么这个对象占用的内存才会被释放；否则，垃圾回收器会认为这个对象还在使用中，就不会释放这个对象占用的内存。

``` java
public E removeElement(){
  E deletedVal = data[size - 1]; 
  
  // 删除最后一个元素
  // 必须给最后一个元素置为 null，否则会内存泄漏
    data[size - 1] = null;
    size--;

    return deletedVal;
}
```



### 动态数组 - 代码实现

> 可借助 力扣 707. 设计链表



---



## ACM 模板

```java
import java.util.*;

class Solution {
    public int add(int a, int b) {
        return a + b;
    }
}

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        // 读取到 EOF
      	// 只要还有输入，就一直读取。
        while (sc.hasNext()) {
            int a = sc.nextInt();
            int b = sc.nextInt();
            int result = new Solution().add(a, b);
            System.out.println(result);
        }
        sc.close();
    }
}

//EOF = End Of File（输入结束）。

        输入
          │
          ▼
Scanner 读取数据
          │
          ▼
调用 Solution 里的算法
          │
          ▼
得到结果
          │
          ▼
System.out.println 输出
```



----

# 链表

## 基本原理

构建链表: 

```java
class ListNode{
  int val; 
  ListNode next; 
  ListNode(int x){
		val = x; 
  }
}
```



java

```java
// 力扣
class ListNode{
  int val; 
  ListNode next; 
  ListNode(int x){
    val = x; 
  }
}

// 实际应用
class Node<E>{
  E val;          // 数据
	Node<E> next;   // 下一个节点
	Node<E> prev;   // 上一个节点 
  
  Node(Node<E> prev, E element, Node<E> next) {
        this.val = element;
        this.next = next;
        this.prev = prev;
    }
}

注意：
val 保存的是数据
next 保存的是节点
prev 保存的是节点
```



python : 

力扣

```python 
## 力扣中 构建单链表

class ListNode: 
  def __init__(self, x):
    self.val = x
    self.next = None
```

```python 

def __init__(self, x):
  # 这个节点的值是 x。
	self.val = x 
  self.next = None
  
这是 Python 里的构造函数。

它会在你创建对象的时候自动执行。

self 表示“当前这个对象自己”。

node = ListNode(5)
- 这里创建出来的这个节点，就是 self。

所以在 __init__ 里面：

self.val = x

意思是：

给当前这个节点添加一个属性 val，值是 x。
```

实际编程中:

```python 
class Node: 
  def __init__(self, prev, element, next):
    self.val = element
    self.next = next
    self.pre = pre 
```



### 好处

- 提高内存的利用效率
- 从来不需要考虑扩缩容和数据搬移的问题，理论上讲，链表是没有容量限制的（除非把所有内存都占满，这不太可能）
- 坏处: 数组最大的优势是支持通过索引快速访问元素，而链表就不支持。



## 单链表操作

创建单链表

``` java
class ListNode{
  int val; 
  ListNode next; 
  ListNode(int x){
    val = x; 
  }
}

// 输入一个数组,转化为单链表
ListNode createLinkedList(int[] arr){
  if (arr == null || arr.length == 0){
    return null; 
  }
  
  ListNode head = new ListNode(arr[0]);
  ListNode cur = head; 
  for(int i = 0; i < arr.length; i++){
    cur.next = new ListNode(arr[i]);
    cur = cur.next;
  }
  
  return head; 
}
```



### 查, 改

##### 单链表的遍历/查找/修改

我想访问单链表的每一个节点，并打印其值

- 只能用 for 循环从头结点开始往后找

```java
//创建一条单链表
ListNode head =  createLinkedList(new int[]{1, 2, 3, 4, 5});

// 遍历单链表
for (ListNode p = head;  p != null; p = p.next){
  System.out.println(p.val);
}
```

*这个操作的最坏时间复杂度是 O(n)*，其中 n 是链表的长度**



### 增

##### 在单链表头部插入新元素

我们会持有单链表的头结点，所以只需要将插入的节点接到头结点之前，并将新插入的节点作为头结点即可。

```java
//创建一条单链表
ListNode head = createLinkedList(new int[]{1,2,3,4,5});

// 在单链表头部插入一个新节点 0
ListNode newNode = new ListNode(0);
newNode.next = head; 
head = newNode; 
```

这个操作的时间复杂度是 O(1)



##### 在单链表尾部插入新元素

```java
//创建一条单链表
ListNode head = createLinkedList(new int[]{1,2,3,4,5});

// 在单链表尾部插入一个新节点 6
ListNode p = head; 

//先走到链表的最后一个节点
while (p.next != null){
  p = p.next
}

// 现在 p 就是链表的最后一个节点
// 在 p 后面插入新节点
p.next = new ListNode(6); 
```

这个操作的时间复杂度是 O(N)

- 因为需要遍历

  

#####  在单链表中间插入元素

我们还是要先找到要插入位置的前驱节点，然后操作前驱节点把新节点插入进去：

``` java
ListNode head = createLinkedList(new int[]{1,2,3,4,5});

ListNode p = head; 

// 在第 3 个节点后面插入一个新节点 66
// 先要找到前驱节点，即第 3 个节点

for(int i = 0; i< 2; i++){
  p = p.next;
}

// 此时 p 指向第 3 个节点
// 组装新节点的后驱指针

ListNode newNode = new ListNode(66);

newNode.next = p.next; 

p.next = newNode; 
```

> 难点 - 易错点: 
>
> - 应该先让新的节点指向之后的节点, 如果直接让`p.next = newNode` 后面的节点就都丢失了

这个操作的时间复杂度是 O(N)

- 因为需要先找到插入位置的前驱节点。



### 删除

##### 在单链表中删除一个节点

删除一个节点，首先要找到要被==删除节点的前驱节点==，然后把这个前驱节点的 `next` 指针指向被删除节点的下一个节点。这样就能把被删除节点从链表中摘除了。

``` java
ListNode head = createLinkedList(new int[]{1,2,3,4,5});

// 删除第 4 个节点，要操作前驱节点
ListNode p = head; 
for(int i = 0; i< 2; i++){
  p = p.next;
}

p.next = p.next.next; 

```

这个操作的时间复杂度是 O(N)

- 因为需要先找到插入位置的前驱节点。
