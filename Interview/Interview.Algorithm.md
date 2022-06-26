# 前端算法问题面试题目汇总★

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****

1. ## 二叉树的层序遍历

   **题目描述**：给你一个二叉树，请你返回其按 层序遍历 得到的节点值。 （即逐层地，从左到右访问所有节点）

   二叉树：`[3,9,20,null,null,15,7]`

   ```javascript
      3
     / \
    9  20
      /  \
     15   7

    返回其层次遍历结果：

    [
      [3],
      [9,20],
      [15,7]
    ]
   ```

   解题思路：
    - 层序遍历顺序就是广度优先遍历。
    - 不过在遍历时候需要记录当前节点所处的层级，方便将其添加到不同的数组中。

   方法一：

   ```javascript
   /**
    * Definition for a binary tree node.
    * function TreeNode(val) {
    *   this.val = val;
    *   this.left = this.right = null;
    * }
    */
   /**
    * @param {TreeNode} root
    * @return {number[][]}
    */
    var levelOrder = function(root) {
      if (!root) return [];
      // 节点 + 当前层级
      const queue = [[root, 0]];
      const res = [];

      while (queue.length) {
        const [p, level] = queue.shift();
        if (!res[level]) {
          res.push([p.val]);
        } else {
          res[level].push(p.val);
        }
        if (p.left) queue.push([p.left, level + 1]);
        if (p.right) queue.push([p.right, level + 1]);
      }
      return res;
    };
   ```

   时间和空间复杂度

   - 时间复杂度：o(n) => 最坏的情况下n是节点数
   - 空间复杂度：o(n) => 最坏的情况下n是节点数

   方法二：保证每次遍历到新层，就剔除掉所有老节点，新增新节点，即新陈代谢。这就可以一次性把每层元素依次放入数组中了。

   ```javascript
   /**
    * Definition for a binary tree node.
    * function TreeNode(val) {
    *   this.val = val;
    *   this.left = this.right = null;
    * }
    */
    /**
    * @param {TreeNode} root
    * @return {number[][]}
    */
    var levelOrder = function(root) {
      if (!root) return [];

      const queue = [root];
      const res = [];

      while (queue.length) {
        let len = queue.length;
        res.push([]);
        // 保证每次遍历到新层，就剔除掉所有老节点，新增新节点，即新陈代谢
        while (len--) {
          const p = queue.shift();
          res[res.length - 1].push(p.val);
          if (p.left) queue.push(p.left);
          if (p.right) queue.push(p.right);
        }
      }
      return res;
    };
   ```

   时间和空间复杂度

   - 时间复杂度：o(n) => 最坏的情况下n是节点数
   - 空间复杂度：o(n) => 最坏的情况下n是节点数

1. ## 二叉树的最大深度

    ```javascript
    /**
     * Definition for a binary tree node.
     * function TreeNode(val) {
     *     this.val = val;
     *     this.left = this.right = null;
     * }
     */
    /**
     * @param {TreeNode} root
     * @return {number}
     */

    // 递归
    var maxDepth = function (root) {
      if (!root) {
        return 0;
      } else {
        const left = maxDepth(root.left);
        const right = maxDepth(root.right);
        return Math.max(left, right) + 1;
      }
    };

    // BFS（Breadth-First Search）广度优先搜索
    const maxDepth = (root) => {
      if (!root) return 0;
      const nodeList = [root];
      let deep = 1;
      while (nodeList.length) { // 每一层的循环
        const nodeSize = nodeList.length; // 操作nodeList会影响到之前的长度
        for (let i = 0; i < nodeSize; i++) { // 每一层的左右节点
          const curentNode = nodeList.shift();
          if (curentNode.left) nodeList.push(curentNode.left);
          if (curentNode.right) nodeList.push(curentNode.right);
        }
        if (nodeList.length) deep += 1; // 避免最后为空也算进去
      }
      return deep;
    };

    //DFS （Depth-First-Search）深度优先搜索
    const maxDepth = (root) => {
      if (!root) return 0;
      const nodeList = [root];
      const lenList = [1];
      let max = 1;
      while (nodeList.length) {
        const curentNode = nodeList.shift();
        const len = lenList.shift();
        max = Math.max(len, max);
        if (curentNode.left) {
          nodeList.push(curentNode.left);
          lenList.push(len + 1); //len 没有自增很关键
        }
        if (curentNode.right) {
          nodeList.push(curentNode.right);
          lenList.push(len + 1); //len 没有自增很关键
        }
      }
      return max;
    };
    ```

