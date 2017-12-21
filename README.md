# fis-spriter-csssprites

基于FIS的csssprites，由[fis-spriter-csssprites](https://github.com/fex-team/fis-spriter-csssprites) 修改而来，具体说明请访问原项目了解

### 特性
在官方基础上，添加支持图片分组合并、@media处理、合并路径指定、rem支持

####@media处理
样式中存在的媒体查询，往往是需要做响应式兼容，大多数情况下需要跟其他图片分开处理，如retina处理。
所以，将**@media**当作单独的一部分样式处理，生成的css也写入到@media中，完美解决原先合并处理后生成的样式混乱问题。

<table>
    <tr>
        <th>query</th>
        <th>说明</th>
    </tr>
    <tr>
        <td>?__sprite</td>
        <td>标识图片要做合并</td>
    </tr>
    <tr>
        <td>?__sprite=group</td>
        <td>标识图片合并到"group_z.png"</td>
    </tr>
</table>

> `group`只支持“字母、数字、-、_”

### 配置

* 启用 fis-spriter-csssprites 插件

```javascript
fis.match('::package', {
    spriter: fis.plugin('csssprites-group')
});
```

* 其他设置，更多详细设置请参考[fis-spriter-csssprites](https://github.com/fex-team/fis-spriter-csssprites)

```javascript
fis.config.set('settings.spriter.csssprites-group', {
    //图片缩放比例
    scale: 1,
    //1rem像素值
    rem: 50,
    // 默认单位
    unit: 'px',
    //图之间的边距
    margin: 10,
    //使用矩阵排列方式，默认为线性`linear`
    layout: 'matrix',
    //合并图片存到/img/
    to: '/img'
});

fis
.match('vue/*.css', {
    // 这里的spriteTo为最高优先匹配，会覆盖全局的to设置
    spriteTo : 'img/pkg'
})
```

> `rem`： 若设置了，则默认使用`rem`作为单位

> `unit`： 可覆盖默认单位，不影响`background-size`识别的单位

> `to`： 可以为相对路径（相对当前css路径）、绝对路径（项目根路径）

> `spriteTo`： 作为文件的to设置，为最高优先匹配，与`to`一样支持相对、绝对路径


### 单位自动识别
目前支持在特有情况下自动识别单位，若为rem，则根据`settings.rem`转换单位
此时忽略`settings.unit`设置，`background-size`**强制使用识别的单位**(`px`、`rem`)

* 当有`background-size`时，如下：

```css
.icon {
    background: url('img/icon.png?__sprite') no-repeat;
    background-size: .5rem .5rem;
}
```

* 当`background-size:contain`时、且存在`windth`、`height`，如下：

```css
.icon {
    display: inline-block;
    width: .5rem;
    height: .5rem;
    background: url('img/icon.png?__sprite') no-repeat;
    background-size: contain;
}
```

> 以上两个例子是等价的，都会使用rem作为单位处理

对于层叠的样式，因为条件复杂，无法正确识别上下文，所以不支持组合的样式`background-size:contain`匹配，如下：

> 使用**clean-css**处理后的样式，部分相同的`width`、`height`等属性会被移位，所以会识别失败，建议减少使用`contain`匹配单位

```css
.icon {
    display: inline-block;
    width: .5rem;
    height: .5rem;
}
.icon-1 {
    background: url('img/icon.png?__sprite') no-repeat;
    background-size: contain;
}
```
> 这种情况是无法识别成功的
