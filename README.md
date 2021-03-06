# TallyBook - 简易记账本

## 查看项目

可直接下载 zip 或 clone git repo 并使用 local websever 打开 index.html 文件查看

vs code 可直接使用 live server -> go live 查看
（因为 csv 文件被放在本地文件夹中，浏览器无法直接加载）

## 基础功能：

- 首页加载 bill.csv 所有内容
- 可使用下拉菜单筛选月份查看当月账单内容,并统计条目数量
- 账单上方统计收入和支出情况，会随选择的月份变化
- 用户可添加新的账单和新的标签

## 使用技术栈：

- HTML, CSS, JavaScript
- 使用 fetch API 加载 csv 数据
- OS: Windows

## 需求分析及实现思路

1. 展示数据和统计支出、收入情况
   展示数据是需求中最简单的部分，只需要用 JS 创建新的 Dom 并添加到父级容器中。在此思路上我根据 CSV 文件中的内容创建了模拟数据，以数组的形式储存，其中每一条数据为一个 Object。
   通过 Array.reduce 实现支出和收入的统计。
   首先创建静态 APP，所有内容以 hard code 实现，并调整 style。
   然后在通过 JS 将展示的内容动态创建,减少 HTML 中单条元素的粘贴复制。

2. 支持使用者添加账单
   添加账单的部分使用 form 和 input 来获取用户输入的数据，点击添加后将获取的字段 push 到数组中并展示和重新统计。
   其中出现的问题是点击提交后页面会跳转到新的 route 中，导致新添加的数据没有正常显示。\
    原因：form 中提交的默认操作和普通的按钮不同 \
    解决方法：阻止 form 中按钮的点击默认事件 

3. 月份筛选
   这里使用了 select 元素创建下拉菜单，并使用 Array.filter 来筛选用户选择的月份数据。筛选出的数据储存在新的数组中展示和重新统计。\
    在之前展示数据的时候已将时间戳创建为 Date 类型，此处可以用 getMonth 和 option 中的 value 来匹配完成筛选。

4. CSV 数据加载
   在上述功能全部完成之后，才开始真正读取数据，也是在这个项目中刚开始最困扰的部分。
   首先由于前面功能的实现都是基于数据保存为数组形式的前提下，在读取 csv 数据后同样希望能保存在数组中。
   刚开始使用了 papaparse 和 d3 两种插件来读取 csv 数据，都没有达到想要的效果。papaparse 在读取 bill.csv 的时候会导致页面卡死，用户体验太差。d3 虽然将每一条数据都读取为了 object，但一次 ajax 完成后只读取了一条数据，无法将它们全部保存在数组中。虽然可以展示数据，但收入和支出的统计变的很麻烦所以排除。\
    后发现 fileReader 可以读取用户上传的文件并读取为 String 格式，是一种实现方法，但现阶段希望能够读取本地文件。\
    最后使用了 fetch API，可以将文件作为 http 请求，并且 fetch 返回的是 promise，可以避免回调地狱，在代码编写上可读性更佳。缺点是需要 local server，使用了 vs code 中的插件解决。\
    将 fetch 的响应转换为 text，并对得到的 String 进行处理并保存为数组。

功能全部实现后，对代码重构，并测试用户输入的边界情况。
比如 type 只能输入 0 或者 1，所以将原本输入 type 的 input 改变为 select，让用户选择。
同样 category 最开始是让用户输入对应的 id，后调整为 select 让用户选择对应的名字。
加入提示信息。当用户创建成功后会提示创建成功字样，required 的部分没有添加的时候提示对应的 alert 字样。

5. 附加功能：账单二次筛选，可对选择的月份数据二次通过分类筛选
在选择月份之后再选择分类，可对展示数据二次筛选。（经测试，如果月份为total时，点击分类时无法筛选。在分类已选择后，再改变月份，此时只筛选了月份，需要重新选择分类才可以筛选）
在月份的select上监听change事件，对数据进行第一次筛选。在此事件中监听分类的change事件，完成第二次筛选。
