---
title: 威胁分析与风险评估(TARA)
---
**1、资产识别（Asset identification）：**

了解什么会受到危害

按照标准的陈述：识别项目或组件的破坏破坏场景和资产…

**2、威胁场景识别（Threat scenario identification）：**

了解资产会如何受到危害

按照标准的陈述：根据所分析资产的网络安全属性识别威胁场景

**3、影响打分（Impact rating）：**

威胁将导致多大的危害

按照标准的陈述：根据损失场景 (3.1.18) 评估损失或人身伤害的大小。

**4、攻击路径分析（Attack path analysis）：**

什么行为导致了威胁

按照标准的陈述：识别并将潜在攻击路径与一个或多个威胁场景关联

**5、攻击可能性打分（Attack feasibility rating）：**

危害发生的可能性有多大

按照标准的陈述：基于易受攻击性对攻击路径进行可行性评级

![img](https://pic4.zhimg.com/80/v2-57bbcdcc32903e64ebf417bf7d2c7ffb_720w.webp)

**6、风险值识别（Risk value determination）：**

威胁导致的风险有多大

按照标准的陈述：确定威胁场景的风险值

**7、风险处理决策（Risk treatment decision）：**

如何应对风险

按照标准的陈述：通过选择适当的风险处理选项应对所识别的风险

TARA分析帮助开发人员系统性地找出目标存在的安全问题，然后妥善地处置这些问题，重点在于系统性，而不是纯靠开发人员的灵光一闪。ISO 21434定义的TARA规范示意图如下。“风险值 = 攻击可行性 × 损害程度”作为中心思想，以此判定CAL等级，确认处置措施。攻击树分析是核心的环节，表述了抽象的威胁场景具体有哪些实现路径。下面简要分析如何做攻击树模型。

![img](https://pic4.zhimg.com/80/v2-1209f63689834bedc75a7e3180b868f3_720w.webp)

图1 TARA方法论

攻击树模型的一个总要前置条件是确认安全目标。这里以整车电子电气架构作为一个示例去考虑系统边界，暴露在最外侧的是各种物理接口和传感器，再往内就是各种通讯总线，处于端点的是控制器。从信息交互实体和传播路径考虑，边界内的主要的安全资产可以分类为：

- 各类物理接口
- ECU固件
- 总线通信
- 用户数据
- 密码学相关数据
- 服务或功能

![img](https://pic1.zhimg.com/80/v2-587986d57cfac1dc635b3895fd1db824_720w.webp)

图2 整车系统边界

安全属性模型，有经典的CIA，还有STRIDE威胁模型（对应6个属性），对于车载网络环境，更适合的是EVITA提出的模型（8种），包含以下安全属性：

- 真实性（身份认证）
- 完整性
- 可用性
- 授权
- 抗否认性
- 新鲜度
- 匿名
- 机密性

可以将资产和安全属性做一个映射，如下表所示：

![img](https://pic4.zhimg.com/80/v2-b8c0045d3f113476bf4df4945e88b32f_720w.webp)

安全目标可以看作是保护资产的安全属性，那么威胁则是资产的安全属性可能遭到破坏的场景，损害场景则是相应威胁可能导致的破坏或造成的损失。

- 安全目标 = 保护资产X的安全属性Y
- 威胁场景 = 何种方式破坏资产X的安全属性Y
- 损害场景 = 破坏资产X的安全属性Y后可能导致的损害

以ECU固件的完整性为例，安全目标是保护ECU固件的完整性。完整性遭到破坏，即固件遭到篡改，为ECU固件的威胁场景。而具体遭到篡改的数据位置不同，造成的后果也不同，可能有以下几种损害场景：

- 篡改了代码段，修改了ECU功能，车辆功能发生变化
- 篡改了代码段，植入后门
- 篡改了代码段，屏蔽了认证过程，提升权限
- 篡改了标定数据，车辆性能发生了变化
- 篡改了车辆配置，激活了未付费购买的功能

具体的攻击树实现路径分析，严重依赖于网络安全开发人员的经验。我们也可以参考一些开源的攻击路径数据库，如CAPEC。CAPEC数据库从多个视角对攻击路径进行了分类，常用的有按攻击机制分类和按域分类。这些分类也可以作为参考，进行枚举攻击路径。

**按照攻击域来分类：**

- 3000 - Domains of Attack
- Software - (513)
- [http://capec.mitre.org/data/definitions/513.html](https://link.zhihu.com/?target=http%3A//capec.mitre.org/data/definitions/513.html)
- Hardware - (515)
- [http://capec.mitre.org/data/definitions/515.html](https://link.zhihu.com/?target=http%3A//capec.mitre.org/data/definitions/515.html)
- Communications - (512)
- [http://capec.mitre.org/data/definitions/512.html](https://link.zhihu.com/?target=http%3A//capec.mitre.org/data/definitions/512.html)
- Supply Chain - (437)
- [http://capec.mitre.org/data/definitions/437.html](https://link.zhihu.com/?target=http%3A//capec.mitre.org/data/definitions/437.html)
- Social Engineering - (403)
- [http://capec.mitre.org/data/definitions/403.html](https://link.zhihu.com/?target=http%3A//capec.mitre.org/data/definitions/403.html)
- Physical Security - (514)
- [http://capec.mitre.org/data/definitions/514.html](https://link.zhihu.com/?target=http%3A//capec.mitre.org/data/definitions/514.html)

**按照攻击机制来分类：**

- 1000 - Mechanisms of Attack
- Engage in Deceptive Interactions - (156)
- [http://capec.mitre.org/data/definitions/156.html](https://link.zhihu.com/?target=http%3A//capec.mitre.org/data/definitions/156.html)
- Abuse Existing Functionality - (210)
- [http://capec.mitre.org/data/definitions/210.html](https://link.zhihu.com/?target=http%3A//capec.mitre.org/data/definitions/210.html)
- Manipulate Data Structures - (255)
- [http://capec.mitre.org/data/definitions/255.html](https://link.zhihu.com/?target=http%3A//capec.mitre.org/data/definitions/255.html)
- Manipulate System Resources - (262)
- [http://capec.mitre.org/data/definitions/262.html](https://link.zhihu.com/?target=http%3A//capec.mitre.org/data/definitions/262.html)
- Inject Unexpected Items - (152)
- [http://capec.mitre.org/data/definitions/152.html](https://link.zhihu.com/?target=http%3A//capec.mitre.org/data/definitions/152.html)
- Employ Probabilistic Techniques - (223)
- [http://capec.mitre.org/data/definitions/223.html](https://link.zhihu.com/?target=http%3A//capec.mitre.org/data/definitions/223.html)
- Manipulate Timing and State - (172)
- [http://capec.mitre.org/data/definitions/172.html](https://link.zhihu.com/?target=http%3A//capec.mitre.org/data/definitions/172.html)
- Collect and Analyze Information - (118)
- [http://capec.mitre.org/data/definitions/118.html](https://link.zhihu.com/?target=http%3A//capec.mitre.org/data/definitions/118.html)
- Subvert Access Control - (225)
- [http://capec.mitre.org/data/definitions/225.html](https://link.zhihu.com/?target=http%3A//capec.mitre.org/data/definitions/225.html)

R155法规也提供了一个威胁的攻击方法清单（附录5表格A1），可用于自查枚举结果是否完备。

![img](https://pic1.zhimg.com/80/v2-9900d78f77932e5bf2a97a1912c155e0_720w.webp)

由于攻击树和对应的措施严重依赖专家经验，建议建立一个汽车电子电气架构的通用技术攻击路径数据库，数据库有多个视图，可以从资产类别、安全属性、攻击机制等方式进行分类。再配合可视化工具从系统的拓扑图上分析，从根节点出发，沿着数据流画出攻击路径上需要经过的节点，以及每个节点或通讯管道的哪些安全属性遭到破坏。我们可以先不考虑每个步骤的可行性有多大，画出如下路径图。

![img](https://pic1.zhimg.com/80/v2-ac5e60696f0631ac155889deacf2e1d0_720w.webp)

图3 攻击路径上标注节点安全属性示例

然后开发人员可以适用工具从数据库中根据安全属性、资产种类查询到对应节点存在哪些攻击方法，包括攻击方法描述、前置条件、攻击原理与示例说明、相关漏洞、减缓措施，并从前置条件可以推断此攻击方法是否适用当前节点，即可完成攻击树模型的确定。当电气拓扑很复杂，或者是目标资产的分发路径多样化，导致攻击分支过于庞杂时，可以做适当的裁剪，当识别到某个路径的可行性非常低时，可无需继续向下展开分析。