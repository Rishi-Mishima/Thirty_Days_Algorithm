# 队列QUEUE/栈STACK基本原理

```markdown 
力扣 20. 有效的括号(🟢)
```





计算机的两种存储方式

- 顺序存储（数组）
- 链式存储（链表）



其实队列和栈都是「操作受限」的数据结构。

- 说它操作受限，主要是和基本的数组和链表相比，它们提供的 API 是不完整的。
- 数组链表都可以增删改查

但是对于队列和栈，它们的操作是受限的：

- **队列只能在一端插入元素，另一端删除元素；**

- **栈只能在某一端插入和删除元素**。

  说白了就是把数组链表提供的 API 删掉了一部分，只保留头尾操作元素的 API 给你用。

形象地理解，队列只允许在队尾插入元素，在队头删除元素

栈只允许在栈顶插入元素，从栈顶删除元素。

这个图中把栈竖着画，队列横着画，只是为了更形象，但实际上它们底层都是数组和链表实现的，后面会讲到：

<img src="https://labuladong.online/images/algo/stack-queue/1.jpg" alt="img" style="zoom:50%;" />

队列就像排队买票，先来的先离开，后来的后离开；栈就像一摞盘子，最先放的压在最下面，最后放的留在最上面，拿的时候也是最上面的先被拿走。

>  所以我们常说，队列是一种「先进先出」的数据结构，栈是一种「先进后出」的数据结构，就是这个道理

**队列 Queue：先进先出 FIFO**
 像排队买票：先来的人先走。

**栈 Stack：先进后出 LIFO**
 像一摞盘子：最后放上去的先拿走。

| 数据结构 | 插入            | 删除           | 查看          | 特点 |
| -------- | --------------- | -------------- | ------------- | ---- |
| 队列     | `push` 队尾插入 | `pop` 队头删除 | `peek` 看队头 | FIFO |
| 栈       | `push` 栈顶插入 | `pop` 栈顶删除 | `peek` 看栈顶 | LIFO |

易错点: 

- pop = 删除并返回栈顶元素



**栈适合解决：**

括号匹配、表达式计算、单调栈、DFS、函数调用栈。

**队列适合解决：**

BFS、层序遍历、任务调度、滑动窗口、消息队列。



```java
// 队列的基本 API
class MyQueue<E> {
    // 向队尾插入元素，时间复杂度 O(1)
    void push(E e);

    // 从队头删除元素，时间复杂度 O(1)
    E pop();

    // 查看队头元素，时间复杂度 O(1)
    E peek();

    // 返回队列中的元素个数，时间复杂度 O(1)
    int size();
}

// 栈的基本 API
class MyStack<E> {
    // 向栈顶插入元素，时间复杂度 O(1)
    void push(E e);

    // 从栈顶删除元素，时间复杂度 O(1)
    E pop();

    // 查看栈顶元素，时间复杂度 O(1)
    E peek();

    // 返回栈中的元素个数，时间复杂度 O(1)
    int size();
}
```



#### 面试: 

```java
ArrayList<Integer> arr = new ArrayList<>();

arr.add(1);
arr.add(2);
arr.add(3);

arr.remove(arr.size() - 1);

为什么删除最后一个元素是 O(1)，而不是 O(n)？
  
  没有任何元素需要移动。
  
所以工作量就是：

找到最后一个元素
删除
size--

结束。

只做了几个固定操

所以时间复杂度 O(1)
  
  
>  那为什么删除第一个元素不是 O(1)？
  需要For循环移动, 所以是O(N)
```







---



## 用链表实现队列/栈

### 栈

```java
import java.util.LinkedList;

// 用链表作为底层数据结构实现栈
public class MyLinkedStack<E> {
    private final LinkedList<E> list = new LinkedList<>();

    // 向栈顶加入元素，时间复杂度 O(1)
    public void push(E e) {
        list.addLast(e);
    }

    // 从栈顶弹出元素，时间复杂度 O(1)
    public E pop() {
        return list.removeLast();
    }

    // 查看栈顶元素，时间复杂度 O(1)
    public E peek() {
        return list.getLast();
    }

    // 返回栈中的元素个数，时间复杂度 O(1)
    public int size() {
        return list.size();
    }

    public static void main(String[] args) {
        MyLinkedStack<Integer> stack = new MyLinkedStack<>();
        stack.push(1);
        stack.push(2);
        stack.push(3);

        System.out.println(stack.peek()); // 3
        System.out.println(stack.pop()); // 3
        System.out.println(stack.peek()); // 2
    }
}
```

```markdown
> 栈顶 既可以是链表头, 也可以是链表尾部

上面这段代码相当于是把双链表的尾部作为栈顶，在双链表尾部增删元素的时间复杂度都是 O(1)，符合要求。

当然，你也可以把双链表的头部作为栈顶，因为双链表头部增删元素的时间复杂度也是 O(1)，所以这样实现也是一样的。只要做几个修改 addLast -> addFirst，removeLast -> removeFirst，getLast -> getFirst 就行了。

==因为双向链表在头部和尾部都能在 O(1) 时间完成插入和删除。关键不是选头还是尾，而是 push 和 pop 必须始终操作同一端，这样才能保持栈“后进先出（LIFO）”的特性== 
```







### 用链表实现队列

