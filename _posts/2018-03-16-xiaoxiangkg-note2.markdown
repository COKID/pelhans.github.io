---
layout:     post
title:      "知识图谱入门 (二)" 
subtitle:   "知识表示与知识建模"
date:       2018-03-16 00:15:18
author:     "Pelhans"
header-img: "img/post-bg-universe.jpg"
header-mask: 0.3 
catalog:    true
tags:
    - Knowledge Graph
---


> 本讲首先对早期的知识表示做了一个简单介绍，而后详细介绍了基于语义网的知识表示框架，如RDF和RDFS和查询语言SQARQL。最终给出几个典型的知识项目的知识表示。

* TOC
{:toc}

#  知识表示历史

## 知识的概念

知识表示就是对知识的一种描述，或者说是对知识的一组约定，一种计算机可以接受的用于描述知识的数据结构。它是机器通往智能的基础，使得机器可以像人一样运用知识。

知识具有相对正确性、不确定性、可表示性以及可利用性的特点。根据不同划分标准，知识可以分为不同的类别。例如按照作用范围分类，可分为常识性知识和领域性知识。按作用及表示分类为事实性知识、过程性知识、控制知识。按确定性分类有确定性知识，不确定性知识。按结构及表现形式可分为逻辑性知识和形象性知识。

## 早期的知识表示方法

### 一阶谓词逻辑
谓词逻辑(Lp)可以对原子命题做进一步分析，分析出其中的个体词、谓词、量词，研究它们的形式结构的逻辑关系、正确的推理形式和规则。

一阶逻辑是数理逻辑的基础部分，主要包括经典命题逻辑和一阶谓词逻辑，但实际上一阶谓词逻辑包含了命题逻辑。一阶逻辑之所以是“一阶”的，是因为它所包含的谓词逻辑是一阶的。谓词就是表示对象属性的语词。对象的属性具有层次，在谓词用法中，这种层次叫做“阶”。所谓一阶谓词就是指刻画个体属性的谓词，如“红色”“大于”等谓词都只适用于个体概念，像“鲜艳”“传递性”等用来刻画“红色”“大于”这种谓词的谓词就是高阶谓词了，它们刻画的是属性的属性。

一阶谓词逻辑具有自然性、接近自然语言、容易接受、严密性、易于转化为计算机内部形式等优点，但同时也具有无法表示不确定性知识、难以表示启发性知识及元知识、组合爆炸、效率低等缺点。为了克服以上缺点，人们提出了Horn逻辑、描述逻辑等改进方案。

### 产生式系统

产生式系统是一种更广泛的规则系统，和谓词逻辑有关联，也有区别。早起的专家系统多数是基于产生式系统的。产生式知识表示法是常用的知识表示方式之一。它是依据人类大脑记忆模式中的各种知识之间的大量存在的因果关系，并以“IF-THEN”的形式，即产生式规则表示出来的。这种形式的规则捕获了人类求解问题的行为特征，并通过认识--行动的循环过程求解问题。一个产生是系统由规则库、综合数据库和控制机构三个基本部分组成。

谓词逻辑中的规则与产生式的基本形式相似,事实上,蕴涵式只是产生式的一种特殊情况。产生式规则表示法具有非常明显的优点，如自然型好，易于模块化管理、能有效表示知识、知识表示清晰等优点。但是产生式规则也有着效率不高、不能表达具有结构性的知识等缺点。因此,人们经常将它与其它知识表示方法(如框架表示法、语义网络表示法)相结合。

### 框架表示法

框架表示法是明斯基于1975年提出来的，其最突出的特点是善于表示结构性知识，能够把知识的内部结构关系以及知识之间的特殊关系表示出来，并把与某个实体或实体集的相关特性都集中在一起。

框架表示法认为人们对现实世界中各种事物的认识都是以一种类似于框架的结构存储在记忆中的。当面临一个新事物时,就从记忆中找出一个合适的框架,并根据实际情况对其细节加以修改、补充,从而形成对当前事物的认识。

框架是一种描述固定情况的数据结构，一般可以把框架看成是一个节点和关系组成的网络。框架的最高层次是固定的，并且它描述对于假定情况总是正确的事物，在框架的较低层次上有许多终端--被称为槽（Slots）。在槽中填入具体值，就可以得到一个描述具体事务的框架，每一个槽都可以有一些附加说明--被称为侧面（Facet），其作用是指出槽的取值范围和求值方法等。一个框架中可以包含各种信息：描述事物的信息，如何使用框架的信息，关于下一步将发生什么情况的期望及如果期望的事件没有发生应该怎么办的信息等等，这些信息包含在框架的各个槽或侧面中。

