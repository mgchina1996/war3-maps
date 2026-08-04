## 数据目录作用

以下路径均以具体版本的 `data/` 目录为根，例如 `4.9.7/data/`。

### `war3map.j`

地图 JASS 脚本。

适合修改：

- 游戏逻辑
- 触发器
- 伤害公式
- 英雄、技能、物品的脚本效果
- 运行时创建单位、添加技能、设置属性等逻辑

### `table/`

对象编辑器导出的 OBJ 覆盖数据，通常对应 `war3map.w3*` 对象文件。

```text
table/ability.ini  技能 OBJ 数据，通常对应 war3map.w3a
table/buff.ini     魔法效果 OBJ 数据，通常对应 war3map.w3h
table/upgrade.ini  科技 OBJ 数据，通常对应 war3map.w3q
table/item.ini     物品 OBJ 数据，通常对应 war3map.w3t
table/unit.ini     单位 OBJ 数据，通常对应 war3map.w3u
table/w3i.ini      地图信息，通常对应 war3map.w3i
```

只有需要修改对象编辑器覆盖字段时才编辑 `table/`。

### `slk/`

SLK 数据，常用于优化后的单位、技能、物品、科技等基础数值。

```text
slk/ability.ini  技能等级、伤害、范围、冷却、魔耗等数值
slk/buff.ini     魔法效果 SLK 数据
slk/upgrade.ini  科技等级和效果
slk/item.ini     物品价格、等级、技能列表等数值
slk/unit.ini     单位生命、护甲、攻击、技能列表、模型等数据
```

### `profile/`

界面显示文本和资源引用。

```text
profile/ability.ini  技能名称、图标、Tip、Ubertip
profile/buff.ini     魔法效果名称、图标和说明
profile/upgrade.ini  科技名称、图标和说明
profile/item.ini     物品名称、图标和说明
profile/unit.ini     单位名称、图标、提示、英雄称谓
```

### `resources/`

用于新增或替换模型、贴图等地图资源，配置文件为：

```text
resources/manifest.json
```

资源文件放在 `resources/` 目录内，`manifest.json` 只负责声明这些文件写入地图 MPQ 后的内部路径。

示例目录：

```text
resources/
├─ manifest.json
└─ files/
   ├─ Hero.mdx
   └─ Textures/
      └─ Hero.blp
```

示例 `resources/manifest.json`：

```json
{
  "version": 1,
  "files": [
    {
      "source": "files/Hero.mdx",
      "target": "Models\\Hero.mdx",
      "mode": "add"
    },
    {
      "source": "files/Textures/Hero.blp",
      "target": "Textures\\Hero.blp",
      "mode": "replace"
    }
  ]
}
```

- `source`：相对于 `resources/` 的本地文件路径。
- `target`：写入地图 MPQ 内部的资源路径，通常使用 `\\`。
- `mode`：`add` 要求目标资源原本不存在；`replace` 可替换已有资源，也可在不存在时新增。
- 不要通过 `resources/manifest.json` 修改 `war3map.j`、`war3map.w3i`、物编文件、`war3map.wts` 或 MPQ 内部表文件。

### `.w3x-object-studio.json`

`w3x-object-studio` 的增量回写和校验文件。

不要修改、格式化、删除或重建该文件，除非用户明确要求并说明原因。

## 修改前先判断字段来源

同一对象可能同时出现在 `table/`、`slk/`、`profile/` 或 `war3map.j` 中。修改前应先搜索对象 ID、名称或字段，判断实际生效来源。

优先规则：

- 修改脚本逻辑、触发逻辑、公式：改 `data/war3map.j`
- 修改对象编辑器覆盖字段：改 `data/table/对应类型.ini`
- 修改 SLK 数值、规则、模型字段：改 `data/slk/对应类型.ini`
- 修改名称、图标、提示、说明文字：改 `data/profile/对应类型.ini`
- 新增或替换地图资源：改 `data/resources/manifest.json` 及对应资源文件

如果不能确定实际使用 `table/` 还是 `slk/`，先搜索同一对象 ID 在两个目录中的字段，再按已有数据结构保持一致，不要混合改无关来源。

## INI 修改规则

对象通常以四字符 ID 分段：

```ini
[hcas]
_parent = "hcas"
```

修改时遵守：

- 只修改目标对象段中的目标字段。
- 不要删除段标题。
- 不要删除或改动 `_parent`，除非用户明确要求。
- 数字不要加引号。
- 字符串使用双引号。
- 路径中的反斜杠保持 `\\`。
- 不要格式化整个文件。
- 不要调整无关对象或字段顺序。

示例：

```ini
[hcas]
_parent = "hcas"
def = 500
file = "Models\\Hero.mdl"
```

## 工作方式

- 修改前先定位具体地图版本目录，目前是 `讨伐打魔王/4.9.7/data/`。
- 优先使用 `rg` 搜索对象 ID、中文名、英文名、技能码或字段名。
- 保持改动最小，只编辑用户请求相关对象。
- 不要把本说明理解为完整 w3x 反编译规范；这里描述的是当前仓库导出的这部分数据的维护规则。

## 天赋文档同步规则

- `4.97/docs/天赋清单.md` 是当前版本已实装天赋的统一索引。
- 在代码中新加天赋时，必须在同一次修改中把天赋编号、名称、所属卡池或类型、获取方式及必要备注记录到该清单。
- 删除、重命名天赋，修改卡池范围、获取条件或进化关系时，也必须同步更新该清单。
- 只存在于 `4.97/天赋/` 下的方案属于设计稿；未写入实际脚本前，应标记为“尚未实装”，不能列入已实装天赋数量。
- 修改天赋效果时，至少同步更新清单中的效果备注；如果该天赋存在独立详细文档，还需同步更新详细文档。
