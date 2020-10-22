### 1、Git常用命令

[Git使用介绍](http://datadoc.kingdee.com/web/#/p/6fd58515f13ad2243fbf19ce8120cad5 "Git使用介绍")
[Git使用规范](http://datadoc.kingdee.com/web/#/p/85516f01c31c5559d601498251aa7af4 "Git使用规范")

#### 1.1、Git常用命令对比与使用标准

**① 将远程代码拉到本地（非合并）**

```
git fetch;
```

**② 合并代码（需要先通过fetch拉到本地，不然本地不知道远程仓库的情况）**
```
git rebase; // 指将当前分支远程的合并到工作目录
git rebase fb_20200526_dev; // 将fb_20200526_dev分支合并到当前分支
git merge; // 指将当前分支远程的合并到工作目录
git merge fb_20200526_dev; // 将fb_20200526_dev分支合并到当前分支
git pull; // 相当于同时执行git fetch；git merge; 一般情况下禁止使用！
```
`总结：小组开发时，同分支更新代码采用rebase，不同分支代码合并采用merge`

![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-26/5eccce19735da.png)
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-27/5ece18235263f.png)
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-26/5eccd0bb51aa0.png)
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-26/5eccd1270b9f8.png)

**③ 将文件交于Git管理**

```
git add xxx.txt; // 将xxx.txt文件交于git做版本管理
git add .; // 将当前目录下的所有文件交于git管理
```

**④ 提交新的commit**

```
git commit -m "提交描述"; // 其他详细参数请查阅git --help
```

**⑤ 将commit推到仓库**

```
git push;
```

**⑥ 打tag**

```
git tag tag_20200526_xxx; // 在基于当前commit打一个tag
git push origin tag_20200526_xxx; // 将当前tag推送到远程仓库
```

**⑦ 切分支**

```
git checkout develop; // 切换到新develop分支
git checkout -b hotfix_20200526_fixBug; // 基于当前commit切出一个名为hotfix_20200526_fixBug的分支
git push origin --set-upstream hotfix_20200526_fixBug; // 将hotfix_20200526_fixBug分支推送到仓库并与本地的hotfix_20200526_fixBug分支关联
```

**⑧ 暂存**

```
git stash; // 将本地修改暂存到栈
git stash pop; // 将栈中暂存的内容弹出
git stash list; // 查看栈中有哪些暂存
```

**⑨ 分支管理**
```
git branch -a; // 查看仓库所有的分支
```


#### 1.2、最佳实践

**① 更新远程代码到本地**
场景1：本地没有未push的commit，本地无代码修改
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-26/5eccc5ca39026.png)
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-26/5eccbfc541492.png)
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-26/5eccc0493d3b8.png)
```
git fetch;
git rebase;
```

场景2：本地没有未push的commit，本地有代码修改
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-26/5eccc67c596b3.png)
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-26/5eccc14eaea0e.png)
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-26/5eccc24053bd1.png)
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-26/5eccc2cf3030f.png)
```
git stash;
git fetch;
git rebase;
git stash pop;
```

场景3：本地有未push的commit，本地无代码修改
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-26/5eccc87af1d08.png)
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-26/5eccc8c947fb9.png)
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-26/5eccc9591cd4d.png)
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-26/5eccc9826bc63.png)

```
git fetch;
git rebase;
```

场景4：本地有未push的commit，本地有代码修改(大家可以按照上面的套路，自行尝试)
```
git stash;
git fetch;
git rebase;
git stash pop
```

**② 正常情况下的全流程介绍**

```
git tag; // 找到最新的tag
git stash; // 暂存本地修改（看实际情况可选）
git checkout tag_xxx; // 切到最新tag，实际上就是一个commit
gitk --all; // 看一下图是否head已经指向远程的最新tag，以及与远程的master关系。
git checkout -b hotfix_20200526_bugfix; // 基于tag切一个bug修复分支
git push origin --set-upstream hotfix_20200526_bugfix; // 将分支推到仓库并与本地关联
... 改代码
git checkout test;
git fetch;git rebase; // 切到test分支并更新
gitk --all; // 核对更新结果
git merge hotfix_20200526_bugfix; // 将bug修复分支合并到test分支测试（不同分支合并采用merge）
gitk --all; // 核对合并结果
git push;
... 测试完毕发布生产环境
git checkout master;
git fetch;git rebase; // 切到master分支并更新
gitk --all; // 查看master分支是否为最新，是否打了tag
git merge hotfix_20200526_bugfix; // 站在master分支上合并修复分支
gitk --all; // 查看合并结果是否正确
git push;
... 发布更新并验证通过
git tag tag_20200526_xxxx;
git push origin tag_20200526_xxxx; // 将tag推仓库
```

#### 1.3、日常学习方式

① 除非你对Git很熟悉，不然少用ide的Git相关按钮！即便要用也看清楚相关选项，别用默认的，更新默认的就是使用merge！
注：使用ide集成的Git更新代码的时候没注意观察ide的进度，你会有惊喜的发现。其实他的做法就是
```
git fetch;
git stash;
git rebase;
git stash pop;
```
有兴趣的同学可以自行观察！

② 善于使用git bash，通过指令操作Git！
- 好处1：能够熟悉Git相关操作，熟悉Git执行流程
- 好处2：在linux环境中只能通过指令操作
- 好处3：在win平台上git bash就是一个简单的linux环境，能够用常用的工具，例如curl、vim等，同时支持linux shell脚本执行。