一个具体事物可由槽中已填入值来描述，具有不同的槽值得框架可以反映某一类事物中的各个具体事物。相关的框架链接在一起形成了一个框架系统，框架系统中由一个框架到另一个框架的转换可以表示状态的变化、推理或其它活动。不同的框架可以共享同一个槽值，这种方法可以把不同角度搜集起来的信息较好的协调起来。

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_1.png)

框架表示法对于知识的描述非常完整和全面;基于框架的知识库质量非常高;且框架允许数值计算,这一点优于其它知识表示语言。但框架的构建成本非常高,对知识库的质量要求非常高;框架的表达形式不灵活,很难同其它形式的数据集相互关联使用。

### 语义网络

语义网络是知识表示中最重要的方法之一，是一种表达能力强而且灵活的知识表示方法。语义网络利用节点和带标记的边结构的有向图描述事件、概念、状况、动作及客体之间的关系。带标记的有向图能十分自然的描述客体之间的关系。

语义网络由于其自然性而被广泛应用。采用语义网络表示的知识库的特征是利用带标记的有向图描述可能事件。结点表示客体、客体性质、概念、事件、状况和动作，带标记的边描述客体之间的关系。知识库的修改是通过插入和删除客体及其相关的关系实现的。采用网络表示法比较合适的领域大多数是根据非常复杂的分类进行推理的领域以及需要表示事件状况、性质以及动作之间的关系的领域。

语义网络的基本形式为(节点， 弧， 节点2)，节点表示各种事物、概念、情况、属性、动作、状态等，每个节点可以带有若干属性，一般用框架或元组表示。此外节点还可以是一个语义子网络，形成一个多层次的嵌套结构。语义网络中的弧表示各种语义联系，指明它所连接的节点间某种语义关系。节点和弧都必须带有标示，来方便区分不同对象以及对象间各种不同的语义联系。一个语义网络的例子为：

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_2.png)

**本质上是将逻辑运算符和逻辑项映射到了图中的元素**。语义网络具有以下优点：    
- 把各个节点之间的联系以明确、简洁的方式表示出来，是一种直观的表示方法；    
- 着重强调事物间的语义联系，体现了人类思维的联想过程，符合人们表达事物间的关系，因此把自然语言转换成语义网络较为容易;    
- 具有广泛的表示范围和强大的表示能力，用其他形式的表示方法能表达的知识几乎都可以用语义网络来表示；    
- 把事物的属性以及事物间的各种语义联系显示地表示出来，是一种结构化的知识表示法。

但语义网络也具有以下缺点：    
- 推理规则不十分明了，不能充分保证网络操作所得推论的严格性和有效性；    
- 一旦节点个数太多，网络结构复杂，推理就难以进行；    
- 不便于表达判断性知识与深层知识。

# 基于语义网的知识表示框架

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_3.png)

上图为W3C推荐的语义网标准栈，其中RDF和SPARQL为网络数据链接部分。与此同时，W3C还推出五星级标准，规定了RDF为标准数据格式，URI标准为事物命名等规范。

## RDF简介

### RDF概念

资源描述框架(Resource Description Framework， RDF)，R代表页面，图片、视频等任何具有URI标识符，D标识属性、特征和资源之间的关系，F标识模型、语言和这些描述的语法。在RDF中，知识总是以三元组的形式出现，即每一份知识都可以被分解为：(subject, predicate, object)。

$$(NJU, student, Pelhans) $$

与此同时，RDF三元组可以看做是图模型的边和顶点$$ (vertex, edge, vertex) $$,还可以将两个三元组结合起来表示：

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_4.png)

在RDF中resource和properties是以URIs的形式表示的，如$$http://mydomain.org/mypath/myresource$$。这样我们的表示就变成了这样：

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_5.png)

再结合URI的表示，我们可以把它简化为：

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_6.png)

在RDF中，properties的值可以是literals，如字符串，因此也可以长成：

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_7.png)

properties还可以是XML类型的，因此还可以长成：

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_8.png)

### RDF和RDFS

