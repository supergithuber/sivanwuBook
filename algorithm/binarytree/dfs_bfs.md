# 深度广度搜索

#### 深度优先搜索

```c++
//深度优先遍历(深栈右)
void depthFirstSearch(Tree root){
    stack<Node *> nodeStack;  //使用C++的STL标准模板库
    nodeStack.push(root);
    Node *node;
    while(!nodeStack.empty()){
        node = nodeStack.top();
        printf(format, node->data);  //遍历根结点
        nodeStack.pop();
        if(node->rchild){
            nodeStack.push(node->rchild);  //先将右子树压栈
        }
        if(node->lchild){
            nodeStack.push(node->lchild);  //再将左子树压栈
        }
    }
}
```



#### 广度优先搜索

```c++
//广度优先遍历（广堆左）
void breadthFirstSearch(Tree root){
    queue<Node *> nodeQueue;  //使用C++的STL标准模板库
    nodeQueue.push(root);
    Node *node;
    while(!nodeQueue.empty()){
        node = nodeQueue.front();
        nodeQueue.pop();
        printf(format, node->data);
        if(node->lchild){
            nodeQueue.push(node->lchild);  //先将左子树入队
        }
        if(node->rchild){
            nodeQueue.push(node->rchild);  //再将右子树入队
        }
    }
}
```

