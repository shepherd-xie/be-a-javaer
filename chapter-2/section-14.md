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


<table>
	<thead>
		<tr>
			<td>Person.java</td>
			<td>Student.java</td>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>
				class Person {
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
				}
			</td>
		</tr>
	<tbody>
</table>
| Person.java | Student.java |
| :---: | :---: |
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
} | class Student {
	private String name;
	private int age;
	private String school; 
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
	public void setSchool(String school) {
		this.school = school;
	}
	public String getSchool() {
		return school;
	}
} |

以上的程序里面出现了代码的重复，在自然的关系上，学生是人的一种类型，并且学生与人相比描述的更加细致，范围更小。