**RDFS(RDF Schema)在RDF的基础上提供了一个术语、概念的定义方式，以及那些属性可以应用到哪些对象上**。换句话说，RDFS为RDF模型提供了一个基本的类型系统。如：

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_9.png)

上述三元组表示用户自定义的元数据Author是Dublin Core的元数据Creator的子类。RDF Schema正是通过这样的方式来描述不同词汇集的元数据之间的关系,从而为网络上统一格式的元数据交换打下基础。

RDFS支持推理功能，如：

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_10.png)

## OWL和OWL2

前面我们知道，通过RDF(S)可以表达一些简单的语义，但在更复杂的场景下，RDF(S)语义表达能力显得太弱，还缺少诸多常用的特征。包括对局部值域的属性定义，类、属性、个体的等价性，不相交类的定义，基数约束，关于属性特征的描述等。因此W3C提出了OWL语言扩展RDF(S)，作为语义网上表示本体的推荐语言。

### OWL

W3C于2002年7月31日发布了OWL Web本体语言(OWL Web Ontology Language)工作草案的细节其目的是为了更好地开发语义网。OWL有三个子语言：OWL Lite、OWL DL、OWL Full。下表给出OWL三个子语言的特征于区别：

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_11.png)

#### OWL各语言如何选择

- 选择OWL Lite还是OWL DL主要取决于用户需要整个语言在多大程度上给出约束的可表达性;    
- 选择OWL DL还是OWL Full主要取决于用户在多大程度上需要RDF的元模型机制 (如定义类型的类型以及为类型赋予属性);    
- 在使用OWL Full而不是OWL DL时,推理的支持可能不能工作,因为目前还没有完全的支持OWL Full的系统实现。

综上所述，在要求简单是可采用OWL Lite，通常可采用OWL DL，对概念要求定义精确时采用OWL Full。(在Protege练习中感觉DL 和 Full区别并不明显)

#### OWL与RDF的关系

- OWL Full可以看成是RDF的扩展;    
- OWL Lite和OWL Full可以看成是一个约束化的RDF的扩展;    
- 所有的OWL文档 (Lite,DL,Full)都是一个RDF文档;    
- 所有的RDF文档都是一个OWL Full文档;    
- 只有一些RDF文档是一个合法的OWL Lite和OWL DL文档。

上面说的很模糊，在Protege操作中，OWL给我的感觉就是在RDFS的基础上，添加了很多描述类别、属性之间关系的定义或约束。,如两个类是否不相交这样的类属性。

#### OWL词汇扩展

|owl:equivalentClass|等价类|
|owl:equivalentProperty|等价属性|
|owl:sameIndividualAs|等价个体|
|owl:TransitiveProperty|属性传递|
|owl:inverseOf|属性互反|
|owl:FunctionalProperty|属性函数性|
|owl:SymmetricProperty|属性对称性|
|owl:allValuesFrom<br>owl:onProperty<br>owl:someValuesFrom<br>owl:onProperty<br>owl:cardinality<br>owl:onProperty|属性约束|
|owl:intersectionOf|相交的类|
|owl:oneOf|声明枚举类型|
|owl:disjointWith|声明两个类不相交|
|owl:unionOf|声明类的并运算|
|owl:minCardinality<br>owl:maxCardinality|最小最大的基数限定|
|owl:InverseFunctionalProperty|声明互反类具有函数属性|
|owl:hasValue|属性的局部约束时,声明所约束类必有一个取值|

### OWL2

OWL2是OWL的最新版本，老的OWL也称为OWL1，OWL2定义了一些OWL的子语言,通过限制语法使用,使得这些子语言能够更方便地实现,以及服务于不同的应用;OWL2也有三大子语言：OWL2 RL，OWL2 QL， OWL2 EL；

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_12.png)

OWL2 QL适合概念多的情况，OWL2 EL适合实例较多的情况，如医学领域，OWL2 RL适合高效推理。

#### OWL2 QL

QL代表query language的意思,专为基于本体的查询设计:    
- OWL 2 QL的复杂度是AC 0 ,非常适合大规模处理;    
- OWL 2的三大子语言中,QL最为简单;    
- OWL 2 QL是基于描述逻辑语言DL-Lite定义的。

OWL2 QL允许的核心词汇为：

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_13.png)

通过OWL 2 QL的语言限制,基于QL的本体查询可以优化到多项式对数时间复杂度。

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_14.png)

#### OWL2 EL

