# 前端算法问题面试题目汇总★

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****

1. 二叉树的层序遍历

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

1. 二叉树的最大深度

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

1. 两数之和

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

1. 两数之和 II - 输入有序数组

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

1. 不借助第三个数，让 `a, b` 两数值交换
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

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****