1. ## 两数之和

   ```javascript
   给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 的那 两个 整数，并返回它们的数组下标。

   你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

   你可以按任意顺序返回答案。
   
   /**
    * @param {number[]} nums
    * @param {number} target
    * @return {number[]}
    */
    var twoSum = function (nums, target) {
      let len = nums.length;
      // 创建 MAP
      const MAP = new Map();
      // 由于第一个元素在它之前一定没有元素与之匹配，所以先存入哈希表
      MAP.set(nums[0], 0);
      for (let i = 1; i < len; i++) {
        // 提取共用
        let other = target - nums[i];
        // 判断是否符合条件，返回对应的下标
        if (MAP.get(other) !== undefined) return [MAP.get(other), i];
        // 不符合的存入hash表
        MAP.set(nums[i], i);
      }
    };
   ```

1. ## 两数之和 II - 输入有序数组

   ```javascript
    给定一个已按照升序排列的有序数组，找到两个数使得它们相加之和等于目标数。

    函数应该返回这两个下标值 index1 和 index2，其中 index1 必须小于 index2。

    说明:

    返回的下标值（index1 和 index2）不是从零开始的。
    你可以假设每个输入只对应唯一的答案，而且你不可以重复使用相同的元素。
    示例:

    输入: numbers = [2, 7, 11, 15], target = 9
    输出: [1,2]
    解释: 2 与 7 之和等于目标数 9 。因此 index1 = 1, index2 = 2 。

    /**
    * @param {number[]} numbers
    * @param {number} target
    * @return {number[]}
    */
    var twoSum = function (numbers, target) {
      let l = 0,
          r = numbers.length - 1,
          sum = 0;

      while (l < r) {
        sum = numbers[l] + numbers[r];

        if (sum === target) {
          return [l + 1, r + 1];
        }

        sum < target ? l++ : r--;
      }
    };
   ```

1. ## 不借助第三个数，让 `a, b` 两数值交换

    - 方法一：

      ```javascript
      a = a + b;
      b = a - b;
      a = a - b;
      ```

    - 方法二：异或运算

      ```javascript
      // a = 1, b = 2
      a = a^b; // 01 ^ 10 = 11
      b = a^b; // 11 ^ 10 = 01
      a = a^b; // 11 ^ 01 = 10
      ```