OWL 2 EL专为概念术语描述,推理而设计:

- 在生物医疗领域广泛应用,如临床医疗术语本体SNOMED CT;    
- 复杂度是PTime-Complete；    
- OWL2 EL是基于描述逻辑语言EL++定义的；

它允许的核心词汇为:

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_15.png)

OWL2 EL允许表达复杂的概念，如

$$Female ⊓ ∃likes.Movie ⊓ ∃hasSon.(Student ⊓ ∃attends.CSCourse )$$

#### OWL2 RL

OWL 2 RL在ter Horst的工作基础上延伸而来; 该工作的目的是将
OWL词汇引入RDFS,使得RDFS在表达能力上丰富起来,同时保持
计算复杂度在PTime级别。OWL 2 RL在RDFS的基础上引入属性的特殊特性 (函数性,互反性,对称性);允许声明等价性;允许属性的局部约束。OWL 2 RL与描述逻辑没有直接关系。

业界的一种观点是,OWL 2 RL是专为高效推理设计的本体语言(推理针对的是实例数据)。

OWL2 RL允许的核心词汇为：

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_16.png)

#### OWL2的推理系统

|OWL 2 DL reasoners| FaCT++ (Manchester), HermiT (Oxford), Pellet (Clarkparsia)|
|OWL 2 EL reasoners| CEL (Dresden), ELK (Oxford)|
|OWL 2 RL reasoners| Jena (HP Labs Bristol, Aberdeen), Oracle 11g OWL Reasoner (Oracle)|
|OWL 2 QL reasoners|QuOnto (Rome), Quill (Aberdeen)|

## SPARQL简介

SPARQL是RDF的查询语言，它基于RDF数据模型，可以对不同的数据集撰写复杂的连接，同时还被所有主流的图数据库支持。

SPARQL的查询结构如下图所示：

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_17.png)

从语法上结构上来看，SPARQL和SQL语言还是有一定的相似性的。比较重要的区别有：

- 变量,RDF中的资源,以“?”或者“$”指示；    
- 三元组模板 (triple pattern), 在WHERE子句中列示关联的三元组模板,之所以称之为模板,因为三元组中允许变量;    
- SELECT子句中指示要查询的目标变量    

有关SPARQL的详细操作指令我打算在实战单开一个小结把重要操作演示一遍。这里仅给出一个小例子看操作是什么样子的：

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_18.png)

## JSON-LD

为了方便程序员阅读知识标识，出现了JSON-LD，JSON-LD是JavaScript Object Notation for Linked Data的缩写,是一种基于JSON表示和传输互联数据 (Linked Data)的方法。JSON-LD描述了如何通过JSON表示有向图,以及如何在一个文档中混合表示互联数据及非互联数据。JSON-LD的语法和JSON兼容。

JSON-LD呈现出语义网技术的风格,它们有着类似的目标:围绕某类知识提供共享的术语。例如,每个数据集不应该围绕“name”重复发明概念。JSON-LD 的 实 现 没 有 选 择 大 部 分 语 义 网 技 术 栈 (Turtle/SPARQL/Quad Stores)而是以简单、不复杂以及面向一般开发人员的方式推进。下图给出JSON-LD事例，可以看出非常容易理解:

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_19.png)

## RDFa

RDFa(Resource Description Framework in attributes)是网页标记语言，也是W3C推荐的标准，它**扩充了XHTML的几个属性**,网页制作者可以利用这些属性在网页中添加可供机器读取的资源。与RDF的对应关系使得RDFa可以将RDF的三元组嵌入在XHTML文档中,它也使得符合标准的使用端可以从RDFa文件中提取出这些RDF三元组来。**RDFa从机器可理解的层面优化搜索,提升访问性以及网页数据的关联性**。

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_20.png)

## HTML5 Microdata

Microdata微数据,是在网页标记标记语言嵌入机器可读的属性数据，微数据使用可以来自自定义词汇表、带作用域的键/值对给DOM做标记。用户可以自定义微数据词汇表,在自己的网页中嵌入自定义的属性。微数据是给那些已经在页面上可见的数据施加额外的语义。当HTML的词汇不够用时,使用微数据可以取得较好的效果。

![](/img/in-post/xiaoxiangkg_note2/xiaoxiangkg_note2_21.png)

# Ref
 
[王昊奋知识图谱教程](http://www.chinahadoop.cn/course/1048)
