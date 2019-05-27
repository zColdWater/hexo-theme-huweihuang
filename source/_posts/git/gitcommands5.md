---
title: "[Git] Diff"
catalog: true
toc_nav_num: true
date: 2019-05-27 20:20:30
subtitle: "如何使用Diff在使用git的过程中"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/man_smoking.jpg"
tags:
- Git
catagories:
- Git
---

> 当前环境: git version 2.16.3

先赋一张图:  

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/example-diff.jpg" height="150" />


查看未被add的diff
=======
`git diff`

```
// 例如执行后输出
diff --git a/myfile b/myfile
index 190a180..a5855d1 100644
--- a/myfile
+++ b/myfile
@@ -1 +1,3 @@
 123        // 这行是之前的
+
+更新了不少   // 这行是新加的
```

查看未被add的diff
=======
`git diff --staged`
```
// 例如执行后输出
diff --git a/myfile b/myfile
index 190a180..a5855d1 100644
--- a/myfile
+++ b/myfile
@@ -1 +1,3 @@
 123       // 这行是之前的
+
+更新了不少  // 这行是新加的
```

查看两个分支的diff/或者查看两个commit的diff
=======
`git diff master..develop` // 比较分支例子  
`git diff 9dac73bd9dcc9eaed2507bfaefff3e8fdefa4153..c9a3536351b43ba9871319c12fea8ff1925889b1` // 比较两个节点例子  
`git diff mybranch..master -- myfile` // 比较两个分支下myfile文件差异  
`git diff 9dac73bd9dcc9eaed2507bfaefff3e8fdefa4153..c9a3536351b43ba9871319c12fea8ff1925889b1 -- myfile` // 比较两个commit下myfile文件差异


查看某文件的所有改动历史
=======
`git log -p myfile.rb` // 查看 myfile.rb 的所有更改历史（虽然这跟git diff命令没关系，但是和查看更改有关系我们就一块写在这里了）




总结
=======
上面我们列举了，查看未添加的diff，查看未提交的diff，查看两个分支下面的diff，查看两个commit下面的diff，查看两个分支或者两个commit的某一个文件的diff，查看某一个文件的所有更改历史，这里包含了开发常用的功能即可。


