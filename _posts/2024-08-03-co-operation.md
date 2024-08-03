---
layout: post
title: 协同设计
categories: [设计模式]
description: 协同设计应用开发
keywords: 设计模式
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
# 资料参考
https://js.design/special/article/collaborative-design.html
https://godbasin.github.io/2020/10/10/why-spreadsheet-app-excited/

# 协作应用
- 文档类： 谷歌、腾讯文档等
- 设计类： Figma

# 技术方案及问题
- 冲突处理
  - 编辑锁：当有人在编辑某个文档时，系统会将这个文档锁定，避免其他人同时编辑。
  - diff-patch：基于 Git 等版本管理类似的思想，对内容进行差异对比、合并等操作，包括GNU diff-patch、Myer’s diff-patch 等方案。
  - 最终一致性实现：包括 Operational Transformation（OT）、 Conflict-free replicated data type(CRDT，称为无冲突可复制数据类型)。
  - 
编辑锁的实现方式简单粗暴，但会直接影响用户体验。diff-patch 可以对冲突进行自助合并，也可以在冲突出现时交给用户处理。OT 算法是 Google Docs 中所采用的方案，Atom 编辑器使用的则是 CRDT。

- 权限管理
