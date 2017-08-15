## 继承性

---

1、清楚继承性的主要作用以及实现

2、继承性的相关限制以及使用规则。

继承性的最大特征是解决代码的重用问题。

### 继承问题的引出

下面将通过一段简短的程序来分析一下，为什么需要继承。

**范例：**要求定义两个描述人与学生的类

```java

```

|  |  |
|:---:|:---:|
| class Person {
	private String name;
	private int age;
	public void setName(String name) {
		this.name = name;
	}
	public String getName() {
		return name;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public int getAge() {
		return age;
	}
} |  |

