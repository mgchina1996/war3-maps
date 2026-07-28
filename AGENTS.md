# Warcraft III 地图文件说明

本项目由 `w3x-object-studio` 导出。修改时先根据字段来源选择正确文件。

## 文件作用

### `war3map.j`

地图JASS脚本，用于修改游戏逻辑、伤害公式、触发器，以及英雄、技能和物品效果。

### `table/`

Warcraft对象编辑器OBJ数据：

```text
table/ability.ini  → war3map.w3a，技能OBJ
table/buff.ini     → war3map.w3h，魔法效果OBJ
table/upgrade.ini  → war3map.w3q，科技OBJ
table/item.ini     → war3map.w3t，物品OBJ
table/unit.ini     → war3map.w3u，单位OBJ
table/w3i.ini      → war3map.w3i，地图信息
```

只有需要修改 `war3map.w3*` 中的对象覆盖数据时才编辑 `table/`。

### `slk/`

SLK优化地图的数值、规则和模型数据：

```text
slk/ability.ini  → 技能等级、伤害、范围、冷却等数值
slk/buff.ini     → 魔法效果SLK数据
slk/upgrade.ini  → 科技等级和效果
slk/item.ini     → 物品价格、等级、技能列表等数值
slk/unit.ini     → 单位生命、护甲、攻击、技能列表和模型
```

### `profile/`

名称、图标、提示和说明文字：

```text
profile/ability.ini  → 技能名称、图标、Tip、Ubertip
profile/buff.ini     → 魔法效果名称、图标和说明
profile/upgrade.ini  → 科技名称、图标和说明
profile/item.ini     → 物品名称、图标和说明
profile/unit.ini     → 单位名称、图标、提示和英雄称谓
```

### `resources/`

用于新增或替换模型、贴图等地图资源，配置文件为：

```text
resources/manifest.json
```

### `.w3x-object-studio.json`

增量回写校验文件，禁止修改或删除。

## 在哪里修改

```text
修改JASS逻辑               → war3map.j
修改OBJ对象数据            → table/对应类型.ini
修改SLK数值、规则或模型     → slk/对应类型.ini
修改名称、图标、提示和说明   → profile/对应类型.ini
新增或替换模型、贴图        → resources/manifest.json
```

如果同一对象同时存在于 `table/` 和 `slk/`，不要混合修改，先确认地图实际使用的是OBJ还是SLK。

## 怎么修改

对象以四字符ID区分：

```ini
[hcas]
_parent = "hcas"
```

只修改目标对象段中的目标字段，不要删除段标题或 `_parent`。

正确示例：

```ini
[hcas]
_parent = "hcas"
def = 500
file = "Models\\Hero.mdl"
```

注意：

- 数字不要加引号。
- 字符串使用双引号。
- 路径中的反斜杠保持 `\\`。
- 不要格式化整个文件。
- 不要修改无关对象。