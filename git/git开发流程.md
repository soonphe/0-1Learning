# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## 开发流程规则
开发模型严谨，自洽，有code review，commit语义，合并rebase，整个开发历史干净清晰。

### git开发流程
1. 根据开发任务，建立git分支, 分支名称模式为feature/任务名，比如关于API相关的一项任务，建立分支feature
git checkout -b feature

2. 运行git branch 确认切换到了feature/api分支

3. 编辑代码完成开发任务， commit相关代码
git add -A
git commit -m "implement api architecture"

4. 将分支代码push到仓库分支
git push origin -u feature

5. 合并分支，登录到github/gitlab的源代码库，创建merge request/pull request按钮去创建一个合并请求.
选择Source branch源分支，Target branch目标分支，填写相关内容：Title标题、Description说明、Assignee/reviewer...

6. 提醒reviewer去审核merge/pull request，系统也会发邮件提醒reviewer.
Reviewer打开merge/pull request页面，查看代码修改情况，也可以在相应的代码处添加注视，提示代码作者哪里应该修正。

7. 代码作者根据reviewer的要求，调整代码后commit／push到服务器。 然后reviewer继续设置， 如此循环，直到没有问题。

8. 当代码没有问题以后， 需要将分支代码merge到主代码库， 有两种方法：
- Reviewer可以在merge/pull request页面点击Merge按钮， 把代码merge到主代码库
- 代码作者自己本地merge到主分支， 并push到服务器。

如果看到master里有修改没在当前分支， 那么运行git rebase master来把master的修改加入到当前分支
运行一下合并命令
git checkout master
git merge --no-ff feature
git push

9. 代码作者删除feature子分支。
git checkout master
git branch -D feature
git push origin :feature
git pull origin master #从主分支pull到子分支



### code review
git 是分布式代码版本管理系统
gerrit是建立在git基础上，基于web的代码审核工具。

### Git常用的commit规范用语
feat: 新增 feature
fix: 修复 bug
docs: 仅仅修改了文档，比如 README, CHANGELOG, CONTRIBUTE等等
style: 仅仅修改了空格、格式缩进、逗号等等，不改变代码逻辑
refactor: 代码重构，没有加新功能或者修复 bug
perf: 优化相关，比如提升性能、体验
test: 测试用例，包括单元测试、集成测试等
chore: 改变构建流程、或者增加依赖库、工具等
revert: 回滚到上一个版本