1. ## LRU(最近最少使用)缓存

    实现 `LRUCache` 类：
    - `LRUCache(int capacity)` 以**正整数**作为容量 `capacity` 初始化 `LRU` 缓存
    - `int get(int key)` 如果关键字 `key` 存在于缓存中，则返回关键字的值，否则返回 `-1`。
    - `void put(int key, int value)` 如果关键字 `key` 已经存在，则变更其数据值 `value`；如果不存在，则向缓存中插入该组 `key-value`。如果插入操作导致关键字数量超过 `capacity`，则应该**逐出**最久未使用的关键字。

    函数 `get` 和 `put` 必须以 `O(1)` 的平均时间复杂度运行。

    示例：

    ```js
    输入
    ["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
    [[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
    输出
    [null, null, null, 1, null, -1, null, -1, 3, 4]

    解释
    LRUCache lRUCache = new LRUCache(2);
    lRUCache.put(1, 1); // 缓存是 {1=1}
    lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
    lRUCache.get(1);    // 返回 1
    lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
    lRUCache.get(2);    // 返回 -1 (未找到)
    lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
    lRUCache.get(1);    // 返回 -1 (未找到)
    lRUCache.get(3);    // 返回 3
    lRUCache.get(4);    // 返回 4
    ```

    提示：

    ```js
    1 <= capacity <= 3000
    0 <= key <= 10000
    0 <= value <= 105
    最多调用 2 * 105 次 get 和 put
    ```

    解答：

    ```js
    /// ES5

    /**
     * @param {number} capacity
     */
    var LRUCache = function (capacity) {
      this.capacity = capacity
      this.map = new Map()
    }

    /** 
      * @param {number} key
      * @return {number}
      */
    LRUCache.prototype.get = function (key) {
      if (!this.map.has(key)) return -1

      const temp = this.map.get(key)
      this.map.delete(key)
      this.map.set(key, temp)
      return temp
    }

    /** 
      * @param {number} key 
      * @param {number} value
      * @return {void}
      */
    LRUCache.prototype.put = function (key, value) {
      if (this.map.has(key)) this.map.delete(key)
      this.map.set(key, value)
      if (this.map.size > this.capacity) this.map.delete(this.map.keys().next().value)
    }

    /**
      * Your LRUCache object will be instantiated and called as such:
      * var obj = new LRUCache(capacity)
      * var param_1 = obj.get(key)
      * obj.put(key,value)
      */

    /// ES6
    // 一个 Map 对象在迭代时会根据对象中元素的插入顺序来进行
    // 新添加的元素会被插入到 Map 的末尾，整个栈倒序查看
    class LRUCache {
      constructor(capacity) {
        this.map = new Map()
        this.capacity = capacity
      }
      get(key) {
        if (!this.map.has(key)) retuen -1
        const temp = this.map.get(key)
        this.map.delete(key)
        this.map.set(key, temp)
        return temp
      }
      put(key, value) {
        if (this.map.has(key)) this.map.delete(key)
        this.map.set(key, value)
        if (this.map.size > this.capacity) this.map.delete(this.map.keys().next().value)
      }
    }
    ```

