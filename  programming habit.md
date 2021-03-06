# 编程习惯

本文意在记录平时工作或者学习中希望自己能养成的编程习惯或者代码规范。不定期更新。

1.任何时候都不推荐省略分号；

2.虽然条件控制语句（如if 语句）只在执行多条语句的情况下才要求使用代码块（即用分号括起来），但最佳实践始终是在控制语句中使用代码块，即使代码块中只有一条语句。 

3.可以使用一条语句定义多个变量，每个变量用逗号分隔即可：

```
var message = 'hi',
  found = false,
  age = 29;
```

虽然代码里的换行和变量缩进不是必需的，但这样做可以提高可读性。

4.合理利用注释便于给代码划分模块，提高可读性，便于管理；定义函数时要注释清楚变量&函数的含义与作用。
```
/** ======================= 模块划分说明 =============================== */
/**
 * 函数说明
 * @param {params1} 参数1说明
 * @param {params2} 参数2说明
 * @param {params3} 参数3说明
 */
export const function = ({params1, params2, params3}) =>
  axios.$post('url', {
    params1,
    params2,
    params3
  })
```
5.消除魔法数字
```
//正例
const WIDTH = 200;
const Dialog = new Dialog(WIDTH);
//反例
const Dialog = new Dialog(200);
```