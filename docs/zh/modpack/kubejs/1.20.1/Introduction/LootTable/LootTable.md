# 战利品

## 前言

- 本条目作为战利品章节所需必备知识的页面，你可以先阅读战利品章节的其他页面，遇到不理解的名词再折返来查看解释。

- **`战利品`** 战利品用于决定在何种情况下生成何种物品。比如自然生成的容器和可疑的方块内容物、破坏方块时的掉落物、杀死实体时的掉落物、钓鱼时可以钓上的物品、猪灵的以物易物——引用自[minecraft-wiki/战利品表](https://zh.minecraft.wiki/w/%E6%88%98%E5%88%A9%E5%93%81%E8%A1%A8)，战利品触发时，将测试所持有的所有战利品池的谓词，过滤出条件通过的战利品池，对这些战利品池中的每个战利品项测试战利品项谓词，随后应用战利品修饰，如果战利品修饰也有谓词，则也需要判断谓词是否通过。

- **`战利品谓词`** 战利品谓词是应用于战利品池、战利品项、战利品修饰的条件组，当用于战利品池时，谓词的通过与否决定该池是否参与抽取；当应用于战利品项时，谓词的通过与否影响该战利品项的掉落；当应用于战利品修饰时，谓词的通过与否影响该战利品修饰的执行。

- **`战利品修饰`** 战利品修饰是应用于战利品池或战利品项的函数，当用于战利品池时，整个战利品池的战利品项都会尝试应用战利品修饰，当用于战利品项时，该项掉落时会尝试应用战利品修饰。

## 战利品池

- 存放许多战利品项的数组，当触发战利品时将从战利品池按抽取次数抽取若干次战利品项。

### 抽取次数

- 决定尝试抽取多少次战利品项，使用1个语句设置即可。

- 语句：pool.rolls = 数字提供器; 请查看[数字提供器](../MiscellaneousKnowledge/NumberProvider.md)

- 语句：pool.setBinomialRolls(重复次数, 成功概率); 设置符合二项分布的抽取次数

- 语句：pool.setUniformRolls(最小值, 最大值); 设置抽取次数区间

## 战利品项

- 作为战利品池的元素而存在。

### 添加物品类型战利品项

- 向战利品池中添加物品类型的战利品项。

- 语句：pool.addItem(args); 具有3个方法重载。

::: code-group

```js [简单]
// 向战利品池中添加一个物品类型战利品
ServerEvents.entityLootTables(event => {
    event.modifyEntity('minecraft:husk', loot => {
        loot.addPool(pool => {
            pool.addItem('minecraft:golden_apple')
        })
    })
})
```

```js [权重]
// 向战利品池中添加一个物品类型战利品并设置权重
ServerEvents.entityLootTables(event => {
    event.modifyEntity('minecraft:husk', loot => {
        loot.addPool(pool => {
            pool.addItem('minecraft:golden_apple', 5)
        })
    })
})
```

```js [物品个数]
// 向战利品池中添加一个物品类型战利品并设置权重与个数，个数由数字提供器决定
ServerEvents.entityLootTables(event => {
    event.modifyEntity('minecraft:husk', loot => {
        loot.addPool(pool => {
            pool.addItem('minecraft:golden_apple', 5)
        })
    })
})
```

:::

### 添加空类型战利品项

- 空的战利品项，抽到它什么都不会掉。

- 语句：pool.addEmpty(权重);

- 示例：尸壳死亡时在该战利品池中将有5的权重什么都不掉。

```js
ServerEvents.entityLootTables(event => {
    event.modifyEntity('minecraft:husk', loot => {
        loot.addPool(pool => {
            pool.addItem('minecraft:golden_apple', 5)
            pool.addEmpty(5)
        })
    })
})
```

### 添加标签类型战利品项

- 创建一个物品标签类型战利品项，将物品标签的全部或单个物品作为战利品。

- 语句：pool.addTag(标签, 是否从中抽取1个物品); 如果为否，则生成标签中全部物品作为战利品。

:::: warning **注意**
::: justify
从标签中抽取物品为true时无法应用战利品修饰，原因：漏洞。目前该漏洞仍在 1.21.1 和 24w33a 中，见[漏洞追踪](https://bugs.mojang.com/browse/MC-212671)
:::
::::

- 示例：尸壳死亡掉落1个猪灵喜爱的物品。

```js
ServerEvents.entityLootTables(event => {
    event.modifyEntity('minecraft:husk', loot => {
        loot.addPool(pool => {
            pool.addTag('minecraft:piglin_loved', true)
            
        })
    })
})
```

## 战利品谓词

### 随机概率

- 通过小数表示的随机概率。

- 语句：randomChance(数字);

- 例：

::: code-group

```js [应用战利品池]
// 有0.5的概率尝试抽取该池 此时概率决定是否尝试抽取战利品池
ServerEvents.blockLootTables(event => {
    event.modifyBlock('minecraft:gravel', loot => {
        loot.addPool(pool => {
            pool.rolls = [1, 1];
            pool.addItem('minecraft:gunpowder');
            pool.randomChance(0.5);
        })
    })
})
```

```js [应用战利品项]
// 有0.5的概率尝试掉落火药 此时概率决定抽取战利品池后是否掉落火药
ServerEvents.blockLootTables(event => {
    event.modifyBlock('minecraft:gravel', loot => {
        loot.addPool(pool => {
            pool.rolls = [1, 1];
            pool.addItem('minecraft:gunpowder').randomChance(0.5);
        })
    })
})
```

:::

### 抢夺影响概率

- 根据抢夺附魔等级影响条件通过的概率。

- 语句：randomChanceWithLooting(无抢夺的基础概率,每级抢夺增加的概率);

- 示例：尸壳有0.2概率掉落苹果，每1级抢夺多0.2概率。

```js
ServerEvents.entityLootTables(event => {
    event.modifyEntity('minecraft:husk', loot => {
        loot.addPool(pool => {
            pool.addItem('minecraft:golden_apple', 5, 1)
            .randomChanceWithLooting(0.2, 0.2)
        })
    })
})
```

### 未被爆炸破坏

- 返回成功概率为1 / 爆炸半径，如果上下文未传递爆炸则始终通过。

- 语句：survivesExplosion()

- 示例：如果燧石被爆炸摧毁不会掉落火药。

```js
ServerEvents.blockLootTables(event => {
    event.modifyBlock('minecraft:gravel', loot => {
        loot.addPool(pool => {
            pool.rolls = [1, 1];
            pool.addItem('minecraft:gunpowder').survivesExplosion()
        })
    })
})
```

### 被玩家所击杀

- 实体死于被玩家击杀则条件通过。

- 语句：killedByPlayer();

- 示例：尸壳死于玩家掉落金苹果。

```js
ServerEvents.entityLootTables(event => {
    event.modifyEntity('minecraft:husk', loot => {
        loot.addPool(pool => {
            pool.addItem('minecraft:golden_apple', 5, 1)
            .killedByPlayer()
        })
    })
})
```

## 战利品修饰

### 对战利品项随机附魔

- 将对战利品项从附魔列表中随机附魔。

- 语句：enchantRandomly(附魔id数组);

- 示例：掉落了保护1的金苹果。

```js
ServerEvents.entityLootTables(event => {
    event.modifyEntity('minecraft:husk', loot => {
        loot.addPool(pool => {
            pool.addItem('minecraft:golden_apple', 5, 1)
            .enchantRandomly(['minecraft:protection'])
        })
    })
})
```

### 对战利品项按等级附魔

- 对战利品项执行一次数字提供器返回的等级的附魔。

- 语句：.enchantWithLevels(数字提供器, 是否包含宝藏附魔);

- 示例：尸壳掉落一把30级附魔的铁剑。

```js
ServerEvents.entityLootTables(event => {
    event.modifyEntity('minecraft:husk', loot => {
        loot.addPool(pool => {
            pool.addItem(Item.of('minecraft:iron_sword', '{Damage:0}'), 5, 1)
            .enchantWithLevels(30, true)
        })
    })
})
```

### 抢夺额外掉落

- 语句：lootingEnchant(每级抢夺掉落数-数字提供器, 战利品项总计最大掉落数);

- 示例：尸壳掉落金苹果，每多1级抢夺额外掉落1-2个，总共最多掉落6个。

```js
ServerEvents.entityLootTables(event => {
    event.modifyEntity('minecraft:husk', loot => {
        loot.addPool(pool => {
            pool.addItem('minecraft:golden_apple', 5, 1)
            .lootingEnchant([1, 2], 6) 
        })
    })
})
```

### 熔炉冶炼

- 得到物品放入熔炉冶炼后的产物。

- 语句：furnaceSmelt()

- 示例：尸壳死亡掉落橡木的熔炉冶炼产物。

```js
ServerEvents.entityLootTables(event => {
    event.modifyEntity('minecraft:husk', loot => {
        loot.addPool(pool => {
            pool.addItem('minecraft:oak_wood', 5, 1).furnaceSmelt()
            
        })
    })
})
```