1. ## LFU(最不经常使用)缓存

    实现 `LFUCache` 类：
    - `LFUCache(int capacity)` - 用数据结构的容量 `capacity` 初始化对象
    - `int get(int key)` - 如果键 `key` 存在于缓存中，则获取键的值，否则返回 `-1`
    - `void put(int key, int value)` - 如果键 `key` 已存在，则变更其值；如果键不存在，请插入键值对。当缓存达到其容量 `capacity` 时，则应该在插入新项之前，移除最不经常使用的项。在此问题中，当存在平局（即两个或更多个键具有相同使用频率）时，应该去除**最近最久未使用**的键

    为了确定最不常使用的键，可以为缓存中的每个键维护一个**使用计数器**。使用计数最小的键是最久未使用的键。

    当一个键首次插入到缓存中时，它的使用计数器被设置为 `1` (由于 `put` 操作)。对缓存中的键执行 `get` 或 `put` 操作，使用计数器的值将会递增。

    函数 `get` 和 `put` 必须以 `O(1)` 的平均时间复杂度运行。

    示例：

    ```js
    输入：
    ["LFUCache", "put", "put", "get", "put", "get", "get", "put", "get", "get", "get"]
    [[2], [1, 1], [2, 2], [1], [3, 3], [2], [3], [4, 4], [1], [3], [4]]
    输出：
    [null, null, null, 1, null, -1, 3, null, -1, 3, 4]

    解释：
    // cnt(x) = 键 x 的使用计数
    // cache=[] 将显示最后一次使用的顺序（最左边的元素是最近的）
    LFUCache lfu = new LFUCache(2);
    lfu.put(1, 1);   // cache=[1,_], cnt(1)=1
    lfu.put(2, 2);   // cache=[2,1], cnt(2)=1, cnt(1)=1
    lfu.get(1);      // 返回 1
                    // cache=[1,2], cnt(2)=1, cnt(1)=2
    lfu.put(3, 3);   // 去除键 2 ，因为 cnt(2)=1 ，使用计数最小
                    // cache=[3,1], cnt(3)=1, cnt(1)=2
    lfu.get(2);      // 返回 -1（未找到）
    lfu.get(3);      // 返回 3
                    // cache=[3,1], cnt(3)=2, cnt(1)=2
    lfu.put(4, 4);   // 去除键 1 ，1 和 3 的 cnt 相同，但 1 最久未使用
                    // cache=[4,3], cnt(4)=1, cnt(3)=2
    lfu.get(1);      // 返回 -1（未找到）
    lfu.get(3);      // 返回 3
                    // cache=[3,4], cnt(4)=1, cnt(3)=3
    lfu.get(4);      // 返回 4
                    // cache=[3,4], cnt(4)=2, cnt(3)=3
     ```

    提示：

    ```js
    0 <= capacity <= 104
    0 <= key <= 105
    0 <= value <= 109
    最多调用 2 * 105 次 get 和 put 方法
    ```

    解答

    ```js
    /// ES5
    /**
     * 定义节点
     * @param {*} key 
     * @param {*} val 
     */
    var Node = function (key, val) {
      this.key = key
      this.val = val
      this.times = 1 // 当前节点的 key 被使用的频率
      this.pre = null // 前一个节点的指针
      this.next = null // 后一个节点的指针
    }

    /**
     * 定义双向链表
     */
    var DoublyLinkedList = function () {
      this.head = new Node() // 头节点
      this.tail = new Node() // 尾节点
      this.head.next = this.tail // 初始化时，头节点的后一个节点为尾节点
      this.tail.pre = this.head // 初始化时，尾节点的前一个节点为头节点
    }

    DoublyLinkedList.prototype.removeNode = function (node) {
      // 1. 将当前节点的前一个节点的 next 指针指向当前节点的 next 指针
      node.pre.next = node.next
      // 2. 将当前节点的后一个节点的 pre 指针指向当前节点的 pre 指针
      node.next.pre = node.pre
    }

    DoublyLinkedList.prototype.addNode = function (node) {
      // 为了方便理解，不妨设当前只有头尾节点以及需要插入的该节点
      // 总的来说，就是分别处理该节点与头尾节点的 pre/next 指针
      // 1. 将 该节点的后一个节点 设置为 头节点的后一个节点(即尾节点)
      node.next = this.head.next
      // 2. 将 尾节点的前一个节点 设置为 该节点
      this.head.next.pre = node
      // 3. 将头节点的后一个节点设置为该节点
      this.head.next = node
      // 4. 将该节点的前一个节点设置为头节点
      node.pre = this.head
    }

    /**
     * 定义 LFU 类
     * @param {number} capacity
     */
    var LFUCache = function (capacity) {
      this.capacity = capacity // 总的容量
      this.size = 0 // 当前已使用的容量
      this.minTimes = 0 // 最小使用频率，为删除操作服务
      this.cacheMap = new Map() // key-value map
      this.timesMap = new Map() // 频率-(key，value，频率)
    };

    /**
     * @param {Node} node
     */
    LFUCache.prototype.increaseTimes = function (node) {
      // 总的来说，把该节点从旧频率对应的链表中移除，然后放进新频率对应的链表中
      // 获取该节点的使用频率
      let times = node.times
      // 获取该使用频率(旧频率)对应的链表
      let linkedList = this.timesMap.get(times)
      // 将该节点从旧频率对应的链表中移除
      linkedList.removeNode(node)
      // 同时满足以下两种情况时，更新 Times 的值
      // 1. 旧频率等于最小频率
      // 2. 该链表为空链表
      if (times === this.minTimes && linkedList.head.next === linkedList.tail) {
        this.minTimes = times + 1
      }
      // 增加该节点的使用频率，姑且称为 新频率
      node.times++
      // 获取新频率对应的链表
      linkedList = this.timesMap.get(times + 1)
      // 如果链表为空，则需要新建链表，并将其放入 timesMap
      if (!linkedList) {
        linkedList = new DoublyLinkedList()
        this.timesMap.set(times + 1, linkedList)
      }
      // 将新频率的节点放进链表中
      linkedList.addNode(node)
    }

    /** 
     * @param {number} key
     * @return {number}
     */
    LFUCache.prototype.get = function (key) {
      // 缓存中没有这个 key，直接返回 -1
      if (!this.cacheMap.has(key)) return -1
      // 获取缓存
      const node = this.cacheMap.get(key)
      // 将该节点的频率 +1
      this.increaseTimes(node)
      // 返回该节点的值
      return node.val
    };

    /** 
     * @param {number} key 
     * @param {number} value
     * @return {void}
     */
    LFUCache.prototype.put = function (key, value) {
      // 若缓存容量为 0，直接返回
      if (!this.capacity) return
      // 获取缓存中 key 对应的节点
      const node = this.cacheMap.get(key)
      if (node) {
        // 若节点存在，则只需要更新该节点的值以及频率
        node.val = value
        this.increaseTimes(node)
      } else {
        // 如果容量已被使用完，则需要移除 最不经常使用 的节点，以空出容量
        if (this.capacity === this.size) {
          // 获取最小使用频率所对应的双向链表
          const minTimesLinkedList = this.timesMap.get(this.minTimes)
          // 将该链表的尾节点的前一个节点移除(尾节点的前一个节点才是有效节点，尾节点充当哨兵作用)
          this.cacheMap.delete(minTimesLinkedList.tail.pre.key)
          minTimesLinkedList.removeNode(minTimesLinkedList.tail.pre)
          this.size--
        }
        // 将该值封装成节点并放进 cacheMap 中
        const newNode = new Node(key, value)
        this.cacheMap.set(key, newNode)
        // 同时需要将该节点插入 timesMap 中频率最小的双向链表中
        // 获取使用频率为 1 的双向链表
        let linkedList = this.timesMap.get(1)
        // 若使用频率为 1 的双向链表是空的，则创建该链表并放进 timesMap 中
        if (!linkedList) {
          linkedList = new DoublyLinkedList()
          this.timesMap.set(1, linkedList)
        }
        // 将新节点放入双向链表中，同时更新 size / minTimes
        linkedList.addNode(newNode)
        this.size++
        this.minTimes = 1
      }
    };

    /// ES6
    ```

1. ## 大数相加

    ```js
    const add = (a, b) => {
      const maxLength = Math.max(a.length, b.length)
      let overflow = false
      let sum = ''
      for(let i = 1; i <= maxLength; i++){
        const ai = a[a.length-i] || '0'
        const bi = b[b.length-i] || '0'
        let ci = parseInt(ai) + parseInt(bi) + (overflow ? 1 : 0)
        overflow = ci >= 10
        ci = overflow ? ci - 10 : ci
        sum = ci + sum 
      }
      sum = overflow ? '1' + sum : sum
      return sum
    }

    console.log(add("11111111101234567","77777777707654321"))
    console.log(add("911111111101234567","77777777707654321"))
    ```

1. ## 无重复字符的最长子串

    ```js
    var lengthOfLongestSubstring = function (s) {
      const length = s.length
      var max = current = start = 0, hash = {}
      for (let i = 0; i < length; i++) {
        if (hash[s[i]] > -1) {
          start = hash[s[i]] + 1 <= start ? start : hash[s[i]] + 1
          current = i - start + 1
        } else {
          current ++
        }
        hash[s[i]] = i
        max = Math.max(max, current)
      }
      return max
    };
    ```

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****
