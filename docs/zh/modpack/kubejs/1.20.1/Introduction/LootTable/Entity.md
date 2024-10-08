# 实体战利品

## 前言

- **`数字提供器`** 返回数字的表达式，详情查看[数字提供器](../MiscellaneousKnowledge/NumberProvider.md)

- **`战利品`** 了解战利品可用的函数以及结构，查看[战利品](./LootTable.md)

- 事件：ServerEvents.entityLootTables(event=>{});

## 战利品

### 替换原有战利品

- 战利品池抽取次数与战利品项物品个数是一个数字提供器

- 语句：event.addEntity(方块id, loot => { loot.addPool(pool => { }) });

- 示例：砂砾只会掉落火药。

::: code-group

```js [单个物品]
ServerEvents.entityLootTables(event => {
    // addEntity将替换该实体战利品
    event.addEntity('minecraft:husk', loot => {
        // addPool创建一个战利品池
        loot.addPool(pool => {
            // 战利品池抽取次数
            pool.rolls = [1, 1];
            // 添加物品
            pool.addItem('minecraft:gunpowder')
        })
    })
})
```

```js [权重列表]
ServerEvents.entityLootTables(event => {
    // addEntity将替换该实体战利品
    event.addEntity('minecraft:husk', loot => {
        // addPool创建一个战利品池
        loot.addPool(pool => {
            // 战利品池抽取次数
            pool.rolls = [1, 1];
            // 添加物品
            pool.addItem('minecraft:gunpowder', 50)
            pool.addItem('minecraft:iron_ingot', 50)
        })
    })
})
```

```js [具有战利品项个数提供器的权重列表]
ServerEvents.entityLootTables(event => {
    // addEntity将替换该实体战利品
    event.addEntity('minecraft:husk', loot => {
        // addPool创建一个战利品池
        loot.addPool(pool => {
            // 战利品池抽取次数
            pool.rolls = [1, 1];
            // 添加物品
            pool.addItem('minecraft:gunpowder', 50, {min: 1, max: 3})
            pool.addItem('minecraft:iron_ingot', 50, {min: 1, max: 3})
        })
    })
})
```

:::

### 添加额外战利品

- 战利品池抽取次数与战利品项物品个数是一个数字提供器

- 语句：event.modifyEntity(方块id, loot => { loot.addPool(pool => { }) });

- 示例：尸壳还会额外掉落火药。

::: code-group

```js [单个物品]
ServerEvents.entityLootTables(event => {
    // modifyEntity将为该实体添加新战利品池
    event.modifyEntity('minecraft:husk', loot => {
        // addPool创建一个战利品池
        loot.addPool(pool => {
            // 战利品池抽取次数
            pool.rolls = [1, 1];
            // 添加物品
            pool.addItem('minecraft:gunpowder')
        })
    })
})
```

```js [权重列表]
ServerEvents.entityLootTables(event => {
    // modifyEntity将为该实体添加新战利品池
    event.modifyEntity('minecraft:husk', loot => {
        // addPool创建一个战利品池
        loot.addPool(pool => {
            // 战利品池抽取次数
            pool.rolls = [1, 1];
            // 添加物品
            pool.addItem('minecraft:gunpowder', 50)
            pool.addItem('minecraft:iron_ingot', 50)
        })
    })
})
```

```js [具有战利品项个数提供器的权重列表]
ServerEvents.entityLootTables(event => {
    // modifyEntity将为该实体添加新战利品池
    event.modifyEntity('minecraft:husk', loot => {
        // addPool创建一个战利品池
        loot.addPool(pool => {
            // 战利品池抽取次数
            pool.rolls = [1, 1];
            // 添加物品
            pool.addItem('minecraft:gunpowder', 50, {min: 1, max: 3})
            pool.addItem('minecraft:iron_ingot', 50, {min: 1, max: 3})
        })
    })
})
```

:::

### 修改原有战利品

- 等待后续...

## 战利品谓词

## 战利品修饰