```java
import java.util.LinkedList;

// 用链表作为底层数据结构实现队列
public class MyLinkedQueue<E> {
    private final LinkedList<E> list = new LinkedList<>();

    // 向队尾插入元素，时间复杂度 O(1)
    public void push(E e) {
        list.addLast(e);
    }

    // 从队头删除元素，时间复杂度 O(1)
    public E pop() {
        return list.removeFirst();
    }

    // 查看队头元素，时间复杂度 O(1)
    public E peek() {
        return list.getFirst();
    }

    // 返回队列中的元素个数，时间复杂度 O(1)
    public int size() {
        return list.size();
    }

    public static void main(String[] args) {
        MyLinkedQueue<Integer> queue = new MyLinkedQueue<>();
        queue.push(1);
        queue.push(2);
        queue.push(3);

        System.out.println(queue.peek()); // 1
        System.out.println(queue.pop()); // 1
        System.out.println(queue.pop()); // 2
        System.out.println(queue.peek()); // 3
    }
}
```



## 用数组实现栈/队列

### 用数组实现栈

把动态数组的尾部作为栈顶，然后调用动态数组的 API 就行了。因为数组尾部增删元素的时间复杂度都是 O(1)

>  数组实现栈，一定把栈顶放数组尾部 - 符合时间复杂度

```java
// 用数组作为底层数据结构实现栈
public class MyArrayStack<E> {
    private ArrayList<E> arr = new ArrayList<>();

    // 向栈顶加入元素，时间复杂度 O(1)
    public void push(E e) {
        arr.add(e);
    }

    // 从栈顶弹出元素，时间复杂度 O(1)
    public E pop() {
        return arr.remove(arr.size() - 1);
    }

    // 查看栈顶元素，时间复杂度 O(1)
    public E peek() {
        return arr.get(arr.size() - 1);
    }

    // 返回栈中的元素个数，时间复杂度 O(1)
    public int size() {
        return arr.size();
    }
}
```

```
按照我们之前实现 MyArrayList 的逻辑，是不行的。因为数组头部增删元素的时间复杂度都是 o(N)，不符合要求。

但是我们可以改用前文 
环形数组技巧中实现的 CycleArray 类，这个数据结构在头部增删元素的时间复杂度是 O(1)，符合栈的要求。

你直接调用 CycleArray 的 addFirst 和 removeFirst 方法实现栈的 API 就行，我这里就不写了。
```



### 用数组实现队列

```java
public class MyArrayQueue<E> {
    private CycleArray<E> arr;

    public MyArrayQueue() {
        arr = new CycleArray<>();
    }

    public void push(E t) {
        arr.addLast(t);
    }

    public E pop() {
        return arr.removeFirst();
    }

    public E peek() {
        return arr.getFirst();
    }

    public int size() {
        return arr.size();
    }
}
```





---

### 手写代码练习



#### 用链表实现栈

```java 
import java.util.LinkedList; 

class MyStack<E>{
  priviate LinkedList<E> list = new LinkedList<>(); 
  
  public void push(E e){
    list.addLast(e); 
  }
  
  public E pop(){
    return list.removeLast(); 
  }
  
  public E peek(){
    return list.getLast();
  }
  
  public int size(){
    return list.size; 
  }
  
  public boolean isEmpty(){
    return list.isEmpty(); 
  }
}
```

#### 



#### [力扣20. 有效的括号](https://leetcode.cn/problems/valid-parentheses/)

细节

- 由于括号两两一对，所以 s 的长度必须是偶数。如果 s 的长度是奇数，可以直接返回 false。
- 我们可以创建一个哈希表（或者数组），保存每个右括号对应的左括号，这样可以直接判断栈顶的左括号是否与右括号为同一类型，从而省去大量 if-else 判断。

### 

```java
Deque 是一种接口（Interface）。 - 双端队列
  
可以理解成：它规定了"应该有哪些功能"，但是自己不负责实现。
```

把 Deque 当 Stack 用。

这是 Java 官方推荐写法。

```markdown
Character 范型是因为括号都是字符不是字符串。
```



```java
class Solution {
    public boolean isValid(String s) {
        int n = s.length(); 
        if (n % 2 == 1 ) return false; 

      // 这个里面只有右括号, 
        Map<Character, Character> pairs = new HashMap<Character, Character>() {{
          // 右括号作为Key
            put(')', '(');
            put(']', '[');
            put('}', '{');
        }};
//因为当我们遇到右括号时，可以直接查它需要匹配谁。

//创建一个栈，用来存放左括号。虽然写的是 Deque，但这里把它当作 栈 来用。
        Deque<Character> stack = new LinkedList<>(); 

        for (int i = 0; i < n; i++){
          //遍历字符串
          **
            i = 0, ch = '('
i = 1, ch = '['
i = 2, ch = ']'
i = 3, ch = ')'
            **
            char ch = s.charAt(i); 
						
          	// 开始匹配 - 判断的是：当前字符是不是右括号。
            if(pairs.containsKey(ch)){
              //这里有两个失败情况。
              //情况 1：栈是空的: 第一个字符就是右括号。但是栈里没有任何左括号可以匹配它。所以直接 false。
              //情况 2：栈顶不是它需要的左括号
                if(stack.isEmpty()|| stack.peek() != pairs.get(ch)){
                    return false; 
                }
								
              
              //匹配成功就弹出
                stack.pop();
            } else{
              
              //如果不是右括号，就是左括号
              //这个 stack.push(ch) 实际上只会把左括号放进去。
                stack.push(ch);
            }
        }
      
      //最后判断栈是否为空 - 因为可能还有左括号没被匹配。
      //全部都是左括号，遍历时不会报错。但是最后栈里还有东西
      //只有最后栈空了，才说明所有左括号都成功匹配了
        return stack.isEmpty();

        

    }
}
```

