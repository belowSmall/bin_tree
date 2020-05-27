## 二叉树定义
在计算机科学中，二叉树是每个结点最多有两个子树的树结构。通常子树被称作“左子树”（left subtree）和“右子树”（right subtree）。二叉树常被用于实现二叉查找树和二叉堆。
一棵深度为k，且有2^k-1个结点的二叉树，称为满二叉树。这种树的特点是每一层上的结点数都是最大结点数。而在一棵二叉树中，除最后一层外，若其余层都是满的，并且或者最后一层是满的，或者是在右边缺少连续若干结点，则此二叉树为完全二叉树。具有n个结点的完全二叉树的深度为floor(log2n)+1。深度为k的完全二叉树，至少有2k-1个叶子结点，至多有2k-1个结点。（转自百度百科）



#### 1.二叉树节点
* 宏定义数据结构入口。这种定义方式可以很方便地修改数据结构
在结构体_bstree_node里，使用BSTREE_ENTRY定义入口的数据结构
* 为什么要这样写？**将业务与数据结构分离**
```c
// 宏定义数据结构的入口
#define BSTREE_ENTRY(name, type)    \
    struct name {                   \
        struct type *left;          \
        struct type *right;         \
	}

struct bstree_node {
	KEY_VALUE data;                    // 宏定义数据类型，可以是自定义的数据结构
	BSTREE_ENTRY(, _bstree_node) bst;  // 树节点定义
};

struct bstree {               // 树定义，包含一个根节点
	struct bstree_node *root;
};
```

#### 2.创建一个节点
malloc分配内存，将参数key设为节点的值，并将左右子树置空。
```c
struct bstree_node *bstree_create_node(KEY_VALUE key) {
	struct bstree_node *node = (struct bstree_node*)malloc(sizeof(struct bstree_node));
	if (node == NULL) {
		assert(0);
	}
	node->data = key;
	node->bst.left = node->bst.right = NULL;

	return node;
}
```

#### 3.往树中插入节点
先找到插入的父节点，后再判断插入到左还是右。
```c
int bstree_insert(struct bstree *T, int key) {

	assert(T != NULL);

	if (T->root == NULL) {
		T->root = bstree_create_node(key); // 创建一个节点 以key为值
		return 0;
	}

	struct bstree_node *node = T->root; // 待插入位置的父节点
	struct bstree_node *tmp = T->root;  // 待插入位置

	while (node != NULL) {
		tmp = node;
		if (key < node->data) {
			node = node->bst.left;
		} else {
			node = node->bst.right;
		}
	}

	if (key < tmp->data) { // 找到插入左还是右
		tmp->bst.left = bstree_create_node(key);
	} else {
		tmp->bst.right = bstree_create_node(key);
	}
	
	return 0;
}
```

#### 4.中序遍历
```c
int bstree_traversal(struct bstree_node *node) {
	if (node == NULL) return 0;
	
	bstree_traversal(node->bst.left);
	printf("%4d ", node->data);
	bstree_traversal(node->bst.right);
}
```

#### 5.完整代码
```c
#include <stdio.h>
#include <stdlib.h>
#include <assert.h>

#define ARRAY_LENGTH 20

typedef int KEY_VALUE;

#define BSTREE_ENTRY(name, type) 	\
	struct name {					\
		struct type *left;			\
		struct type *right;			\
	}

struct bstree_node {
	KEY_VALUE data;
	BSTREE_ENTRY(, bstree_node) bst;
};

struct bstree {
	struct bstree_node *root;
};

struct bstree_node *bstree_create_node(KEY_VALUE key) {
	struct bstree_node *node = (struct bstree_node*)malloc(sizeof(struct bstree_node));
	if (node == NULL) {
		assert(0);
	}
	node->data = key;
	node->bst.left = node->bst.right = NULL;

	return node;
}

int bstree_insert(struct bstree *T, int key) {
	assert(T != NULL);

	if (T->root == NULL) {
		T->root = bstree_create_node(key); // 创建一个节点 以key为值
		return 0;
	}

	struct bstree_node *node = T->root; // 待插入位置的父节点
	struct bstree_node *tmp = T->root;  // 待插入位置

	while (node != NULL) {
		tmp = node;
		if (key < node->data) {
			node = node->bst.left;
		} else {
			node = node->bst.right;
		}
	}
	if (key < tmp->data) { // 找到插入左还是右
		tmp->bst.left = bstree_create_node(key);
	} else {
		tmp->bst.right = bstree_create_node(key);
	}
	return 0;
}

int bstree_traversal(struct bstree_node *node) {
	if (node == NULL) return 0;
	
	bstree_traversal(node->bst.left);
	printf("%4d ", node->data);
	bstree_traversal(node->bst.right);
}

int main() {
	int keyArray[ARRAY_LENGTH] = {24,25,13,35,23, 26,67,47,38,98, 20,13,17,49,12, 21,9,18,14,15};

	struct bstree T = {0};
	int i = 0;
	for (i = 0;i < ARRAY_LENGTH;i ++) {
		bstree_insert(&T, keyArray[i]);
	}

	bstree_traversal(T.root);
	printf("\n");

	return 0;
}
```

---
2020.5.27  16:21  深圳
