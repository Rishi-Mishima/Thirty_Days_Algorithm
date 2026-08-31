# 环形数组的实现与技巧

> 环形数组技巧利用求模（余数）运算，将普通数组变成逻辑上的环形数组，可以让我们用 O(1)的时间在数组头部增删元素。



## 环形数组的原理

用逻辑把数组变成环: 

数组可能是环形的么？不可能。数组就是一块线性连续的内存空间，怎么可能有环的概念？

但是，我们可以在「逻辑上」把数组变成环形的，比如下面这段代码：

```java
// 长度为 5 的数组
int[] arr = new int[]{1, 2, 3, 4, 5};
int i = 0;
// 模拟环形数组，这个循环永远不会结束
while (i < arr.length) {
    System.out.println(arr[i]);
    i = (i + 1) % arr.length;
}
```

关键点在于: 

- ==取模==

>  **这段代码的关键在于求模运算 `%`，也就是求余数**。当 `i` 到达数组末尾元素时，`i + 1` 和 `arr.length` 取余数又会变成 0，即会回到数组头部，这样就在逻辑上形成了一个环形数组，永远遍历不完。

**注意看最后一步**：当索引到达数组末尾 `4` 时，加 1 后对长度取余，结果变回了 `0`。这就让索引从尾部“跳”回了头部，实现了循环。

2. 为什么循环不会结束？

代码中的循环条件是 `while (i < arr.length)`。

- 因为 `i` 是通过 `% arr.length` 计算得到的，所以 `i` 的值永远在 `[0, arr.length - 1]` 之间。
- 这意味着 `i` **永远小于** `arr.length`。
- 因此，`i < arr.length` 这个条件**恒为真**，循环永远不会停止，程序会一直打印 `1, 2, 3, 4, 5, 1, 2, 3...`

3. 环形数组的实际意义

虽然这段示例代码是一个死循环，但它展示了环形数组技巧的底层原理：**通过维护一个指针，并利用 `%` 运算处理边界，让指针在数组范围内循环移动。**

在真正的环形数组实现中（如文章后面的 `CycleArray` 类），我们不会让它无限循环，而是配合 `start` 和 `end` 指针以及元素计数 `count` 来控制有效数据的范围，从而实现 O(1)*O*(1) 时间复杂度的头部增删操作



```
核心原理
上面只是让大家对环形数组有一个直观地印象，环形数组的关键在于，它维护了两个指针 start 和 end，start 指向第一个有效元素的索引，end 指向最后一个有效元素的下一个位置索引。

这样，当我们在数组头部添加或删除元素时，只需要移动 start 索引，而在数组尾部添加或删除元素时，只需要移动 end 索引。

当 start, end 移动超出数组边界（< 0 或 >= arr.length）时，我们可以通过求模运算 % 让它们转一圈到数组头部或尾部继续工作，这样就实现了环形数组的效果。
```



### 实践

#### 易错点: 开闭空间

- 环形数组的区间被定义为左闭右开
  - 最方便处理

```
关键点、注意开闭区间
在我的代码中，环形数组的区间被定义为左闭右开的，即 [start, end) 区间包含数组元素。所以其他的方法都是以左闭右开区间为基础实现的。

那么肯定就会有读者问，为啥要左闭右开，我就是想两端都开，或者两端都闭，不行么？

理论上，你可以随意设计区间的开闭，但一般设计为左闭右开区间是最方便处理的。

因为这样初始化 start = end = 0 时，区间 [0, 0) 中没有元素，但只要让 end 向右移动（扩大）一位，区间 [0, 1) 就包含一个元素 0 了。

如果你设置为两端都开的区间，那么让 end 向右移动一位后开区间 (0, 1) 仍然没有元素；如果你设置为两端都闭的区间，那么初始区间 [0, 0] 就已经包含了一个元素。这两种情况都会给边界处理带来不必要的麻烦，如果你非要使用的话，需要在代码中做一些特殊处理。
```



#### 代码实现

```java
public class CycleArray<T> {
    private T[] arr;
    private int start;
    private int end;
    private int count;
    private int size;

    public CycleArray() {
        this(1);
    }

    @SuppressWarnings("unchecked")
    public CycleArray(int size) {
        this.size = size;
        // 因为 Java 不支持直接创建泛型数组，所以这里使用了类型转换
        this.arr = (T[]) new Object[size];
        // start 指向第一个有效元素的索引，闭区间
        this.start = 0;
        // 切记 end 是一个开区间，
        // 即 end 指向最后一个有效元素的下一个位置索引
        this.end = 0;
        this.count = 0;
    }

    // 自动扩缩容辅助函数
    @SuppressWarnings("unchecked")
    private void resize(int newSize) {
        // 创建新的数组
        T[] newArr = (T[]) new Object[newSize];
        // 将旧数组的元素复制到新数组中
        for (int i = 0; i < count; i++) {
            newArr[i] = arr[(start + i) % size];
        }
        arr = newArr;
        // 重置 start 和 end 指针
        start = 0;
        end = count;
        size = newSize;
    }

    // 在数组头部添加元素，时间复杂度 O(1)
    public void addFirst(T val) {
        // 当数组满时，扩容为原来的两倍
        if (isFull()) {
            resize(size * 2);
        }
        // 因为 start 是闭区间，所以先左移，再赋值
        start = (start - 1 + size) % size;
        arr[start] = val;
        count++;
    }

    // 删除数组头部元素，时间复杂度 O(1)
    public void removeFirst() {
        if (isEmpty()) {
            throw new IllegalStateException("Array is empty");
        }
        // 因为 start 是闭区间，所以先赋值，再右移
        arr[start] = null;
        start = (start + 1) % size;
        count--;
        // 如果数组元素数量减少到原大小的四分之一，则减小数组大小为一半
        if (count > 0 && count == size / 4) {
            resize(size / 2);
        }
    }

    // 在数组尾部添加元素，时间复杂度 O(1)
    public void addLast(T val) {
        if (isFull()) {
            resize(size * 2);
        }
        // 因为 end 是开区间，所以是先赋值，再右移
        arr[end] = val;
        end = (end + 1) % size;
        count++;
    }

    // 删除数组尾部元素，时间复杂度 O(1)
    public void removeLast() {
        if (isEmpty()) {
            throw new IllegalStateException("Array is empty");
        }
        // 因为 end 是开区间，所以先左移，再赋值
        end = (end - 1 + size) % size;
        arr[end] = null;
        count--;
        // 缩容
        if (count > 0 && count == size / 4) {
            resize(size / 2);
        }
    }

    // 获取数组头部元素，时间复杂度 O(1)
    public T getFirst() {
        if (isEmpty()) {
            throw new IllegalStateException("Array is empty");
        }
        return arr[start];
    }

    // 获取数组尾部元素，时间复杂度 O(1)
    public T getLast() {
        if (isEmpty()) {
            throw new IllegalStateException("Array is empty");
        }
        // end 是开区间，指向的是下一个元素的位置，所以要减 1
        return arr[(end - 1 + size) % size];
    }

    public boolean isFull() {
        return count == size;
    }
    
    public int size() {
        return count;
    }

    public boolean isEmpty() {
        return count == 0;
    }
}
```

