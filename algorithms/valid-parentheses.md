本题为 leetcode [第 20 题](https://leetcode.cn/problems/valid-parentheses/description/)。

## <font style="color:rgba(0, 0, 0, 0.9);">题目描述</font>

> <font style="color:rgba(0, 0, 0, 0.9);">给定一个只包含</font><font style="color:rgba(0, 0, 0, 0.9);"> </font>`'('`<font style="color:rgba(0, 0, 0, 0.9);">,</font><font style="color:rgba(0, 0, 0, 0.9);"> </font>`')'`<font style="color:rgba(0, 0, 0, 0.9);">,</font><font style="color:rgba(0, 0, 0, 0.9);"> </font>`'{'`<font style="color:rgba(0, 0, 0, 0.9);">,</font><font style="color:rgba(0, 0, 0, 0.9);"> </font>`'}'`<font style="color:rgba(0, 0, 0, 0.9);">,</font><font style="color:rgba(0, 0, 0, 0.9);"> </font>`'['`<font style="color:rgba(0, 0, 0, 0.9);">,</font><font style="color:rgba(0, 0, 0, 0.9);"> </font>`']'`<font style="color:rgba(0, 0, 0, 0.9);"> </font><font style="color:rgba(0, 0, 0, 0.9);">的字符串</font><font style="color:rgba(0, 0, 0, 0.9);"> </font>`s`<font style="color:rgba(0, 0, 0, 0.9);">，判断字符串是否有效。有效字符串需满足：</font>
>
> 1. <font style="color:rgba(0, 0, 0, 0.9);">左括号必须用相同类型的右括号闭合。</font>
> 2. <font style="color:rgba(0, 0, 0, 0.9);">左括号必须以正确的顺序闭合。</font>
>
> **<font style="color:rgba(0, 0, 0, 0.9);">示例：</font>**
>
> + <font style="color:rgba(0, 0, 0, 0.9);">输入：</font>`"()[]{}"`<font style="color:rgba(0, 0, 0, 0.9);"> </font><font style="color:rgba(0, 0, 0, 0.9);">→ 输出：</font>`true`
> + <font style="color:rgba(0, 0, 0, 0.9);">输入：</font>`"([)]"`<font style="color:rgba(0, 0, 0, 0.9);"> </font><font style="color:rgba(0, 0, 0, 0.9);">→ 输出：</font>`false`
> + <font style="color:rgba(0, 0, 0, 0.9);">输入：</font>`"{[]}"`<font style="color:rgba(0, 0, 0, 0.9);"> → 输出：</font>`true`

## 题目分析

解题基本流程是遍历给定的字符串，判断括号是否“成对”出现，其中括号是可以嵌套的。遍历目标字符串时，遇到左括号需要想办法把左括号存起来，遇到右括号再找到最后访问的那个左括号，看两个括号是否匹配。

利用栈**先进后出**的特点，可以用栈存储左括号，遇到右括号时取出堆顶元素判断两个括号是否匹配。

注意，遍历完成时还要判断栈是否为空。如果不为空说明存在没有正确闭合的左括号，也不符合题目要求。

代码执行流程如下：

![](https://cdn.nlark.com/yuque/__mermaid_v3/807a3ddb6771b456960173093391bc89.svg)

## 代码实现

```javascript
/**
 * @param {string} s
 * @return {boolean}
 */
var isValid = function(s) {
    const stack = []
    const map = {
        ']': '[',
        '}': '{',
        ')': '(',
    }
  
    const regx = /[\[|\{|\(]/

    for(const char of s) {
        // 遇到左括号，入栈
        if(regx.test(char)) {
            stack.push(char)
        } else {
            if(map[char] !== stack.pop()) {
                return false
            }
        }
    }

    return stack.length === 0
};
```

<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(246, 246, 246);">以输入 </font>`s = "{[]}"`<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(246, 246, 246);"> 为例，执行过程如下：</font>


| **步骤** | **当前字符** |       **操作**       |    **栈状态变化**    |   **匹配结果**   |
| :------: | :----------: | :-------------------: | :------------------: | :---------------: |
|    1    |     `{`     |    左括号 → 入栈    |   `stack = ['{']`   |      无匹配      |
|    2    |     `[`     |    左括号 → 入栈    | `stack = ['{', '[']` |      无匹配      |
|    3    |     `]`     | 右括号 → 匹配栈顶`[` |   `stack = ['{']`   | 匹配成功，弹出`[` |
|    4    |     `}`     | 右括号 → 匹配栈顶`{` |     `stack = []`     | 匹配成功，弹出`{` |

## 复杂度分析


|    **维度**    |                        **分析**                        |
| :------------: | :----------------------------------------------------: |
| **时间复杂度** |   O(n)，每个字符仅遍历一次，入栈/出栈操作均为 O(1)。   |
| **空间复杂度** | O(n)，最坏情况下栈存储所有左括号（如输入全为左括号）。 |
