# 数据结构

- 单链表
```
class LinKedList {
    constructor(value){
        this.head = new Node(value);
    }
}
class Node {
    constructor(value, nextValue){
        this.value = value;
        this.next = next;
    }
}
```
- 查找节点
    - 第一次查找：
![alt text](image-1.png) 
第二次查找：
![alt text](image-2.png)
```
function findNode(value) {
    let currentNode = this.head
    while(currentNode !== value){
        currentNode = currentNode.next
    }
    if(!currentNode) return null
    return currentNode
}
```

- 指定位置（后面）插入节点
![alt text](image-3.png)
插入成功
![alt text](image.png)
```
    <!-- 
    1. 创建新节点；2. 找到指定节点；
    3. 节点连接(把新节点的next与指定节点后面一个节点连接，然后把指定节点的next指向新节点) -->
    <!-- 1 2 3 4 insertAfter(2, 5) => 1 2 5 3 4-->
    function insertAfter(value newValue){
        let newNode = new Node(newValue);
        let currenNode = this.findNode(value);
        newNode.next = currenNode.next;
        currenNode.next = newNode;
    }
```