# 递进讲解示例

以下示例用于组织解释。输出为纸面推演，未表示技能使用者实际执行过这些代码。

## 1. 从重复初始化引出fixture

用户已经会写普通pytest测试，也知道`Calculator.add`返回两个数的和。

```python
def test_add_positive():
    calc = Calculator()
    assert calc.add(2, 1) == 3

def test_add_negative():
    calc = Calculator()
    assert calc.add(-2, -1) == -3
```

先定位当前问题：两个测试都准备`Calculator`；准备过程复杂后会重复维护。引入fixture，保留原有调用和断言：

```python
import pytest

@pytest.fixture
def calc():
    return Calculator()

def test_add_positive(calc):
    assert calc.add(2, 1) == 3

def test_add_negative(calc):
    assert calc.add(-2, -1) == -3
```

解释一条用例的执行：pytest发现测试参数`calc` → 解析同名fixture → 调用fixture取得对象 → 将对象传给测试函数 → 测试函数自己调用`add`并断言。

这里仅改变准备过程，两个预期断言相同。默认function作用域下，这两条用例各自获得fixture创建的对象；不要暗示它们自动共享同一对象。

若后续新增“测试结束要关闭资源”的需求，在已理解的fixture上引入`yield`和清理。若新增复用需求，再对比具体生命周期并引入`scope`。当前没有这些需求时，不一次展开所有配置。

## 2. 从Python参数数据引出YAML

沿用用户已理解的辅助函数和参数化测试：

```python
import pytest

def output(first, second):
    return second

cases = [("ading", "value"), ("key", "other")]

@pytest.mark.parametrize(("first", "second"), cases)
def test_output(first, second):
    result = output(first, second)
    assert result == second
```

当前问题：希望独立维护数据。先将同样的两组值保存成YAML，暂不加入框架封装：

```yaml
- first: ading
  second: value
- first: key
  second: other
```

读取后先看Python里的形状。此处`yaml`来自PyYAML；仅讲解时说明依赖，不默认安装。

```python
import yaml

with open("cases.yaml", encoding="utf-8") as file:
    rows = yaml.safe_load(file)

print(rows)
```

预期输出：

```python
[{"first": "ading", "second": "value"},
 {"first": "key", "second": "other"}]
```

先说明：YAML顶层列表成为Python列表，每一项成为字典。理解形状后，组装成原有`cases`的数据形式：

```python
cases = []
for row in rows:
    cases.append((row["first"], row["second"]))
```

将它放到已有装饰器前，保留测试函数、辅助函数调用和断言。数据在模块导入、测试收集期间读取，装饰器接收参数组；没有读取代码时，pytest不会自动发现这个YAML文件。

跟踪第一组数据：`first="ading"`、`second="value"` → `output(first, second)`返回`"value"` → `result`接收返回值 → 断言比较`result`和本轮`second`。两个数据项生成两条用例。

指出职责：YAML提供数据，parametrize绑定测试参数，测试函数显式调用辅助函数和断言。若把调用写成`output(1, 2)`，pytest不会自动改用参数数据。

这个示例假设数据结构正确；它的目的在于解释数据流。迁入项目后，文件位置、字段校验、预期结果设计再按实际需求补充。上面的断言仅演示简单函数的返回约定，不代表完整业务测试设计。

## 3. 一行切片问题保持简短

用户问`words = words[::-1]`时，合适的回复可以是：

> 它从末尾向前取元素，得到一个新列表，再让`words`指向这个列表。例如`["I", "like", "Python"]`会变成`["Python", "like", "I"]`。切片格式是`[起点:终点:步长]`；这里省略起点和终点，步长`-1`表示从末尾每次往前取一个。反转的是单词的排列顺序，单词内部的字母没有反转。

用户继续问下标或新旧列表关系时，再补相应执行过程。当前问题不需要同时展示等价循环、字符串版本、迭代器或完整字符串算法。

## 技术资料也使用同一方法

例如解释Redis写事件：从已有的待发送字节和非阻塞socket状态开始，追踪一次`write`返回已接受的字节数。剩余字节继续留在用户态缓冲区；需要等待可写时注册写事件，事件循环收到就绪通知后再尝试写。说明“socket可写”与“业务SET命令”是不同层面的写，具体注册时机按所用版本实现核对。

这种解释保留缓冲区、返回值和事件循环等真实抽象。不要只用“排队发快递”等生活比喻，也不要因为主题是Redis就重讲整篇网络模型。
