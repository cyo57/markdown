---
tags:
  - devops
date: "240728"
---
> GitHub Actions 是一种持续集成和持续交付 (CI/CD) 平台

## Github Actions 是什么

> GitHub Actions 是一种持续集成和持续交付 (CI/CD) 平台，可用于自动执行生成、测试和部署管道。 您可以创建工作流程来构建和测试存储库的每个拉取请求，或将合并的拉取请求部署到生产环境。
> GitHub Actions 不仅仅是 DevOps，还允许您在存储库中发生其他事件时运行工作流程。 例如，您可以运行工作流程，以便在有人在您的存储库中创建新问题时自动添加相应的标签。

很多操作在不同项目里面是类似的，完全可以共享。GitHub 注意到了这一点，想出了一个很妙的点子，允许开发者把**每个操作写成独立的脚本文件**，存放到代码仓库，使得其他开发者可以引用。

如果你需要某个 action，不必自己写复杂的脚本，直接引用他人写好的 action 即可，整个持续集成过程，就变成了一个 actions 的组合。这就是 GitHub Actions 最特别的地方。

Github Actions 官方[市场](https://github.com/marketplace?type=actions)


## 了解 Github Actions

### 工作流

![](assets/Pasted%20image%2020240729105053.png)

> [!tip]
> Actions 可以使用 Github 的服务器 也可以使用 自己的服务器
> 
> 多个工作流之间，默认是同时进行的，工作步骤则是按顺序进行的

运行一个或多个作业，由存储库中的 `.yaml` 或 `.yml` 文件定义，并在存储库中的事件触发时运行，也可以手动触发，或按定义的时间表触发。

工作流程在存储库的 `.github/workflows` 目录中定义，存储库可以有多个工作流程，每个工作流程都可以执行不同的任务集。

工作路径至少包含：
```shell
.github
	└─workflows
			pyrun.yml
```

当我们推送到远端仓库时，Github 会自动识别目录和 yaml 文件进行工作流。当我们推送空白的pyrun.yml时：

![](assets/Pasted%20image%2020240729105834.png)

![](assets/Pasted%20image%2020240729105753.png)

### 简单语法

查看上文的错误提示

![](assets/Pasted%20image%2020240729105920.png)

根据提示说少了个 `on` ，这意味着你的 workflow YAML 文件中没有定义任何触发事件。我们仍需指定何时触发这个工作流。例如，可以设置在 push 事件或 pull request 事件时触发工作流。\


push 演示
```yaml

```