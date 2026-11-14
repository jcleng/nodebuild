# nodebuild

通过 GitHub Actions 自动 clone 一个 Node.js 项目、按指定命令构建，并将构建产物与源码打包成 zip 上传到 Release。

## 功能

- 克隆指定仓库与分支（`git clone --depth 1`）
- 使用指定版本的 Node.js 环境进行构建
- 将构建产物 `dist` 目录打包为 `dist_<项目名>.zip`
- 将源码（排除 `node_modules`、`.git`）打包为 `src_<项目名>.zip`
- 自动生成带有构建参数与 SHA256 校验值的 Release 说明并发布

## Workflow 说明

工作流定义在 [`./.github/workflows/main.yml`](./.github/workflows/main.yml)，名称为 **RUN**，支持两种触发方式：

- `repository_dispatch`：通过仓库外部事件（如 GitHub API / PAT）触发
- `workflow_dispatch`：在 GitHub 仓库的 **Actions → RUN → Run workflow** 页面手动触发

### 手动触发参数（workflow_dispatch）

| 参数 | 说明 | 默认值 |
| --- | --- | --- |
| `repo_url` | GitHub 项目地址 | `https://github.com/jcleng/jellyfin-web` |
| `branch` | 分支 | `artplayer` |
| `node_version` | Node.js 版本（可选 `node18`/`node20`/`node22`/`node24`/`node26`） | `node26` |
| `build_command` | 构建命令 | `npm install && npm run build:production` |
| `dist_dir` | 构建产物 dist 目录 | `dist` |

### 使用步骤

1. 进入仓库的 **Actions** 标签页，选择 **RUN** 工作流。
2. 点击 **Run workflow**，按需填写上述参数。
3. 工作流运行完成后，在 **Releases** 中查看以 `<时间戳>_<项目名>` 命名的发布。

### Release 产物

每次运行会生成一个 Release，包含：

- `release.txt`：构建参数、文件 SHA256 校验值与大小信息
- `dist_<项目名>.zip`：构建产物目录（由 `dist_dir` 指定）
- `src_<项目名>.zip`：完整源码（已排除 `node_modules` 与 `.git`）

## 注意事项

- `dist_dir` 指定的目录必须存在，否则工作流会以“目录不存在”错误退出。
- 工作流使用 `softprops/action-gh-release@v1`，依赖仓库默认 `GITHUB_TOKEN` 完成发布。
