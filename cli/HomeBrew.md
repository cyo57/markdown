# HomeBrew

安装：`brew install [formula]`
卸载：`brew uninstall [formula]`
更新：`brew update` （更新 Homebrew，使之后下载升级有效）
升级：`brew upgrade` （升级list中所有）
`brew pin [formula]` 固定软件
`brew unpin [formula]` 解除固定
`brew upgrade [formula]` 升级某一软件：
`brew update && brew upgrade && brew cleanup` 更新+升级+清理
待升级：`brew outdated` （列出已安装中待升级）
清理：`brew cleanup` （清理不需要的版本极其安装包缓存）
列出已装软件列表：`brew list`
搜索特定软件：`brew search [formula]`
查看依赖包：`brew deps [formula]` 可以查看该软件的依赖包
查询：`brew info [formula]` （主要看具体的信息，比如目前的版本，依赖，安装后注意事项等）
检查：`brew doctor`
