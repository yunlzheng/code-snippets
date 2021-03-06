# 敏捷方法

> 以用户的需求进化为核心，采用迭代、循序渐进的方法进行软件开发。常用的方式如Scrum以及看板。

## 管理方法

### Scrum

> 迭代式增量软件开发过程。

角色：

* Product Owner
* Development Team
* Scrum Master

产出：

* Product Backlog
* Sprint Backlog
* Work Dliverable Software

活动：

* Sprint Planing Meeting
* Sprint
* Daily Stand up Meeting
* Sprint Review
* Sprint Retrospective

价值观：

* Courage
* Commitment
* Focus
* Respect
* Openness

### Kanban

> 看板方法脱胎于丰田生产方式和约束理论，是精益方法的进一步延伸。它将软件开发过程视为一种价值流，并且相信拉动式的管理能产生更好的结果。它通过限制在制品的数量等一系列简单可行的技巧，发现和缓解软件开发过程中的压力和瓶颈，提高生产效率。

* 工作流程形象化
* 限制“在制品”（work in progress，简称 WIP） – 明确设定限制在每个状态下同一时间能有多少工作任务。看板的本质是一个很朴素的思想：在制品（work-in-progress，WIP）必须被限制。只有当前的某项工作被交付，或是有了来自于下游的拉动，新的工作才能开始。
* 度量生产周期（即完成一个任务的平均时间），优化开发过程，缩短开发周期和使它更易于预测。

#### Scrum VS Kanban

|Scrum	|看板开发方式|
|---|----|
|要求定时迭代|	没指定定时限迭代，可以分开计划、发布、过程改进，可以事件驱动而不是限定时限|
|团队在每个迭代承诺一定数目的工作|	承诺不是必须的|
|以速度（Velocity）作为计划和过程改进的度量数据|	使用开发周期作为计划和过程改进的度量数据|
|指定跨功能团队|	没有指定跨功能团队，也容许专门团队|
|工作任务细分，可于一个迭代中完成|	没有指定工作任务大小|
|指定使用燃烧图	|没有指定任何图表|
|间接限制开发中工作（每个迭代）|	设定开发中工作的限制（每个工作流程状态）|
|规定估算过程|没有指定任何估算方式|
|在迭代中不能加入新工作任务	|只要生产力容许，可以随时加工作任务|
|由单一团队负责 Sprint Backlog|多个团队和团员分享看板|
|指定三个角色（产品负责人／ScrumMaster/团队）|没有指定任何团队角色|
|Scrum board 在每个迭代后重设|看板反映持久开发情况|
|规定优先化的 product backlog|优先级是非必须的|

## 技术实践

### 极限编程

* 规划策略(The Planning Game)；
* 结对编程(Pair programming)
* 测试(Testing)
* 重构(Refactoring)
* 简单设计(Simple Design)
* 代码集体所有权(Collective Code Ownership)
* 持续集成(Continuous Integration)
* 现场客户(On-site Customer)
* 小型发布（Small Release）
* 每周40小时工作制（40-hour Week）
* 编码规范（Code Standards）
* 系统隐喻（System Metaphor）

## 需求管理

### 用户故事（User Story）

> 用户故事（user story）是从用户的角度来描述用户渴望得到的功能

1. 角色：谁要使用这个功能。
2. 活动：需要完成什么样的功能或目标。
3. 商业价值：为什么需要这个功能，这个功能带来什么样的价值。

模板：

```
As a <Role>, I want to <Activity>, so that <Business Value>.
作为一个<角色>, 我想要<活动>, 以便于<商业价值>
```

3要素：

* 卡片（Card）: 用户故事一般写在小的记事卡片上。卡片上可能会写上故事的简短描述，工作量估算等。
* 交谈（Conversation）： 用户故事背后的细节来源于和客户或者产品负责人的交流沟通。
* 确认（Confirmation）： 通过验收测试确认用户故事被正确完成。

六个特性- INVEST（Independent, Negotiable, Valuable, Estimable, Small, Testable）

* 独立性（Independent）
* 可协商性（Negotiable）
* 有价值（Valuable）
* 可以估算性（Estimable）
* 短小（Small）
* 可测试性（Testable）

### 用户故事地图（User Story Map）

> 理解整个系统的意图: 用户故事地图将用户故事排列成一个有用的模型，以帮助理解系统的功能，识别漏洞和遗漏，并有效规划整个版本，为每个版本提供价值给用户和业务。

1. 创意框架（IDEA）
2. 用户画像（User Persona）
3. 用户活动（User Activity）
4. 探索细节(Details)

6步创建用户故事地图：

1. 理清问题：用户是谁，价值是什么
2. 构建全景：广度优先
3. 探索细节：深度弹错
4. 制定发布策略：聚焦业务目标，砍掉多余的功能，达成目标MVP
5. 指定学习策略：为用户切分更小的MVP实验，逐步了解真正的用户价值
6. 指定开发策略：根据实验的先后顺序，将最小可行方案进步一切分，投入开发

## 图书

* 《用户故事与敏捷方法》
* 《用户故事地图》
* 《敏捷开发的艺术》
* 《硝烟中的Scrum和XP-我们如何实施Scrum》
* 《精益开发实战：用看板管理大型项目》