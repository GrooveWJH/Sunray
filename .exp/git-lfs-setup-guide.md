# Git LFS 设置操作指南

## 什么是 Git LFS？

Git LFS (Large File Storage) 是 Git 的扩展，专门用来处理大文件。它的工作原理是：
- 大文件本身存储在远程服务器上
- Git 仓库中只保存指向这些文件的指针（pointer files）
- 需要时才下载实际的大文件内容

## 我刚才执行的操作步骤

### 1. 初始化 Git LFS
```bash
git lfs install
```
**作用**：在当前仓库中启用 Git LFS 功能，设置必要的 Git hooks

### 2. 配置文件跟踪规则
```bash
git lfs track "*.csv"    # 跟踪所有 CSV 文件
git lfs track "*.DAE"    # 跟踪所有 DAE 模型文件  
git lfs track "*.sdf"    # 跟踪所有 SDF 描述文件
```
**作用**：告诉 Git LFS 哪些文件类型需要通过 LFS 管理

### 3. 添加配置文件
```bash
git add .gitattributes
```
**作用**：`.gitattributes` 文件记录了 LFS 跟踪规则，需要提交到仓库

### 4. 添加所有文件到暂存区
```bash
git add -A
```
**作用**：将所有修改过的文件添加到暂存区，LFS 跟踪的大文件会自动转换为指针文件

## 实际效果

### 本项目中被 LFS 管理的文件类型：

1. **CSV 文件 (6个)**：
   - `Simulation/gazebo_plugin/livox_laser_simulation/scan_mode/` 下的激光雷达扫描模式配置文件

2. **DAE 文件 (200+ 个)**：
   - AWS 3D 模型的网格文件
   - 车辆、建筑物、家具等各种 3D 模型

3. **SDF 文件 (100+ 个)**：
   - Gazebo 仿真环境的模型描述文件
   - 无人机、传感器、场景模型配置

## Git LFS 的实际工作机制

### 提交时发生什么：
1. 大文件被上传到 LFS 服务器
2. 本地 Git 仓库只保存小的指针文件（~100字节）
3. 指针文件包含文件的哈希值和大小信息

### 克隆时发生什么：
1. 快速下载 Git 历史和指针文件
2. 根据需要下载实际的大文件内容
3. 可以选择只下载当前分支需要的文件

## 日常使用命令

### 查看 LFS 状态
```bash
git lfs status           # 查看 LFS 文件状态
git lfs ls-files         # 列出所有 LFS 跟踪的文件
git lfs track            # 查看跟踪规则
```

### 手动下载 LFS 文件
```bash
git lfs pull             # 下载当前分支的所有 LFS 文件
git lfs fetch            # 获取 LFS 文件但不切换到工作目录
```

### 添加新的跟踪规则
```bash
git lfs track "*.stl"    # 跟踪新的文件类型
git lfs track "models/**" # 跟踪特定目录下所有文件
```

## 性能优势

### 仓库大小对比：
- **不使用 LFS**：整个仓库可能几个 GB，克隆很慢
- **使用 LFS**：Git 仓库只有几十 MB，大文件按需下载

### 操作速度提升：
- `git clone` 快速完成
- `git status`、`git log` 等操作不受大文件影响
- 分支切换更快

## 注意事项

1. **首次推送**：LFS 文件需要上传到 LFS 服务器，可能比较慢
2. **存储配额**：GitHub LFS 有存储和带宽限制
3. **兼容性**：团队成员都需要安装 Git LFS
4. **历史文件**：已经提交的大文件需要特殊处理才能迁移到 LFS

## 验证 LFS 设置

提交后可以通过以下方式验证：
```bash
# 查看 .gitattributes 内容
cat .gitattributes

# 查看某个大文件是否被 LFS 管理
git lfs ls-files | grep "某个文件名"

# 查看文件内容（应该是指针而不是实际内容）
git show HEAD:path/to/large/file.dae
```

## 总结

Git LFS 让我们能够：
- 在 Git 中高效管理大型 3D 模型和数据文件
- 保持仓库轻量级，提升开发体验
- 按需下载大文件，节省本地存储空间
- 维持完整的版本控制历史

这对于包含大量仿真模型和数据的机器人项目特别有用！