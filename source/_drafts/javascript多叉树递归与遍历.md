---
title: javascript多叉树递归与遍历
date: 2019-01-23 19:09:48
tags: javascript
categories: 前端
---


#### 递归遍历

```javascript
// 遍历单个节点
function traverseNode(node){
    var divObj = document.getElementById("app");
    divObj.innerHTML = divObj.innerHTML + "  " + node.name;
}

// 递归遍历树
// 作者：张超
function traverseTree(node){
    if (!node) {
        return;
    }

    traverseNode(node);
    if (node.children && node.children.length > 0) {
        var i = 0;
        for (i = 0; i < node.children.length; i++) {
            this.traverseTree(node.children[i]);
        }
    }
}

traverseTree(root);
```


#### 非递归遍历
````javascript
// 遍历单个节点
function traverseNode2(node){
    var divObj2 = document.getElementById("app2");
    divObj2.innerHTML = divObj2.innerHTML + "  " + node.name;
}

// 非递归遍历树
// 作者：张超
function traverseTree2(node){
    if (!node) {
        return;
    }

    var stack = [];
    stack.push(node);
    var tmpNode;
    while (stack.length > 0) {
        tmpNode = stack.pop();
        traverseNode2(tmpNode);
        if (tmpNode.children && tmpNode.children.length > 0) {
            var i = tmpNode.children.length - 1;
            for (i = tmpNode.children.length - 1; i >= 0; i--) {
                stack.push(tmpNode.children[i]);
            }
        }
    }
}

traverseTree2(root);
````


来源：[Javascript多叉树的递归遍历和非递归遍历](https://blog.csdn.net/zhangchao19890805/article/details/71157843)

