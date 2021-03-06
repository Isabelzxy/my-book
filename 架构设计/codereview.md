## Code Review 规范

### Code Review Checklist

Code Review主要检查代码中是否存在以下方面问题：

**代码的一致性、编码风格、代码的安全问题、代码冗余、是否正确设计以满足需求（功能、性能）**等等

- 完整性检查
    * 代码是否完全实现了设计文档中提出的功能需求
    * 代码是否已按照设计文档进行了集成和Debug
    * 代码是否已创建了需要的数据库，包括正确的初始化数据
    * 代码中是否存在任何没有定义或没有引用到的变量、常数或数据类型

- 一致性检查
    * 代码的逻辑是否符合设计文档
    * 代码中使用的格式、符号、结构等风格是否保持一致

- 正确性检查
    * 代码是否符合制定的标准
    * 所有的变量都被正确定义和使用
    * 所有的注释都是准确的
    * 所有的程序调用都使用了正确的参数个数

- 可修改性检查
    * 代码涉及到的常量是否易于修改(如使用配置、定义为类常量、使用专门的常量类等)
    * 代码中是否包含了交叉说明或数据字典，以描述程序是如何对变量和常量进行访问的
    * 代码是否只有一个出口和一个入口（严重的异常处理除外）

- 可预测性检查
    * 代码所用的开发语言是否具有定义良好的语法和语义
    * 是否代码避免了依赖于开发语言缺省提供的功能
    * 代码是否无意中陷入了死循环
    * 代码是否是否避免了无穷递归

- 健壮性检查
    * 异常处理和清理（释放）资源
    * 代码是否采取措施避免运行时错误（如数组边界溢出、被零除、值越界、堆栈溢出等）

- 结构性检查
    * 程序的每个功能是否都作为一个可辩识的代码块存在
    * 循环是否只有一个入口

- 可追溯性检查
    * 代码是否对每个程序进行了唯一标识
    * 是否有一个交叉引用的框架可以用来在代码和开发文档之间相互对应
    * 代码是否包括一个修订历史记录，记录中对代码的修改和原因都有记录
    * 是否所有的安全功能都有标识

- 可理解性检查
    * 注释是否足够清晰的描述每个子程序
    * 是否使用到不明确或不必要的复杂代码，它们是否被清楚的注释
    * 使用一些统一的格式化技巧（如缩进、空白等）用来增强代码的清晰度
    * 是否在定义命名规则时采用了便于记忆，反映类型等方法
    * 每个变量都定义了合法的取值范围
    * 代码中的算法是否符合开发文档中描述的数学模型

- 可验证性检查
    * 代码中的实现技术是否便于测试

- 可重用性
    * DRY（Do not Repeat Yourself）原则：同一代码不应该重复两次以上
    * 考虑可重用的服务，功能和组件
    * 考虑通用函数和类

- 可扩展性
    * 轻松添加功能，对现有代码进行最小的更改。一个组件可以被更好的组件替换

- 安全性
    * 进行身份验证，授权，输入数据验证，避免诸如SQL注入和跨站脚本（XSS）等安全威胁 ，加密敏感数据（密码，信用卡信息等）

- 性能
    * 使用合适的数据类型，例如StringBuilder，通用集合类
    * 懒加载，异步和并行处理
    * 缓存和会话/应用程序数据

### 常见Code Review过程中发现的问题

主要体现在两个方面，一个是编码习惯问题，另一个是编码质量的问题。编码习惯主要有日志编写、代码注释以及编码风格的问题，而编码质量则与很多方面相关，比如轮子的使用、数据交互、逻辑精简程度等等。下面展开来说

#### 编码习惯问题：

- 方法体偏长，不易管理维护，可逐步抽取成小方法来减少代码长度。
- 缺少注释或注释与实现不符，这对后期维护人员是个伤害。
- 硬编码，随手写的代码或测试时的死数据或常见的公共常量未维护，一旦发生变更，维护的代码量较大
- 日志缺失或缺少或输出意义不大，一旦发生问题，线上排查难度较大
- 编码风格比较个性，读起来晦涩难懂，对融入团队是个障碍。

#### 编码质量问题：

- 重复造轮子的问题，常见工具类使用不到位，经常自己写方法实现。比如Apache commons，Google Guava等。另一个是共用的业务代码，未能提交协商好，造成多个版本实现，后期维护成本上升。
- 公共数据使用不充分，存在重复调用的情况，而不是一次调用，多次使用，这种情况在与第三方交互的场景中对效率损伤更大。
- 参数过多时，可转化为对象传参，否则一个方法的参数要加大代码的可维护性。
- 采用MBG产生的单表的关联查询，但在业务中适合多表关联查的情况下，可多表联查，提高效率。【涉及NDB Cluster存储引擎，跨库Join问题】
- 代码命名，未能见名知意，这也是一个老生常谈的问题，起个优雅的名字是多么的重要。
- 代码逻辑不顺畅，存在走弯路的倾向，能精简的代码要反复的重构以达到最优目标。
- 多余代码，并无实际意义。如有些情况下，先查询，再更新，典型的hibernate的思路，完全可以采用以主键选择更新的方法。
- 需要异步处理的情况就不要同步处理，以免影响主业务流程效率。比如流程过程中产生的短信、推送通知等，以通知为主要目的的除外。
- 代码重复，针对功能类似的方法，可添加一个参数加以区分复用。
- 校验逻辑要提前，防止做无用功。
- 前后逻辑重复，Controller中作必输校验，Service无须再次校验。
- 虽标记了FIXME/TODO，却未实际修复，重构不能是一句空话。

#### 何时实施代码重构：

既然发现了问题，我们又该如何把握好节奏来重构我们的代码呢？下面推荐几比较好的重构时机：添加功能时重构、修补错误时重构 、复审代码时重构、时间空余时重构 。

回头审视过去的代码，就像审视我们的过去的编码思路、技巧，要想有所提升成长，就需要反复来重构，以达到一个最优结果。如果只是写过，事后不做复盘重构，对个人成长没有促进作用。



