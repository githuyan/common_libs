# HTML

### 表格

```vue
<table>
    <tr>
        行
        <th> // 中心对齐
            列
        </th>
        <td> // 左对齐
            列
        </td>
    </tr>
</table>
```

### label  

> 提高用户体验，点击该标签中的文字，就会选中该标签所关联的空间按钮

```html
// 注意 显式指定 for ，注意 for 必须与 id 一致
// label 标签的作用是，不用准确的点击单选框，只需点击后面的 文字 就能够聚焦这个字段
<label for="license">
    <input type="checkbox" id="license" v-model="isAgree">同意协议
</label>
```

### input

> 各种表单输入

```html
<input type="number"> // 限制输入只能为数字
```

