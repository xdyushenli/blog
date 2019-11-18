---
title: SheetJS常用API
date: 2019-11-17 13:18:47
tags: [ SheetJS, js ]
---
# SheetJS简介
`SheetJS`是前端操作`Excel`以及类似的二维表的最佳选择之一, 而`js-xlsx`是它的社区版本。
`js-xlsx`将注意力集中到了数据转换和导出上, 所以它支持相当多种类的数据解析和导出, 不仅仅局限于支持`xlsx`格式。
它可以：
* 解析符合格式的数据
* 导出符合格式的数据
* 利用中间层操作数据

可以运行在浏览器端和`Node`端。
## 浏览器端特色
* 纯浏览器端解析数据
* 纯浏览器端导出数据

## Node端特色
* 读写文件
* 流式读写
值得注意的是, 虽然`SheetJS`可以实现流式读写, 但本身并没有直接实现流式读写的`API`。

# 引入
## 浏览器端
浏览器端可以使用`script`标签进行引入。
```js
<script lang="javascript" src="dist/xlsx.full.min.js"></script>
```

## Node端
`Node`端可以使用`npm`进行引入。
```
npm install xlsx --save
```
在文件中, 可以通过`import`或`require`引入。
```js
const xlsx = require('xlsx');
import XlSX from 'xlsx';
```

# 常用API
## workbook & worksheet
首先要明确的两个概念是`workbook`和`worksheet`, 这两个对象分别对应了整个文件和一个文件中的一个表格。

### workbook
`workbook`有两种方式来获取：
* 读取已有的文件, 返回一个`workbook`对象。
* 通过`XLSX.utils.book_new()`创建空白的`workbook`对象。

常用到的`workbook`的属性有两个：
* `Sheets`：文件中的表格列表。
* `SheetNames`：文件中的表格名列表, 表现为数组。
需要获取`workbook`中的某个表格时, 可以这样获取：
```js
let sheetname = workbook.SheetNames[0];
let worksheet = workbook.Sheets[sheetname];
```

### worksheet
`worksheet`对象代表文件中的一个表格, 可以通过下标的形式访问表格中任意一小格的值：
```js
// 访问表格中A列第1行的值
worksheet['A1']
```
其返回值为一个对象, 一般具有两个属性：
* `v`：当前小格的值。
* `t`：当前小哥值的类型。

## 读取文件
`SheetJS`通过两种方式读取文件内容：
* `XLSX.read(data, read_options)`：读取`data`并解析。
* `XLSX.readFile(filename, read_options)`：读取`filename`文件并解析。
有关`read_options`的内容详见[read_options](https://github.com/SheetJS/sheetjs#parsing-options)。

## 写入数据
`SheetJS`通过三种方法写入数据, 这两种方法均会对数字、字符串、`null`和`undefined`、日期等类型进行自动解析：
* `XLSX.write(workbook, write_options)`：按照`workbook`中的数据转化为文件所需要的格式, 但不生成文件。
* `XLSX.writeFile(workbook, filename[, write_options])`：按照`workbook`对象生成文件。若在浏览器端, 会自动下载该文件。在Node端, 会自动生成该文件并保存到本地。
* `XLSX.writeFileAsync(filename, workbook, o, cb)`: 按照`workbook`对象生成文件。当`o`执行完毕后, 调用`cb`回调函数。
有关`write_options`的内容详见[`write_options`](https://github.com/SheetJS/sheetjs#writing-options)。

```js
// XLSX.writeFile
// 第一个参数为一个workbook对象, 第二个参数为所要生成文件的文件名
XLSX.writeFile(workbook, 'out.xlsb');

// XLSX.write
// 第一个参数为一个workbook对象, 第二个参数是对生成文件格式的一些设置
// 在本例中, 生成格式为xlsx的文件, 编码为base64
XLSX.write(workbook, {bookType:'xlsx', bookSST:false, type:'base64'})
// 由于其不生成文件, 而只是返回组成文件的数据, 所以很适合一些需要通过异步请求来修改服务器上的文件等场景
```
## 转换数据格式
`SheetJS`支持读取文件并把数据导出成任意格式, 可通过以下几个`API`完成, 传入的参数均是一个`worksheet`对象：
* `XLSX.utils.sheet_to_csv(worksheet)`：将表格数据转化为`csv`格式。
* `XLSX.utils.sheet_to_txt(worksheet)`：将表格数据转化为生成由`utf16`编码的`txt`格式。
* `XLSX.utils.sheet_to_html(worksheet)`：将表格转化为`html`文件。
* `XLSX.utils.sheet_to_json(worksheet)`: 将表格数据转化为`json`格式。

## 表格操作
* `XLSX.utils.aoa_to_sheet(Array[][])`：将二维数组转化为`worksheet`对象。
* `XLSX.utils.json_to_sheet(Object)`：将`js`对象转化为`worksheet`对象。
* `XLSX.utils.table_to_sheet(HTML)`：将`DOM`节点转化为`worksheet`对象（一般为`table`元素、`tr`元素和`th`元素）。
* `XLSX.utils.sheet_add_aoa(worksheet, Array[][])`：将二维数组中的数据添加到已有的`worksheet`中。
* `XLSX.utils.sheet_add_json(worksheet, Object)`：将`js`对象中的数据添加到已有的`worksheet`中。
* `XLSX.utils.book_append_sheet(workbook, worksheet, sheetname)`：将`worksheet`对象添加到`workbook`中, 并命名为`sheetname`。

# 参考链接
* [`SheetJS`项目`github`地址](https://github.com/SheetJS/sheetjs)
* [`js-xlsx`模块学习指南](https://segmentfault.com/a/1190000018077543)
