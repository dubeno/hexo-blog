---
title: 一致性hash-系统设计
date: 2020-05-23 20:03:15
tags:
---
一致性hash通过将数据划分到几个存储服务器来实现存储系统的扩展性
当我们有大量数据分布式的存储在许多服务器上(数据库服务器)，并且服务器的数量会持续发生变化(增加新的服务器或者删除服务器)时，我们就会使用到一致性hash

# 为什么不使用简单Hash

简单的hash函数通过data/key，产生一个落在某个范围内的一个数字。

假设我们有大量数据，我们首先要计算它的哈希值(比如使用md5)，这样做是为了使我们可以获得[0 – 2 ^（128）− 1]范围内的随机值。
现在，我们的哈希函数将类似于以下server_number = hash（key）％n，这将算出一个在[0-（n-1）]范围内的随机数，并且n是服务器数。

```
Example -> number of servers, n = 3 and we have 6 keys
           keys          hash value     server_number(hashing)
           key1            100                1
           key2            577                1
           key3            872                2
           key4            376                0                  
           key5            23                 2
           key6            6798               0
```
下面这张图显示了key如何分配到3台机器上
<span><div style="text-align: center;">
{% asset_img Diagram-4-1.png one %}
</div></span>


使用简单哈希将键映射到数据库服务器

添加服务器时就会出现问题,现在将服务器数量变成4，可以看到此时需要重新计算所有的key的hash,key的分布将发生变化，这就是说，大量的key将进行数据迁移。

```
Example -> number of servers, n = 4 and we have 6 keys
           keys          hash value     server_number(hashing)
           key1            100                0
           key2            577                1
           key3            872                0
           key4            376                0                  
           key5            23                 3
           key6            6798               2
```
<span><div style="text-align: center;">
{% asset_img Diagram-5.jpg two %}
</div></span>
新服务器3被添加，因此所有的key需要rehash。

我们将遇到的另一个问题是数据倾斜，即，过多的key将hash到同一个服务器上，而hash到其他服务器的则很少，或者key分布不均匀。
<span><div style="text-align: center;">
{% asset_img Diagram-6.png three %}
</div></span>
现在我们总结一下常规hash遇到的问题：

- 添加服务器将发生rehash,导致数据迁移
- 删除服务器将发生rehash,导致数据迁移
- 可能会发生数据分布不均匀-数据倾斜

# 一致性Hash
一致性哈希可以帮助我们以最小化重组的方式在一组节点/服务器之间分布数据。一致性哈希的神奇之处在于我们向服务器分配key的方式。

在一致的哈希中，哈希功能独立于节点/服务器的数量,与服务器的数量无关。我们假设有这么一个虚拟环，key和服务器分布在这个环上面。


哈希函数：  position = hash（key）％（2 ^ 32）。这里2 ^ 32代表位置（或环长），是完全随机的数字，您可以选择任意大数。

# 查找服务器（数据库服务器）的位置和环中的键
- 为了找到服务器的位置，我们可以获取服务器IP地址的哈希值，然后使用上述哈希函数计算其位置。
- 使用上述哈希函数找到每个key的哈希值并找到其位置，然后将其放在环上对应的位置。
- 将服务器放置在环中计算出的位置。
- 将密钥映射到服务器，该服务器具有与密钥相同的哈希值（密钥哈希值==服务器哈希值）。
- 如果密钥哈希值与任何服务器都不匹配，则密钥将按顺时针方向映射到最近的服务器。

<span><div style="text-align: center;">
{% asset_img Diagram-1-1-1.png four %}
</div></span>

# 向环添加新服务器
假设我们要添加一个新服务器，并假设哈希值位于服务器0和服务器1之间。
<span><div style="text-align: center;">
{% asset_img Diagram-1-2.png five %}
</div></span>

添加新服务器后，我们不必rehash所有键值，而只需rehash在服务器0和服务器1之间的keys。平均而言，如果我们有n个服务器和k个key，则平均而言，我们仅需要重新哈希k / n个密钥。这是对简单散列的重大改进。

# 从环上移除服务器
万一服务器出现故障，那么我们只需要rehash存储在这台故障服务器中key，然后把这些key存储在其顺时针相邻服务器中。此处假设服务器2发生故障。
<span><div style="text-align: center;">
{% asset_img Diagram-1-1-2.png six %}
</div></span>

# 非均匀分布
现在可能还会出现另一个问题。非均匀哈希问题，即，大多数密钥被哈希到某台机器上或就近的某个服务器。这是不理想的，因为这一台服务器存储了比其他所有其他服务器更多的key。
<span><div style="text-align: center;">
{% asset_img Diagram-1-1-4.png serven %}
</div></span>
为了克服这个问题，我们引入了每个服务器的更多副本，并且服务器的每个副本被散列为不同的值，这意味着有更多的机器被放置在环上的随机位置。随着这些副本数量的增加，分布将变得越来越均匀。

<span><div style="text-align: center;">
{% asset_img Diagram-1-1-5.jpg eight %}
</div></span>



So,这就是一致性哈希!