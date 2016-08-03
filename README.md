# 版面划分

## 输入
一张藏经图片和对应的文本，文本为UTF8格式，每行结尾以\r\n符号换行。

藏经图片中文字按列的方向排列，列按从右往左的顺序，图片中每一列与文本每一行精确对应。在图片中可能有一些和普通正文格式不一样的文字，这些文字在文本中会加上一些标记符号来区分出来。具体的标记符号如下：
- <> 中间的文字表示小字，即在文本中一组尖括号中间的文字为图片中的小字，如图片某一列中某一段位置有两列小字，则在文本中用两组尖括号表示，如<卷第二十><第一百张>；
- 【】中间的文字表示在正文旁的字；
- @@中间的文字表示题记；
- () 中间的文本表示轶号，或者音义文字，主要是指与藏经实际内容无关的文字。

对于文本中的空格，统一都用全角空格，在行首、行中间都可能会有空格，在图片相应的列中会有空白区域与空格对应。一列中的空白区域包含在其他正常的区域中一起输出，不必作为单独区域输出。

文本中的空行表示在图片中有空白列。空白列也当作一个区域输出。空白列的宽度可能比正常有文字的列的宽度大一些。

最后达到的目标是，对于每一列，将该列对应输出的几个区域拼接起来，能完整复原该列的原图。将所有列对应输出的区域拼接起来，能完整复原整个原图。

## 输出
一般情况下，输出藏经图片中每一列的区域和对应的文本，如果这一列中有特殊文字，则需要将特殊文字区域及相应的文本输出。如上面提到的图片某一列中某一段位置有两列小字，在文本中用两组尖括号表示，如<卷第二十><第一百张>，则分别输出“卷第二十”对应的图片区域和“第一百张”对应的图片区域。

## 举例说明
![带小字的列](https://raw.githubusercontent.com/dang-xia/LayoutSegmentation/master/samples/%E5%B8%A6%E5%B0%8F%E5%AD%97%E7%9A%84%E5%88%97.jpg)________![带小字的列划分后的结果](https://raw.githubusercontent.com/dang-xia/LayoutSegmentation/master/samples/%E5%B8%A6%E5%B0%8F%E5%AD%97%E7%9A%84%E5%88%97%E5%88%92%E5%88%86%E5%90%8E%E7%9A%84%E7%BB%93%E6%9E%9C.jpg)

如上面第一张图是藏经图片中的某一列，对应的文本为：
```
怛姪他<天可切後九篇初他><字悉同此音其一>富樓尼<念履切後九><篇音尼字悉>
```

做版面划分后的结果如右边第二张图所示。实际输出每个区域对应的四个坐标值：left, right, top, bottom。区域在横轴的区间范围为[left, right)，纵轴的区间范围为[top, bottom)，坐标值为在整个图片中的坐标。

程序在实际执行时，输入一个图片文件和一个文本文件，输出一个json格式的数组，表示每个区域的坐标及相应的文本，其中对应每一个区域，包含这些字段：
- text，表示区域的文本；
- mark，可选，对于有标记的特殊文本，mark为对应的标记符号中前一个符号，比如对于标记符号<>，mark就设为<；
- left，左边界坐标；
- right，右边界坐标；
- top，上边界坐标；
- bottom，下边界坐标；
- line_no，表示行号；
- region_no，表示在一行中区域的编号，从１开始计数。

比如输入样本中的2.jpg和2.txt，输出的json格式文本为：
```json
[{
    "text": "怛姪他",
    "left": 21,
    "right": 71,
    "top": 1,
    "bottom": 50,
    "line_no": 1,
    "region_no": 1
}, {
    "text": "天可切後九篇初他",
    "mark": "<",
    "left": 21,
    "right": 71,
    "top": 1,
    "bottom": 50,
    "line_no": 1,
    "region_no": 2
}, {
    "text": "字悉同此音其一",
    "mark": "<",
    "left": 21,
    "right": 71,
    "top": 1,
    "bottom": 50,
    "line_no": 1,
    "region_no": 3
}, {
    "text": "富樓尼",
    "left": 21,
    "right": 71,
    "top": 1,
    "bottom": 50,
    "line_no": 1,
    "region_no": 4
}, {
    "text": "念履切後九",
    "mark": "<",
    "left": 21,
    "right": 71,
    "top": 1,
    "bottom": 50,
    "line_no": 1,
    "region_no": 5
}, {
    "text": "篇音尼字悉",
    "mark": "<",
    "left": 21,
    "right": 71,
    "top": 1,
    "bottom": 50,
    "line_no": 1,
    "region_no": 6
}]
```
可参考文件samples目录中的文件2_output_json.txt。