③ 如遇忘记指令等情况，或者不知道参数如何写
- 推荐通过git -help
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-26/5eccd6e081534.png)
- 其次再是百度，注意百度来的指令请自行验证，不要污染了仓库

④ 善用gitk --all指令
- 查看commit、分支关系
- 分析代码是否合并正确

注：建议使用ide操作Git的场景
<font color='red'>

- 提交代码：因为需要选择提交文件，提交内容。git bash操作不便
- 解决冲突：git bash容易出错，不直观
</font>

### 2、KEBC仓库拆分

#### 2.1、当前KEBC仓库情况
**仓库：**所有模块代码全部放在一个Git仓库中管理，不同的业务采用不同的目录划分，他们之间没有任何关系！
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-27/5ece00fc566e3.png)

**分支管理：**各种业务分支都存在，有线索、伙伴、健康打卡、产品等等，他们之间没有任何的交集，完全独立，甚至能独立部署运行！
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-27/5ece028be317e.png)

**提交管理：**
① 更新合并方式不统一：很多同学采用merge方式来合并同分支更新！
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-27/5ece059161288.png)

② 多业务开发团队同时并频繁操作同一个分支，导致commit关系图错综复杂！
图例：合并到develop分支
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-27/5ece0669ec536.png)
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-27/5ece0704d27f5.png)

**版本管理：**暂时还无tag，如果要对发布的稳定版本打tag，只能对整个大工程打tag，tag无法细分，例如prm发版想对prm单独打一个tag，无法实现。各个开发小组上线的版本tag会全部混在一起。

#### 2.2、KEBC仓库拆分后目标

**仓库：**相关联的内容放在一个仓库中，例如prm仓库，里面就维护prm的内容，也很少会出现多个团队同时对同一仓库内容进行开发。

**分支管理：**例如prm开发小组基于prm仓库进行开发，也有自己的develop、test、master分支。
容易：基于小组来管理Git。
困难：基于大部门，甚至是多部门的开发同事。

**提交管理：**
① 统一小组规则，具体参照部门的Git使用规则。由小组长监督！
② 合并develop或者test分支由小组长统一合并！
这样可以大幅缓解Git管理上的困难，图请不清晰好不好看直接取决于小组长有没有监督好组员。

**版本管理：**每个仓库（模块）单独进行版本管理，单独打tag。如果发布线上出现问题，也只知道还原这个模块即可，其他模块无需还原！

#### 2.3、仓库拆分规则

| 模块名称                                     | 模块缩写  | 备注             |
| -------------------------------------------- | --------- | ---------------- |
| 客户关系管理                                 | crm       | 对应现有kebc/crm |
| 客户订单管理                                 | cso       | 对应现有kebc/cso |
| 内部订单管理                                 | sal       | 暂无             |
| 项目交付管理                                 | pms       | 暂无             |
| 伙伴关系管理                                 | prm       | 对应现有kebc/prm |
| 采购管理                                     | pur       | 暂无             |
| 人力资源                                     | hr        | 对应现有hr       |
| 财务类                                       | fi        | 对应现有fi       |
| it服务类应用                                 | its       | 对应现有its      |
| 数据服务                                     | data      | 暂无             |
| 基础资料（产品、客户、定价、物料、伙伴等等） | basicdata | 暂无             |
| 基础服务（基础服务、登录、附件等等）         | common    | 对应现有base     |

#### 2.4、拆分后变化

**如下以prm示例**
**clone工程：**

```
git clone git@rdgit.kingdee.com:it-bd/prm.git; // 将prm工程拉下来
cd prm; // 进入到prm工程
git submodule init; // 初始化工程子模块，例如prm依赖了base，则会将base作为子模块拉下来
git submodule update; // 将依赖的子模块更新到最新（这里的最新看远程仓库指向子模块的哪个commit）
```

**本地目录结构示例：**
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-27/5ece0f60bca22.png)
![](http://datadoc.kingdee.com/server/../Public/Uploads/2020-05-27/5ece0fd248ade.png)

#### 2.5、拆分后的注意事项以及问题

**注意事项：**
① 因为采用了子模块的概念，而git是基于commit来管理的，说白了就是主工程所依赖的子模块可以是任意的一个commit。假如说base模块的的最新tag是tag2，但是prm当前还是指向base的上一个tag1。则prm开发负责人需要在prm下次发版的时候将依赖的子模块指向tag2，保证及时发现问题并暴露，使用最新的稳定版本子模块。这样势必会导致操作的复杂与繁琐，但是为了管理的准确与清晰，这点代价是值得的！

② 假如A模块依赖B模块。B模块今天晚上7点发版，然后发版验证成功并打了tag，8点钟生产环境爆出问题。而恰巧A模块也在今天发版，发版的时候用了最新的tag，导致A模块发布失败！对于这种情况需要B维护团队通知到所有依赖方，在最新版本B模块基础上做评估与测试，以保证不会影响依赖方。而不是闷声发大财，啥也不管，发了再说！
因为假如B模块更新了，但是A模块还是指向了B模块的上一个tag，本地开发环境是无法发现任何异常的。当发版的时候强制使用B模块的最新tag构建，就会暴露出问题，但是那个时候暴露出问题就晚了。

<font color='red'>
注：多仓库依赖采用git submodule实现
拆分测试完毕后会提供一份详细的操作文档，详询伟大的架构师！
</font>
[官网文档连接](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97 "官网文档连接")