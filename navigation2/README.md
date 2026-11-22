# navigation2 子模块说明

本目录以 **git submodule** 方式引入 ROS 2 Navigation2 官方仓库 <https://github.com/ros-navigation/navigation2>，代码位于 `navigation2/navigation2`。

## 文件目录说明

```
study_with_ai/
├── .gitmodules                  # 子模块登记表，父仓库跟踪
├── README.md
└── navigation2/
    ├── README.md                # 本说明文件，父仓库跟踪
    └── navigation2/             # 子模块工作区，属 nav2 独立仓库
        ├── nav2_amcl/
        ├── nav2_bringup/
        ├── nav2_behavior_tree/
        ├── nav2_bt_navigator/
        ├── nav2_common/
        ├── nav2_costmap_2d/
        ├── nav2_planner/
        ├── nav2_controller/
        ├── doc/
        └── ...
```

外层 `navigation2/` 归父仓库，存放说明文件；内层 `navigation2/navigation2/` 是子模块目录，其中所有文件由 nav2 仓库管理，父仓库只记录它的一个提交哈希。

两处必须同时存在，缺一不可：

| 位置 | 作用 |
| --- | --- |
| `.gitmodules` | 记录子模块的 path 与 url（地址簿） |
| 索引中的 `160000` 条目 | gitlink 指针，`git submodule update` 真正依据它拉取代码 |

> 只有 `.gitmodules` 而没有 gitlink 时，`git submodule update --init --recursive` 会静默什么都不做。

## 拉取代码

在仓库根目录执行：

```bash
# 首次：初始化并拉取
git submodule update --init --recursive

# 之后：更新到父仓库记录的提交
git submodule update --recursive
```

克隆父仓库时也可以一步到位：

```bash
git clone --recurse-submodules <本仓库URL>
```

> `git pull` 不会自动拉取子模块内容，仍需执行 `git submodule update`。

## 子模块配置

`.gitmodules`（仓库根目录）：

```ini
[submodule "navigation2/navigation2"]
	path = navigation2/navigation2
	url = https://github.com/ros-navigation/navigation2
	branch = main
```

如需换用其他发行版分支，例如 Humble：

```bash
git submodule set-branch -b humble navigation2/navigation2
git submodule update --remote navigation2/navigation2
```

## 常见问题

- 目录为空、`git submodule status` 无输出：仓库索引缺少 gitlink（`git ls-files -s` 看不到 `160000` 条目）。先执行 `git submodule add <url> navigation2/navigation2` 建立指针，再 `git submodule update --init --recursive`
- 已登记但目录仍空：确认在仓库根目录执行命令
- `curl 28 ... Connection was reset`：GitHub 连接被重置，可试 `git -c http.version=HTTP/1.1 submodule update --init --recursive`，或配置代理后重试
