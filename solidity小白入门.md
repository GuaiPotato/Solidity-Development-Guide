# solidity开发指南

本文档就一个目标：秒懂流程，快速上手！😼

官方文档：[中文](https://docs.soliditylang.org/zh/v0.8.20/)，[英文](https://docs.soliditylang.org/en/v0.8.23/)（中文的显示靠左侧，右边是空的😅）



## 合约编译部署调用流程

> 首先上来别管那么多，运行个HelloWorld！

1. 创建一个文件夹
2. 导入HelloWorld.sol
3. 编译
4. 选择用户进行部署
5. 调用合约方法

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/235629/33/7365/101091/65785d32Faabc0da6/ae7c226757ba6363.jpg)

接下来让我们详细讲讲

### 用户问题

默认**FISCO BCOS**是没有用户的，需要自己手动新建。如果你现在的已经有用户了，但是你不喜欢，你还是可以新建。

操作流程：**合约管理** -> **用户列表** -> **新增**。

![d5631a44a0c2a08c1a254ea8209ca6f2.png](https://img10.360buyimg.com/ddimg/jfs/t1/119786/20/35687/29100/65785ef9F60548064/43473a8bd0edb590.jpg)

然后起一个你喜欢的名字（只能使用**英文字母** /  **下划线** / **数字**）

![1792ea8cd817465970d0522afc3ab4e2.png](https://img12.360buyimg.com/ddimg/jfs/t1/231998/35/7945/8950/65785fc3F168ed731/af07d8a21355dcef.jpg)

点击确定，新用户就创建完毕啦！

![105f35cf4adec6799eba1ea3d7c2961c.png](https://img11.360buyimg.com/ddimg/jfs/t1/236186/20/7659/4027/65785ff2Fb75a567f/ff6079e28e664140.jpg)

### 编译

点击**编译**，生成合约的 [abi](#abi) 和 [bin](#bin)

### 部署

使用指定的用户调用合约构造方法，初始化状态和数据。生成合约地址。

> 真实业务中，需要用该账户支付费用，并给予该用户管理员权限。
>
> 所有部署都是通过[交易](#交易)实现的。

### 调用

方式很多：控制台命令调用（FISCO BCOS的控制台），浏览器图形化界面调用（你在webase上点击调用），http接口调用（后端或者使用postman等测试工具）。

如果多一个 `account` 的参数出来，是说明该方法需要你指定调用该函数的用户。

> 注意：这个参数与你在使用`get`这类方法时传递的什么 `userAddr` 不同。主要区别是在函数内获取的方式不一样，account是通过全局变量 `msg.sender` 或者 `tx.origin` 获得。而 `userAddr`，是通过函数参数列表中定义的参数进行接收的。在请求体中也可以发现，`account` 是通过 `user` 发送的，而 `userAddr`这类参数是在 `funcParam` 中的。

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/230071/40/7455/8746/657861b5Fc72149f7/3f8c18d5112d6f7d.jpg)

会出现该参数是因为函数签名中没有使用 `view` 或者 `pure` 修饰符，这说明该方法不会[修改状态](https://docs.soliditylang.org/zh/v0.8.20/contracts.html#view)，为了保证**安全性**，必须执行交易（调用函数）的用户。

> 我猜的🫣，其实也差不多，不指定这个account就没法做用户权限校验和记录发起交易的用户了，这一点官方文档也有说到。

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/111727/15/37946/9155/65786117Fc9d109f6/ccc4d338a1fc74b6.jpg)



## 开发流程

1. 了解业务流程（逻辑）
2. 操作业务中的数据流（crud）

第一步，了解业务流程，这个需要结合实际的业务。可以在之后的练习中逐渐熟悉。

第二步，CRUD。提到CRUD，那就不得不提数据的读写。在`FISCO BCOS`中，有两种存储数据的方式：

- 直接变量存储

  ![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/102903/28/44178/10678/65770a4aF2f49f330/aa935d618da9f44e.jpg)

  这些三个string变量都会永久存储在区块链中。

- `FISCO BCOS`提供的`Table CRUD`API（啥是API？就是你把文件一导入就能直接用它写好的函数）e.g.：![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/235253/31/7693/50815/65770b09F343cf2fb/7ab91b1171e9a405.jpg)

  这个时候你又要问啦：**Table是什么东西啊？** 🤔

  Table？简单理解就是**excel**，那玩意不也叫做Table（表）嘛，一样的一样的😘。

> **题外话**：实际业务中数据还是存储在数据库中的。这个是通过ORM实体映射实现的（别慌，就是数据库库表设计，啥存数据库都一样）。由此可见，区块链的主要应用还是在加密和安全同步（防止篡改）上。



## Table CRUD

如果是要像SQL那样讲Table的话，那就有的讲了。不过`FISCO BCOS`提供的这个table那么的复杂，只是利用那个模型进行数据存储。

具体用法见[官网文档](https://fisco-bcos-documentation.readthedocs.io/zh-cn/latest/docs/articles/3_features/33_storage/crud_guidance.html)

咱webase平台上也有一些参考用例，去找里面带`Table.sol`的文件夹。



### 概念

主键：唯一标识表中每一行记录的一列或一组列。**STOP！STOP！**这是在DB中的概念，在`FISCO BCOS`中，主键只能有一个，且不唯一。详情见[这里](#表设计)。

记录：行

字段：列名



## 交易

由于在区块链中所有的计算和存储都是需要消耗燃气`gas`值的（说白了就是计算资源），所有的方法调用，合约部署都是通过交易进行的。（花钱使用资源）



## 合约相关参数

### contractAddress

合约存储的地址

### abi

ABI 定义了合约方法的名称、参数类型、返回类型和事件的结构。

### bin



## solidity开发语言

这语言给我感觉是**SQL**+**面向对象**。主要原因是**FISCO BCOS**提供了这套**Table CRUD**，不能说就像，根本就是**SQL**。再加上语言本身是**面向对象**的。哇，这语言真不错。（毕竟是收入最高的语言😎）

### 面向对象

**面向对象**这个概念可以参考[韩顺平30天零基础速成JAVA](https://www.bilibili.com/video/BV1fh411y7R8?p=194)。这个概念很重要，在很多编程语言中都有体现。（不过随着web的发展，现在的语言逐渐像接口化发展了，e.g.：Golang）

### SQL

关于SQL参考我关于mysql的教程。

### 事件

> solidity中的事件类似日志。
>
> 一般在敏感操作（增删改）数据的时候触发事件。

1. 定义事件

   使用event关键字

   ```soli
   // 合约定义
   contract MyContract {
       event MyEvent(address indexed sender, uint256 value);
   
       // ...
   }
   ```

2. 触发事件

   ```solidity
   // 合约定义
   contract MyContract {
       event MyEvent(address indexed sender, uint256 value);
   
       function doSomething() public {
           // 执行操作
           emit MyEvent(msg.sender, 123);
       }
   }
   ```

3. 监听事件

   监听事件：在你的 DApp 或其他合约中，你可以添加事件监听器来捕获和处理触发的事件。在以太坊开发中，你可以使用 Web3.js 或其他以太坊客户端库来监听事件。

   使用Web3.js监听的实例代码：

   ```javascript
   // Web3.js 示例代码
   const myContract = new web3.eth.Contract(abi, contractAddress);
   
   myContract.events.MyEvent()
       .on('data', (event) => {
           console.log('收到事件:', event.returnValues);
           // 在这里处理事件
       })
       .on('error', (error) => {
           console.error('事件错误:', error);
       });
   ```




## 开发注意点

### 变量

#### Stack too deep

合约内变量不要定义太多，太多了会报错`stack too deep`。如果出现报错了，解决方法：

- 使用结构体`struct`

  ![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/232865/34/7417/8758/65770474F67376bda/296c1468c7fe6525.jpg)

- 尽可能跳过中间变量   e.g.


![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/235421/5/6217/13383/65702d15F7bd5c606/2a7a079376208398.jpg)



### 修饰符

#### memory

一些特定的类型不能使用`memory`修饰符，不然会爆`TypeError：Storage location can only be given for array or struct types.`

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/224241/30/7652/4175/657704c8F779a1fdd/c0217e1ccadd7a81.jpg)

不能使用`memory`修饰的类型：

- `int`, `int256`, `uint256`, ......
- `address`
- `bool`
- `Entry`（特别注意，使用table的时候不要误加）





### 函数



### Table CRUD

#### API调用

**数组**操作（新建，定位索引）使用`uint256`类型，而**Table**的相关操作中（`entry.get(int256)`， `entries.size()`的返回值）的整型是`int256`类型。



Table不能直接获取全部



#### 表设计

**Table**表是**多主键**的，这一点与sql是不一样的。

