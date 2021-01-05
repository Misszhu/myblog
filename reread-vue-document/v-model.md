V-model 是一个指令 在表单元素上实现数据的双向绑定。

它会根据控件的类型智能选择获取数据。比如<input> 就是在input(输入)时获取和更新数据，<select>就是在select(选择)时获取和更新数据

v-model根据表单自身的类型在表单元素的内部监听不同的属性变化，并抛出对应的事件：

text 和 textarea：value作为 property，抛出input事件

Checkbox 和 radio:  checked作为 property，抛出 changed事件

select：value作为prop，抛出change事件



值绑定

对select，radio，CheckBox，v-model绑定的值通常是字符串，可以通过v-bind把其他类型的value绑定到元素上。



修饰符

是指令 v-model 的修饰符

.lazy

对于input元素来说，都是input事件被触发时将输入框的值与数据进行同步，加了.lazy修饰符，会变成change事件被触发时将输入框的值与数据进行同步。

.number

将用户输入的值自动转换成数值。实际上Vue在底层将输入值用`parseFloat()` 解析再返回。如果不能用`parseFloat()` 解析，则返回原始值。

.trim

将用户输入的数据的首尾空白符去掉