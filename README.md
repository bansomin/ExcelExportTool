# ExcelExportTool 
## 简单强大的excel转json的工具
## 功能说明
- 使用简单，不需要额外的关联文件
- 批量处理excel文件
- 同一个excel文件中可配置多个json并导出
- 可对excel的sheet配置主从关系来输出任意多级json
- json的每一级都支持列表和字典配置
- 可在excel单元格中直接配置列表和字典作为下级内容
- json可输出为便于阅读的格式化文件或是省空间的字符串文件

## 工具依赖
- 基于python 3.6开发，python3 3.6及以上都行
- excel使用xlrd这个开源库解析
  - xlrd https://pypi.org/project/xlrd/1.2.0/
  - 用pip命令安装xlrd :   pip install xlrd==1.2.0
  - 注意最新的xlrd 2.0 一直无法正常读取excel文件，已安装的需要回退到1.2版

## 使用方法
配置好Config.json后双击ExcelExportTool.bat进行文件转换
### Config配置
~~~PYTHON
{
    #表头所在的行，可以在前面留出行加注释
    "headRow": 2,
    #是否四舍五入
    "round":true
    #生成的json是否格式化为方便阅读的json格式
    "format":  true,
    #是否忽略空值，为真则直接跳过空值项
    "ignoreEmpty": true,
    #放置源文件的目录
    "srcFolder":  "./excel",
    #输出json的目录
    "destFolder":  "./json",
}
~~~
### Excel配置
- Excel不能以~开头
- sheet名前面加上！则不会被读取
- 输出json名为sheet名

- 表格存在主从关系则仅输出主表，从表不会输出，理论上从表可以配置任意多级，主从表位置可以随意调整
- 没有主从关系的表会单独输出，相当于主表
- 有主从关系则从表名称作为主表的项，从表数据根据配置输出到该项中(从表为obj类型除外)
- 表格主从关系配置
  - 主表名称为正常表名，作为最后输出的表名
  - 从表名格式为  _从表名~主表名_ 
  - 从表中需要配置对应主表主键的列，表头以'~'开头，后面可以不跟任何内容

- 可对表名加上修饰符进行输出限定，格式为 _表名#修饰符_，修饰符可以为：
  - obj：该表的每一项作为单独的对象输出，如果是从表则直接单独将每一条数据作为子项目添加到上级表单中
  - dic：该表以字典的形式输出，每条数据的主键作为字典每一项的key，如果是从表则根据依赖的主表主键合并为字典并以输出到对应主表中
  - 不加限定或其他限定则均默认为列表输出，如果是从表则根据依赖的主表主键合并为列表并以输出到对应主表中
  - 找不到主表对应数据的从表数据不会被读取(比如主表用!注释掉，则对应从表数据也不会被读取)
  - 加限定的从表格式为 _从表名#修饰符~主表名_

- 表格数据基本配置
  - 键名为空或者健名前加上！则该单元格不会被读取
  - 主键前加上！则该行数据不会被读取
  - 主键以*开头，没有主键则默认除映射主表列以外的第一列为主键列
  - 数据类型会自动识别，也可在列名后面可以跟修饰符进行限定，格式为 _键名#修饰符_ 
  - 修饰符可以为：
    - int ：       如果是数值类型则强制转换为整形
    - float ：     浮点型,可通过参数设置小数位数，不设置则原样输出。格式：_键名#修饰符#小数位数_
    - str ：    字符串
    - bool ：      0或false输出false，其他输出true
    - date ：      输出日期格式
    - obj :        将数据拆分为多个子项来替代当前项，每一项以'|'分隔，键值对以':'分隔。例： _key1:value1|key2:value2_ 。作为主键修饰符则该条数据会丢失主键并以第一项作为主键
    - [] ：        以列表形式输出内容，列表项以'|'分隔。例： _value1|value2|value3_ 。
    - {} ：        以字典形式输出内容，字典项以'|'分隔，键值对以':'分隔。例： _key1:value1|key2:value2_ 。字典无法哈希，故无法作为主键，会报错

## 例子
详见Sample文件夹
- sample1主要测试各种数据类型以及一个文件输出多个表
- sample2主要测试多层嵌套和通过'!'注释数据
