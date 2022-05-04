[TOC]

# Android 事件分发机制

> 参考 [GcsSloop](http://www.gcssloop.com/customview/dispatch-touchevent-theory) 和[吴小龙同学](http://wuxiaolong.me/2015/12/19/MotionEvent/)

- 总的来说，事件的分发（`dispatchTouchEvent`）顺序是从 `Activity`/父 `View` 到子 `View`，而事件的处理（`onTouchEvent`）顺序则是从子 `View` 传回 `Activity`/父 `View`

- 拦截、处理方法返回的 `boolean` 表示事件是否被消费
	- 一旦拦截，事件便不再分发给它的子 `View`，事件处理直接从它开始
	- 一个 `View` 一旦处理了事件，便不再交给父 `View` 处理，如果 `Activity` 仍未处理事件，事件才会被抛弃
    
    >  `ViewGroup` 比 `View` 多了一个拦截（`onInterceptTouchEvent`）方法，先分发再拦截

# git学习笔记（感谢 [stormzhang](http://stormzhang.com/github/2016/06/19/learn-github-from-zero-summary/) 和[廖老师](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)）

## 远程库和Github
### SSH key

* [为不同账户设置不同 SSH key](https://www.jianshu.com/p/c30e1f787b92)

* [为不同项目设置不同用户名和邮箱](https://blog.csdn.net/QPC908694753/article/details/79237748)

  * 取消全局的用户名和邮箱

    ```bash
    git config --global --unset user.name
    git config --global --unset user.email
    ```

### 远程库

- 克隆远程仓库到本地：
`git clone git@server-name:path/repo-name.git`
- 如果本地仓库需要关联远程仓库：
	`git remote add origin git@server-name:path/repo-name.git`
	- 关联后，第一次推送时，加上`-u`是为了将本地和远程`master`分支关联起来，方便以后的推送和拉取：
	`git push -u origin master`
- 从本地推送到远程（`origin`）分支：
`git push origin <branch_name>`
- 从远程拉取到本地，一般先 pull 再 push：
`git pull origin <branch_name>`
- 查看所有远程仓库：
	`git remote`
	- 显示详细信息：
	`git remote -v`
- 将远程 dev 分支迁到本地 dev 分支：
`git checkout -b dev origin/dev`
### Github
- Watch：<kbd>Watch</kbd> 了某个项目，那么以后只要这个项目有任何更新，你都会第一时间收到关于这个项目的通知提醒
- Fork(衍生)：希望在别人的开源项目上做改进，可以 Fork 到自己的仓库
	- [Fork 一个仓库后，怎么同步原仓库的更新？](https://gaohaoyang.github.io/2015/04/12/Syncing-a-fork/)
- Pull request：希望自己的改进能被原作者加入他的仓库，可以发起 Pull request，等待原作者的接受
## 基本
- 初始化一个仓库：
`git init`
- 将a文件添加到暂存区（stage），可以一次add多个文件，文件间用空格隔开：
```shell
	git add a
	git add b c.md Util.java main.c
```
- 如果需要add被[`.gitignore`](#忽略特殊文件)忽略的文件，加上：`-f`
- 将之前add的所有文件提交到仓库，`-m<message>`表示提交的说明：
`git commit -m "I have a dream..."`
- 查看当前仓库状态：
`git status`
- 查看某个文件的修改情况：
	`git diff a.txt`
	- 查看工作区和当前版本（HEAD）某个文件的区别
	`git diff HEAD -- a.txt`
- 查看历史提交信息：
	`git log`
	- 简化一行显示，加上：
	`--pretty=oneline`
	- 缩写`commit_id`，加上：
	`--abbrev-commit`
	- 查看分支图，加上：
	`--graph`
	- 查看最后一次提交，加上：
	`-1`
- 查看之前操作的所有命令：
`git reflog`
## 版本穿梭和分支
### 版本穿梭
- `HEAD`表示当前分支版本，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上n个版本可以写成`HEAD~n`
	- 回退/前进到某个版本：
	`git reset --hard <commit_id>`
- 不添加到暂存区的修改是不会提交的
- 丢弃工作区的修改。如果文件已添加到暂存区后又进行修改，那么就回到添加到暂存区的状态；如果文件还未放到暂存区，就回到当前版本：
`git checkout -- a.txt`
- 丢弃暂存区的修改（unstage）：
`git reset HEAD a.txt`
- 删除文件（需要提交到版本库）：
`git rm a.txt`
- 储藏工作区未添加到暂存区/提交的文件，以便之后恢复：
`git stash`
- 查看储藏列表：
`git stash list`
- 恢复最近一次储藏的内容，但恢复后不会删除：
	`git stash apply`
	- 恢复某一次储藏内容：
	`git stash apply stash_id`
- 删除储藏内容（删除某一次同上）：
`git stash drop`
- 恢复并删除最近一次储藏内容：
`git stash pop`
### 分支
- 创建并切换到dev分支：
	`git checkout -b dev`

	等价于

	```
	git branch dev
	git checkout dev
	```
- 查看本地所有分支，当前分支前会标`*`号：
`git branch`
- 查看远程所有分支：
`git branch -r`
- 合并dev到当前分支（一般是fast forward合并）：
	`git merge dev`
	- 普通合并会生成一个提交，从分支图上可以看出合并信息：
	`git merge --no-ff -m "merge using no-ff" dev`
- 删除**合并过的** dev 分支：
`git branch -d dev`
- 删除**未合并过的**分支：
`git branch -D <name>`
## 标签
- [标签是一个有意义的名字，与某个版本对应，也方便查找](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013762144381812a168659b3dd4610b4229d81de5056cc000)
- 查看所有标签：
	`git tag`
	- 给**最新提交**打标签：
	`git tag <tag_name>`
	- 给**指定提交**打标签：
	`git tag <tag_name> <commit_id>`
	- 创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：
	`git tag -a <tag_name> -m "tag message" [commit_id]`
- 查看指定标签信息：
`git show <tag_name>`
- 删除本地标签：
`git tag -d <tag_name>`
- 推送某个标签到远程：
	`git push origin <tag_name>`
	- 推送所有未推送的标签到远程：
	`git push origin --tags`
- 删除远程标签：
	1. 删除本地标签
	2. 删除远程标签：`git push origin :refs/tags/<tag_name>`
## 忽略特殊文件
- [在工作区的根目录下创建一个`.gitignore`文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013758404317281e54b6f5375640abbb11e67be4cd49e0000)。
	忽略文件的原则是：
	1. 忽略操作系统自动生成的文件，比如缩略图等；
	2. 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的`.class`文件；
	3. 忽略带有敏感信息的配置文件，比如存放口令的配置文件。
- [GitHub已经为我们准备了各种配置文件，只需要组合一下就可以使用了](https://github.com/github/gitignore)
- 配置完成后将`.gitignore`加入版本库
- 检查某个文件是否被忽略：`git check-ignore -v <file>`

## 配置别名
- 用`st`表示`status`：`git config --global alias.st status`
	- `--global`表示全局参数，也就是这些命令应用于这台电脑的所有Git仓库，对应的配置文件为用户主目录下的`.gitconfig`；而每个仓库的配置文件都放在`.git/config`中
	- 用`last`表示`log -1`：`git config --global alias.last 'log -1'`
	- 为了更清楚地展现每次的提交和分支情况，可以：`alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"`

## rebase
* [让提交记录看起来更整齐](https://www.liaoxuefeng.com/wiki/896043488029600/1216289527823648)
  
  * pull 时用 rebase 整理提交记录，不至于出现 merge 的提交记录：`git config --global pull.rebase true`
  
* [合并多次提交记录](http://jartto.wang/2018/12/11/git-rebase/)
  * [如何在 IDEA 上合并多次提交记录？](https://www.zhihu.com/question/56624823)

## cherry-pick

- [将某个分支的一个或多个提交转移到另一个分支](https://www.ruanyifeng.com/blog/2020/04/git-cherry-pick.html)

# 数据库相关

## 一些概念

* DDL (**Data Definition Language**)：不需要提交的操作，不包括 `select`
* DML(**Data Manipulation Language**)：需要提交的操作，包括 `select`

## oracle

*  [`row_number() OVER (PARTITION BY col1 ORDER BY col2)` 用法：](https://www.cnblogs.com/fxgachiever/archive/2010/09/15/1826792.html)根据 col1 分组，在分组内部根据 col2 排序，返回每组内部排序后的行号（组内行号连续唯一)

- [`rank() OVER (PARTITION BY col1 ORDER BY col2)`](https://www.cnblogs.com/linJie1930906722/p/6036053.html) 与上一个的区别在于：相同 col2 数据的行号是相同的（_e.g._ 相同分数的排名并列）
-  
  - 查询所有定时任务：`user_jobs` 表
  - 查询某个表所涉及的存储过程：`select * from user_source s WHERE s.text LIKE '%TABLE_NAME%'`
- [更新、插入时卡死解决方法](https://blog.csdn.net/itmyhome1990/article/details/81745990)

# 加密算法

## 对称加密

用*同一个密钥*进行加密和解密。

[优点是速度较快，适合对数据量比较大的数据进行加密。缺点是密钥的保存方式需要保证，一旦加密或者解密的哪一方泄漏了密钥，都会导致信息的泄漏](http://zhangzr.cn/2018/07/04/%E5%B8%B8%E7%94%A8%E5%8A%A0%E5%AF%86%E7%AE%97%E6%B3%95%E7%AE%80%E4%BB%8B/)。

### DES

以 64 位为分组对数据进行加密，密钥长度为 56 位。

### 3DES

基于 DES 加密，对一块数据使用三个不同的密钥进行加密三次，强度更高。

### AES

新一代加密算法标准，取代 [DES](#DES)，速度快，安全级别高。密钥长度最少支持 128、192、256，分组长度 128 位。

## 非对称加密

使用公钥和私钥进行加解密。

### RSA

是目前最有影响力的公钥加密算法，并且被普遍认为是最优秀的公钥方案之一，能够抵抗到目前为止已知的所有密码攻击。

### ECC

相对于 RSA，ECC 的抗攻击性更强，并且计算量小，处理速度快，存储空间占用小，所以适用于移动设备使用。

## Hash 加密

不可逆，通过 Hash 算法对目标信息生成一段特定长度的唯一的 Hash 值，用于校验数据完整性。

### MD5

严格来说，MD5 不是一种**加密算法**，而是**摘要算法**。

### SHA1

比 MD5 安全性更高。

# Java 知识点

## switch 语法

从 jdk 1.5 开始，可以使用枚举类型；jdk 1.7 开始，可以使用 `String`；不能使用 `long`。

***

## 包装类型的缓存池

包装类型的 `valueOf` 方法会使用指定范围内缓存池的值。

`new Xxx()` 每次都会新建一个对象。

|    类型     |      缓存数值范围      |
  | :---------: | :--------------------: |
  |  `Boolean`  |   ```true、false```    |
  |   `Byte`    |          不限          |
  |   `Short`   |       -128～127        |
  |  `Integer`  |       -128～127        |
  | `Character` | ```\u0000 ~ \u0007F``` |

## ```+=、-=、*=、/=``` 

>  自动将计算结果向下转型

```java
short s1 = 1;
// 相当于 s1 = (short)(s1 + 1);
s1 += 1;
```

## [重写、重载](https://pdai.tech/md/java/basic/java-basic-lan-basic.html#%E9%87%8D%E5%86%99%E4%B8%8E%E9%87%8D%E8%BD%BD)

### 重载

方法名相同，参数列表不同；否则不是重载。

## `hashCode` 方法

```java
public native int hashCode();
```

来自 `Object` 类，返回散列值；等价的两个对象散列值一定相同，但是散列值相同的两个对象不一定等价。即，重写 `equals` 方法时应当总是重写 `hashCode` 方法。

## `clone` 方法

```java
protected native Object clone() throws CloneNotSupportedException;
```

> 来自 `Object` 类

* 浅拷贝
  拷贝对象和原始对象引用自同一个对象。
* 深拷贝
  拷贝对象和原始对象引用自不同对象。

## classpath

JVM 用到的一个环境变量，告诉 JVM 如何搜索 class 文件；classpath 是**一组**目录的集合，目录格式和操作系统有关；推荐在 JVM 启动时设置 classpath。

## 异常

全都继承自 `Throwable` **类**，进一步分为 `Error` 和 `Exception` 类。

`Error` 表示 JVM 无法处理的错误，如：

- `OutOfMemoryError`：内存耗尽
- `NoClassDefFoundError`：无法加载某个 Class
- `StackOverflowError`：栈溢出

`Exception` 为应该捕获并处理的异常，分为可检查（checked）异常和不检查（unchecked）异常。

可检查异常在源代码里必须显式地进行捕获处理，比如 `IOException`、`FileNotFoundException`。 这是编译期检查的一部分；

不检查异常就是所谓的运行时异常（`RuntimeException`），类似 `NullPointerException`、`ArrayIndexOutOfBoundsException` 之类，通常是可以编码避免的逻辑错误，具体根据需要来判断是否需要捕获，并不会在编译期强制要求。

* 异常的屏蔽

  在 `finally` 语句和  `catch` 语句都会抛出异常的情况下，异常信息只在 `finally` 语句中抛出，`catch` 语句中的异常称为被屏蔽的异常；要获取所有的异常信息，需要调用 `Throwable#addSuppressed` ，添加被屏蔽的异常，然后在 `finally` 中抛出。

### [断言关键字](https://www.liaoxuefeng.com/wiki/1252599548343744/1264740093521088)

```java
public static void main(String[] args) {
    double x = Math.abs(-123.45);
    assert x >= 0;
    System.out.println(x);
}
```

> 语句 `assert x >= 0;` 即为断言，断言条件 `x >= 0` 预期为 `true`。如果计算结果为 `false`，则断言失败，抛出 `AssertionError`。

使用 `assert` 语句时，还可以添加一个可选的断言消息：

```
assert x >= 0 : "x must >= 0";
```

这样，断言失败的时候，`AssertionError` 会带上消息 `x must >= 0`，更加便于调试。

Java 断言的特点是：断言失败时会抛出 `AssertionError`，导致程序结束退出。因此，断言不能用于可恢复的程序错误，只应该用于开发和测试阶段。

 JVM 默认关闭断言指令，即遇到 `assert` 语句就自动忽略了，不执行。

## 泛型

> [Java 采取了**“伪泛型”**的策略，语法上支持泛型，但在编译阶段会进行**“泛型擦除”**，即，将所有的泛型表示都替换为具体的类型（泛型的上限），就像没有泛型一样](https://pdai.tech/md/java/basic/java-basic-x-generic.html#%E4%B8%BA%E4%BB%80%E4%B9%88%E4%BC%9A%E5%BC%95%E5%85%A5%E6%B3%9B%E5%9E%8B)

* 泛型的上下限

  * `<? extends A>` 表示类型为（继承） A 的子类，可以理解为类型至多是 A，即，该泛型的上限为 A
  * `<? super A>` 表示类型为 A 的父类，可以理解为类型至少是 A，即，该泛型的下限为 A，上限为 `Object`

  > `?` 为无限制通配符

## 注解

### 作用

* 生成文档，通过代码里标识的元数据生成 javadoc 文档
* 编译检查，通过代码里标识的元数据让编译器在编译期间进行检查验证，例如 `@Override`、`@SuppressWarnings`
* 编译时动态处理，例如动态生成代码，修改 `.class` 文件
* 运行时动态处理，例如使用反射注入实例

通过 `@interface` 关键字来定义注解，注解的本质是接口（通过反编译注解的 `.class` 文件），所以注解中可以定义常量和抽象方法，不能使用 `extends`。

方法不能有参数，返回值**不包括**  `String`、`Class` 以外的对象类型及其数组类型。

抽象方法也叫注解的属性，在方法体后添加 `default` 指定属性的默认值；如果只有一个属性需要赋值，且属性名为 `value`，那么赋值时可以省略属性。

### 元注解

修饰注解的注解。

#### @Target

表示被修饰的自定义注解能够作用在代码中的位置，比如类、方法、属性上。

#### @Retention

表示被修饰的自定义注解能够保留到的阶段，比如编译期 `RetentionPolicy.SOURCE`、字节码阶段 `RetentionPolicy.CLASS`、运行期（JVM 能够读取）`RetentionPolicy.RUNTIME`，自定义注解通常都用 `RetentionPolicy.RUNTIME`。

#### @Inherited

表示被修饰的自定义注解可以被使用了该自定义注解的**类**的子类继承，对使用自定义注解修饰的接口的继承无效。

#### @Documented

表示在代码中被 `@Documented` 修饰的自定义注解会保留在 javadoc 文档中。

## 反射

> 通过 `Class` 实例获取类（class）信息的方法称为反射。类是由 JVM 在读取到一种类时动态加载（用到时才加载）进内存的，每加载一种类，JVM 就会创建一个这个类的 `Class` 类型实例。

要判断一个向上转型是否成立，可以调用：

```java
// Number n = ?
Number.class.isAssignableFrom(Integer.class); // true，因为 Integer 可以赋值给 Number
```

### 动态代理

> [对于接口而言；不编写实现类，在运行期动态创建接口对象；原理是 JVM 在运行期通过反射创建实现类（字节码）并加载](https://www.liaoxuefeng.com/wiki/1252599548343744/1264804593397984)

通过 `Proxy.newProxyInstance` 创建接口实现类，核心在于代理类中的 `InvocationHandler#invoke` 方法通过反射来代理方法。

## SPI 机制

> SPI（Service Provider Interface），是 JDK 内置的一种服务发现机制，为接口寻找服务实现。调用方提供接口，服务提供方实现接口，将接口与实现分离，达到解耦的目的。
>
> API 中的接口和实现都在一起

服务提供方需要在 classpath 下的 `META-INF/services/` 目录里创建一个以服务接口全路径命名的文件，这个文件里的内容就是这个接口的具体的实现类路径，调用方通过服务发现，加载实现类，就可以使用该服务了。JDK 中查找服务的实现的工具类是：`java.util.ServiceLoader`。

### 应用

#### JDBC DriverManager

JDBC 定义了数据库驱动的接口（标准），数据库厂商提供驱动的实现。

#### Commons Logging

Commons Logging 提供了一套接口，根据情况使用不同的日志实现，自动搜索并使用 Log4j，如果没有找到 Log4j，再使用 JDK 自带的 Logging。

#### SLF4J

SLF4J 类似于 Commons Logging，也是一个日志接口，而 Logback 类似于 Log4j，是一个日志的实现。

## 集合

分类两大类，`Collection<E>` 接口和 `Map<K, V>` 接口。

### List

`Collection` 的子接口；有序，遍历 `List` 尽量使用 `Iterator<E>` 接口来访问，因为不同的 `List` 实现类返回的 `Iterator<E>` 对象实现也不同，但总有最高的访问效率；用 `for each` 循环遍历 `Iterable<E>`  实现类时，会自动使用 `Iterator<E>` 遍历。

#### ArrayList

底层数组实现，添加元素时容量不足会自动扩容，查询效率高（通过下标访问），增删效率低。

> [数组进行扩容时，会将老数组中的元素重新拷贝一份到新的数组中，每次数组容量的增长大约是其原容量的 1.5 倍。这种操作的代价是很高的，因此在实际使用时，我们应该尽量避免数组容量的扩张。当我们可预知要保存的元素的多少时，要在构造 ArrayList 实例时，就指定其容量，以避免数组扩容的发生。或者根据实际需求，通过调用 ensureCapacity 方法来手动增加 ArrayList 实例的容量。](https://pdai.tech/md/java/collection/java-collection-ArrayList.html)

```java
// add 方法扩容代码，右移一位相当于减半
int newCapacity = oldCapacity + (oldCapacity >> 1);
```

#### LinkedList

既实现了 `List` 接口，又实现了 [`Deque`](#Deque) 接口，底层为双向链表，增删效率高，查询效率低（根据元素位置从头/尾遍历访问）。

```java
// get 方法遍历获取指定位置元素核心代码
Node<E> node(int index) {
  if (index < (size >> 1)) {
    // 元素在前半部分，正序遍历
    Node<E> x = first;
    for (int i = 0; i < index; i++)
      x = x.next;
    return x;
  } else {
    // 元素在后半部分，倒序遍历
    Node<E> x = last;
    for (int i = size - 1; i > index; i--)
      x = x.prev;
    return x;
  }
}
```

#### Vector

与 [ArrayList](#ArrayList) 类似，底层都是动态数组，区别在于 `Vector` 是线程安全的，因为每个方法都有 [synchronized 关键字](#synchronized 关键字)，对性能影响较大，所以不常用。

需要线程安全的 `List` 时，可以使用工具类 `java.util.Collections`：

```java
List list = ...;
list = Collections.synchronizedList(list);
```

### Map<K, V>

接口，重复 key 的 value 会被覆盖；遍历 Map 可以使用：

```java
Map<String, Integer> map = new HashMap<>();
for (Map.Entry<String, Integer> entry : map.entrySet()) {
  String key = entry.getKey();
  Integer value = entry.getValue();
  // ...
}
```

#### HashMap

**无序**；底层通过大数组存储所有 value，根据 key 的[哈希值](#hashCode 方法)计算出下标，空间换时间，数组容量不足时会自动扩容；不同 key 的哈希值相同时，通过链表进行存储，[在 Java8 中，当链表中的元素达到了 8 个时，会将链表转换为红黑树，在这些位置进行查找的时候可以降低时间复杂度为 O (logN)](https://pdai.tech/md/java/collection/java-map-HashMap&HashSet.html#java8-hashmap)。

Java7 及以前是通过动态数组 + 单向链表实现。

Java8 之后是通过动态数组 + 单向链表 / 红黑树实现。

#### TreeMap

底层红黑树实现。

有序，在内部会对 key 按 `Comparable#compareTo` 进行排序，`SortedMap` 接口的实现类，key 需要实现 `Comparable` 接口。

### Set

接口，存储不重复的元素集合，相当于只存储 key，不存储 value 的 `Map`。

#### HashSet

[`HashMap`](#HashMap) 的包装，无序。

#### TreeSet

[`TreeMap`](#TreeMap) 的包装，有序（按 `Comparable#compareTo` 进行排序），`SortedSet` 接口的实现类，元素必须实现 `Comparable` 接口。

### Queue

先进先出，队首出队，队尾入队。

| 队列操作           | throw Exception | 返回 false 或 null |
| :----------------- | :-------------- | ------------------ |
| 添加元素到队尾     | add(E e)        | boolean offer(E e) |
| 取队首元素并删除   | E remove()      | E poll()           |
| 取队首元素但不删除 | E element()     | E peek()           |

#### PriorityQueue

优先级最高的，先出队，优先级高低根据 `Comparable` 或 `Comparator<E>` 接口决定。

#### Deque

接口，对首、对尾都能出队、入队，双端队列（Double Ended Queue）。

|                    | queue                  | Deque                           |
| :----------------- | :--------------------- | ------------------------------- |
| 添加元素到队尾     | add(E e) / offer(E e)  | addLast(E e) / offerLast(E e)   |
| 取队首元素并删除   | E remove() / E poll()  | E removeFirst() / E pollFirst() |
| 取队首元素但不删除 | E element() / E peek() | E getFirst() / E peekFirst()    |
| 添加元素到队首     | 无                     | addFirst(E e) / offerFirst(E e) |
| 取队尾元素并删除   | 无                     | E removeLast() / E pollLast()   |
| 取队尾元素但不删除 | 无                     | E getLast() / E peekLast()      |

## JMM

Java 内存模型；并发要满足三个要素：可见性、原子性、有序性。

JMM 是软件层面抽象的模型，解决了多线程环境下的以上三类问题。

* 可见性：一个线程对共享变量的修改，另一个线程能立刻看到
* 原子性：一个或多个操作要么全部执行，过程不会被打断；要么全不执行
* 有序性：执行顺序按代码的先后顺序，但为了提高性能，编译器、处理器会对指令做重排序，JMM 有相应的规则针对这两种重排序

> Java 内存模型只保证了基本读取和赋值是原子性操作，如果要实现更大范围操作的原子性，可以通过 synchronized 和 Lock 来实现。由于 synchronized 和 Lock 能够保证任一时刻只有一个线程执行该代码块，从而保证了原子性。
>
> Java 提供了 volatile 关键字来保证可见性。当一个共享变量被 volatile 修饰时，它会保证修改的值会立即被更新到主存，当有其他线程需要读取时，它会去内存中读取新值。通过 synchronized 和 Lock 也能够保证可见性，synchronized 和 Lock 能保证同一时刻只有一个线程获取锁然后执行同步代码，并且在释放锁之前会将对变量的修改刷新到主存当中，因此可以保证可见性。
>
> Java 可以通过 volatile 关键字来保证一定的有序性。另外可以通过 synchronized 和 Lock 来保证有序性，很显然，synchronized 和 Lock 保证每个时刻是有一个线程执行同步代码，相当于是让线程顺序执行同步代码，自然就保证了有序性。

## JUC

Java 并发包。即 `java.util.concurrent` 包。

### synchronized 关键字

**同一个锁对象**的代码块在任意时刻最多只有一个线程能执行。

如果一个类被设计为允许多线程正确访问，我们就说这个类就是 “线程安全” 的（thread-safe）。

没有特殊说明时，一个类默认是非线程安全的。

JVM 允许同一个线程重复获取同一个锁，这种能被同一个线程反复获取的锁，就叫做**可重入锁**。

* ```java
  synchronized(lockObject) { // 获取锁
    
  } // 释放锁
  ```

* ```java
  synchronized void foo(){ // synchronized 修饰方法，相当与 this 为锁对象
    
  }
  ```

* ```java
  synchronized static void foo(){ // synchronized 修饰静态方法，用当前类的 Class 实例作为锁
    
  }
  ```

#### 死锁

同一个线程可以获取一个锁后，再获取另一个锁。

多个线程获取多个不同对象的锁可能导致死锁。

##### 发生死锁如何解决

只能重启应用。

##### 如何避免

不同方法中获取锁对象的顺序要一致。

#### 不需要 synchronized 的原子操作

- 基本类型（`long` 和 `double` 除外）赋值，例如：`int n = m`
- 引用类型赋值，例如：`List<String> list = anotherList`

###线程的状态

- New：新建，线程未执行

- Runnable：运行中

- Blocked：阻塞，等待其他线程释放锁时

- Waiting：等待，调用 `lockObj#wait`、`Thread.join` 无超时时间时

- Timed Waiting：计时等待，调用 `Thread#sleep` 或 `lockObj#wait`、`Thread#join` 带上超时时间时

- Terminated：终止，`Thread#run` 执行完毕

### 多线程协调运行

当条件不满足时，线程进入等待状态；当条件满足时，线程被唤醒，继续执行任务。

在 `synchronized` 块中执行 `lockObj.wait()` 后，线程进入[等待状态](#线程的状态)，方法不会返回，并释放锁；直到从等待状态被其他线程唤醒后，并重新试图获取锁，获取成功后才会返回继续执行。

在**相同的锁对象**上调用 `notify()` 或 `nofityAll()` 方法，能让等待的线程被重新唤醒；前者只会随机唤醒一个等待的线程，后者会唤醒所有在当前锁等待的线程。

### 如何终止线程

* `Thread#interrupt` 配合 `Thread#isInterrupted`

线程外通过 `Thread#interrupt` 发起中断请求，线程内的 `Thread#isInterrupted` 为 `true`。

发起中断请求后，线程内的等待操作（`Thread#sleep`、`Thread#join`、`lockObj#wait`）会抛出 `InterruptedException`。

* `volatile` 的共享变量

### ReentrantLock（可重入锁）

`java.util.concurrent` 下的 `Lock` 接口实现类。

效果相当于 [synchronized](#synchronized 关键字) 加锁，性能更好，增加了获取锁超时机制。

```java
private final Lock lock = new ReentrantLock();

public void foo(){
  if(lock.tryLock(1, TimeUnit.SECONDS)){
    try{
      ...
    }finally{
      lock.unlock();
    }
  }else{
    // 1 秒后未获取到锁的处理
    ...
  }
}
```



### Condition

接口。相当于实现 `synchronized` 下， `wait()` 和 `notifyAll()` 的功能。

`Condition` 对象必须从 `Lock` 实例的 `newCondition()` 返回，这样才能获得一个绑定了 `Lock` 实例的 `Condition` 实例。

```java
private final Lock lock = new ReentrantLock();

private final Condition condition = lock.newCondition();

public void foo(){
  lock.lock();
  try{
    ...
    // 相当于 wait 方法
    condition.await();
  }finally{
    lock.unlock();
  }
}

public void bar(){
  ...
  // 相当于 notifyAll 方法
  condition.signalAll();
}
```



### ReadWriteLock

接口。实现类是 `ReentrantReadWriteLock` 。

允许多个线程同时读（提高性能）；一个线程写；适合读多写少的场景。

* `ReadWriteLock#readLock()` 读锁

> 悲观读锁，一个线程读的过程中其他线程不能写入，必须等待

* `ReadWriteLock#writeLock()` 写锁

```java
private final ReadWriteLock rwlock = new ReentrantReadWriteLock(); 
private final Lock rlock = rwlock.readLock(); 
private final Lock wlock = rwlock.writeLock();

public void foo(){
  // 防止读的过程中其他线程写，但可以读
  rlock.lock();
  try{
    // 读操作
  }finally{
    rlock.unlock();
  }
}

public void bar(){
  // 防止写的过程中其他线程读
  wlock.lock();
  try{
    // 写操作
  }finally{
    wlock.unlock();
  }
}
```



### StampedLock

类。比 [`ReadWriteLock`](#ReadWriteLock) 提高了并发效率：读的过程中允许写入，因此提供了乐观读锁，虽然可能会导致读到的数据不一致，需要判断读的过程是否有写入。是不可重入锁。

### 线程池

`ExecutorService` 接口表示线程池，常用的实现类通过 `Executors.newXxx()` 实例化。

#### Future

代表一个未来能获取结果的对象

```java
Future<String> future = executorService.submit(task);
String result = future.get(); // 同步调用，直到任务完成才返回结果
```

### ThreadLocal

在线程中传递状态。

```java
class ...{
  // ThreadLocal 实例通常总是以静态字段初始化
  static ThreadLocal<User> threadLocalUser = new ThreadLocal<>();
  
  void init(User user) {
    try {
      threadLocalUser.set(user);
    	...
    } finally {
      // 当前线程执行完相关代码后，很可能会被重新放入线程池中，如果 ThreadLocal 没有被清除，该线程执行其他代码时，会把上一次的状态带进去
      threadLocalUser.remove();
    }
  	...
  }
  
  void a() {
    User user = threadLocalUser.get();
    ...
  }
}
```



## IO

> 以内存为中心。
>
> Input：从外部（文件、网络）**读**数据到内存中
>
> Output：从内存把数据**写**到外部

* 字节流：InputStream/OutputStream，以 `byte` 为最小单位
* 字符流：Reader/Writer，以 `char` 为最小单位

利用装饰器模式，每个流都包括两大块功能：核心功能（不同数据源的流）和附加功能（缓冲、压缩、解密流），两部分可以独立扩展，自由组合。

### 网络 IO

TCP/IP 称为 **TCP/IP 协议族**，核心的两个协议是 TCP 协议和 IP 协议。

IP 协议为互联网协议，处于计算机网络分层模型的网络层，用于分组交换，只负责发数据包，不保证传输的可靠性。

TCP 协议为传输控制协议，处于分层模型的应用层，传输数据包之前需要先建立连接，传输完要断开连接，允许双向通行，能保证传输的可靠性。

网络编程本质就是进程间的网络通信，服务端进程监听某个指定端口，等待客户端进程连接；客户端指定服务端的 IP 地址和端口连接服务端，连接成功后，双方就建立了一个 TCP 连接，之后可以互相发送、接收数据。

`Socket` 相当于对 TCP 协议连接、传输的封装，`Socket#getInputStream` 读取对方发送的数据（输入）、`Socket#getOutputStream` 发送数据给对方（输出）。

服务端通过新建 `ServerSocket` 实例指定端口，`ServerSocket#accept` 获取 `Socket` 。

客户端通过新建 `Socket` 实例指定 IP、端口。

### IO 模型

#### 同步 IO

会产生阻塞操作的 IO，比如等待数据、将数据从操作系统复制到应用进程。

##### 阻塞 IO

应用进程调用接收数据指令，过程中，因为等待数据阻塞，获取到数据后再将其从操作系统复制到应用进程后才继续执行。

##### 非阻塞 IO

应用进程调用接收数据指令，操作系统返回状态码，应用进程要通过轮询获取数据状态，直到操作系统获取到数据，再复制到应用进程。轮询会增加系统开销。

##### IO 复用

应用进程可以处理多个 IO 事件，**等待**多个事件中的任何一个获取到数据后，将其从操作系统复制到应用进程后返回。

##### 信号驱动 IO

应用进程调用信号指令后直接返回，获取到数据时操作系统会通知应用进程，再将数据从操作系统复制到应用进程。

#### 异步 IO

应用进程调用异步指令后直接返回，数据到应用进程之后操作系统才会通知应用进程。

# [Spring](Spring.md)

# Docker

## 一些概念

* image 文件

镜像文件。存储应用及其依赖。

* 容器文件

也叫 Docker 容器。根据 image 生成，也是文件；同一个 image，可以生成多个容器。

* Dockerfile

用来配置 image。Docker 根据该文件生成二进制的 image 文件。

# Redis

键值存储数据库，通过键，存储、查找值，值包括了一系列数据结构。

## 数据结构

### 字符串

```shell
# 所有 redis 的单条命令都是原子操作
# 存取值
set test "test"
get test => "test"
# key 对应的值是否存在
exists test => 1

# 整数是特殊的字符串
SET connections 10
INCR connections => 11
INCR connections => 12
DEL connections
INCR connections => 1
INCRBY connections 100 => 101
DECR connections => 100
DECRBY connections 10 => 90

# expire 设置过期时间（s），ttl（time to live）查看剩余存活时间（s）
# pexpire、pttl 同上，只不过时间单位变为毫秒
SET resource:lock "Redis Demo"
EXPIRE resource:lock 120
TTL resource:lock
SET resource:lock "Redis Demo 3" EX 5
```

### list

有序存储，双向链表结构

```shell
# 左右入队操作
RPUSH friends "Alice"
RPUSH friends "Bob"
LPUSH friends "Sam"
# push 操作支持入队多个元素
RPUSH test 1 2 3 => 3
# 0 表示第一个元素，-1 表示最后一个元素，-2 表示倒数第二个元素
LRANGE friends 0 -1 => 1) "Sam", 2) "Alice", 3) "Bob"
LRANGE friends 0 1 => 1) "Sam", 2) "Alice"
LRANGE friends 1 2 => 1) "Alice", 2) "Bob"
# pop 操作会返回弹出的元素
LPOP friends => "Sam"
RPOP friends => "Bob"
# 获取 list 长度
LLEN friends => 1
```

### set

无序，元素不重复

```shell
# 添加
SADD superpowers "flight"
SADD superpowers "x-ray vision" "reflexes"
# 支持一次添加多个元素
SADD letters a b c d e f => 6
# 移除，移除不存在的元素返回 0
SREM superpowers "reflexes" => 1
SREM superpowers "making pizza" => 0
# 元素是否在 set 中
SISMEMBER superpowers "flight" => 1
SISMEMBER superpowers "reflexes" => 0
# 获取 set 所有元素
SMEMBERS superpowers => 1) "flight", 2) "x-ray vision"
# 组合多个 set
SADD birdpowers "pecking"
SADD birdpowers "flight"
SUNION superpowers birdpowers => 1) "pecking", 2) "x-ray vision", 3) "flight"
# 添加元素失败返回 0，否则返回 1
SADD superpowers "flight" => 0
SADD superpowers "invisibility" => 1
# 随机弹出两个元素，参数表示想弹出的元素个数
SPOP letters 2 => 1) "c" 2) "a"
```

### sorted set

有序，为每个元素增加了 score 权重，用于排序

```shell
# score 在前，元素在后
ZADD hackers 1940 "Alan Kay"
ZADD hackers 1906 "Grace Hopper"
ZADD hackers 1953 "Richard Stallman"
ZADD hackers 1965 "Yukihiro Matsumoto"
ZADD hackers 1916 "Claude Shannon"
ZADD hackers 1969 "Linus Torvalds"
ZADD hackers 1957 "Sophie Wilson"
ZADD hackers 1912 "Alan Turing"
# 一次性添加多个元素
zadd leaders 1 mao 2 xi
# 下标同 lrange
ZRANGE hackers 2 4 => 1) "Claude Shannon", 2) "Alan Kay", 3) "Richard Stallman"
```

### hashes

类似 [HashMap](#HashMap)，是字符串键与字符串值的映射

```shell
# 键在前，值在后
HSET user:1000 name "John Smith"
HSET user:1000 email "john.smith@example.com"
HSET user:1000 password "s3cret"
# 获取所有键、值
HGETALL user:1000 => 1) "name"
									   2) "John Smith"
                     3) "email"
                     4) "john.smith@example.com"
                     5) "password"
                     6) "s3cret"
# 对某个键进行增加、减少、删除
HSET user:1000 visits 10
HINCRBY user:1000 visits 1 => 11
HINCRBY user:1000 visits 10 => 21
HDEL user:1000 visits
HINCRBY user:1000 visits 1 => 1
# 一次性添加多个键值对
HMSET user:1001 name "Mary Jones" password "hidden" email "mjones@example.com"
# 获取某个键的值
HGET user:1001 name => "Mary Jones"
```

## 分布式锁实现

并发请求统一到 Redis 获取锁，通过 `setnx` 命令设置成功与否来实现锁的抢占并设置过期时间保证锁能释放，命令如下：

```shell
# key 不存在（not exist），设置 key 的值，否则什么都不做
# 保证设值和设置过期时间的原子性
set <key> <value> ex <seconds> nx
# 释放锁
del <key>
```

抢占锁的线程执行完业务后，通过删除 key 来释放锁。

线程的业务执行时间超过过期时间，锁自动释放，导致锁被其他线程获取，从而失去原子性，因此需要设置唯一编号，在执行完业务后，删除自己设置的锁。

## 分布式 ID

通过 `incr` 和 `increby` 这样自增的命令，保证原子性

# 前端知识点

* jQuery
    - `class` 属性用 `.`
    - `id` 属性用 `#` 来引用
    
* JavaScript

    * 真「_一切皆对象_」，`number`、`string`、`boolean` 都是对象的类型

    * 变量名是大小写英文、数字、`$` 和 `_` 的组合，且不能用数字开头，也不能是 JavaScript 的关键字

    * 函数
        * 函数名命名同变量名
        * 函数也是一个对象，而**函数名可以视为指向该函数的变量**
        * 函数内可以再定义函数。函数内定义的函数，`this` 指向 `undefined` 了（在非 strict 模式下，它重新指向全局对象 `window`！）
        * 如果没有 `return` 语句，函数执行完毕后也会返回结果，只是结果为 `undefined`
        * 关键字 `arguments`
            * 只在函数内部起作用，并且永远指向当前函数的调用者传入的所有参数。`arguments` 类似 `Array` 但它不是一个 `Array`
        * 高阶函数：将**函数作为参数**接收的函数
        * 闭包：函数中，将__函数作为返回值__的结构。内部函数可以引用外部函数的参数和局部变量，当外部函数返回函数时，相关参数和变量都保存在返回的函数中
        * `Array`的 `sort()` 方法会直接对 `Array` 进行修改，它返回的结果仍是当前 `Array`
        
    * JavaScript 把 `null`、 `undefined`、`0`、`NaN` 和空字符串 `''` 视为 `false`，其他值一概视为 `true`

    * [*ECMAScript* 是几个公司联合 _ECMA_（**European Computer Manufacturers Association**）组织定制了JavaScript 语言的标准。最新版 _ECMAScript 6_ 标准（简称*ES6*）已经在 2015 年 6 月正式发布了，所以，讲到 JavaScript 的版本，实际上就是说它实现了 *ECMAScript* 标准的哪个版本。](https://www.liaoxuefeng.com/wiki/1022910821149312/1023020745357888)

    * ```javascript
        var s = "hello";
        // 模版字符串
        s = `${s} world`;
        // ['hello']
        s.split(',');
        // ['h', 'e', 'l', 'l', 'o']
        s.spilt('');
        var arr = [1, 2, 3, 4];
        // [3, 4, 5, 6]
        arr = arr.map(a => a+2);
        // 另一种写法，[5, 6, 7, 8]
        arr = arr.map(function(x){
          return x + 2;
        });
        // filter 过滤出满足条件的元素
        // [6, 8]
        arr.filter(a => a%2 === 0);
        // 遍历数组
        arr.forEach((ele, idx, arr) => {
        });
        // 数组拼接数组
        // [6, 8, 6, 8]
        arr.concat(arr);
        ```

    * [**展开语法**](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Spread_syntax)

        ```javascript
        function sum(i, j){
          return i+j;
        }
        var arr = [6, 8];
        // 14
        sum(...arr)
        ```

        

* CSS（**C**ascading **S**tyle **S**heet）

    ![](https://mdn.mozillademos.org/files/9443/box-model.png)

* 

# maven 通过命令手动导入 jar 包

`mvn install:install-file -Dfile=<path-to-file> -DgroupId=<group-id> -DartifactId=<artifact-id> -Dversion=<version> -Dpackaging=jar`

# mac 下获取文件的 md5 与 sha 值

> 终端下：

* `md5 filepath ` 就会输出对应的 MD5 值

* `shasum filepath`  就会输出对应的 sha1 值，并且 `shasum` 命令**默认检测 SHA1 值**，而通过参数

   `-a 224/256/384/512` 可以修改为 SHA224、SHA256、SHA384 或 SHA512

# Chrome 开发者工具快捷键

* 快速跳转到某一行：<kbd>control </kbd> + <kbd>G</kbd>

# terminal 一键启动、关闭 redis

* 前台启动 redis：`redis-server `

* 后台启动 redis：`brew services start redis` 

  - 相关命令：

    > `[sudo] brew services list`
    >
    > List all running services for the current user (or root).
    >
    > 
    >
    > `[sudo] brew services run (formula|—all)`
    >
    > Run the service formula without registering to launch at login (or boot).
    >
    > 
    >
    > `[sudo] brew services start (formula|—all)`
    >
    > Start the service formula immediately and register it to launch at login (or
    >
    > boot).
    >
    > 
    >
    > `[sudo] brew services stop (formula|—all)`
    >
    > Stop the service formula immediately and unregister it from launching at login(or boot).
    >
    > 
    >
    > `[sudo] brew services restart (formula|—all)`
    >
    > Stop (if necessary) and start the service formula immediately and register it to launch at login (or boot).
    >
    > 
    >
    > `[sudo] brew services cleanup`
    >
    > Remove all unused services.

  - 查看端口占用、杀死进程

    > - [sudo] lsof -i -P | grep -i (端口号)
    >
    >   > lsof = list open file
    >
    > - `lsof -i:(端口号)`
    >
    > - 杀死进程：`kill pid`

* 关闭 redis：`redis-cli shutdown`
