### ngx_rbtree_t 结构

```C
typedef ngx_uint_t  ngx_rbtree_key_t;
typedef ngx_int_t   ngx_rbtree_key_int_t;
 
typedef struct ngx_rbtree_node_s  ngx_rbtree_node_t;
 
struct ngx_rbtree_node_s {
    ngx_rbtree_key_t       key;		// 无符号整型的关键字
    ngx_rbtree_node_t     *left;	// 左子节点
    ngx_rbtree_node_t     *right;	// 右子节点
    ngx_rbtree_node_t     *parent;	// 父节点
    u_char                 color;	// 节点颜色，0表示黑色，1表示红色
    u_char                 data;	// 一字节的节点数据
};
 
 
typedef struct ngx_rbtree_s  ngx_rbtree_t;
 
// 为了解决不同节点含有相同关键字的元素冲突问题，可以灵活的添加冲突元素
typedef void (*ngx_rbtree_insert_pt) (ngx_rbtree_node_t *root,
    ngx_rbtree_node_t *node, 
    ngx_rbtree_node_t *sentinel);
 
struct ngx_rbtree_s {
    ngx_rbtree_node_t     *root;		// 指向红黑树的根节点
    ngx_rbtree_node_t     *sentinel;	// 指向NIL哨兵节点
    ngx_rbtree_insert_pt   insert;		// 表示红黑树添加元素的函数指针，它决定在添加新节点时的行为是替换还是新增
};

```

### 使用方法

```C
// 初始化红黑树
ngx_rbtree_init(tree, s, i);

// 向红黑树中添加节点
void ngx_rbtree_insert(ngx_rbtree_t *tree, ngx_rbtree_node_t *node);

// 从红黑树中删除节点
void ngx_rbtree_delete(ngx_rbtree_t *tree, ngx_rbtree_node_t *node);

// 向红黑树中添加数据节点，每个数据节点的关键字都是唯一的，不存在同一个关键字有多个节点的问题
void ngx_rbtree_insert_value(ngx_rbtree_node_t *root, ngx_rbtree_node_t *node,
    ngx_rbtree_node_t *sentinel);

// 向红黑树中添加数据节点，每个数据节点的关键字表示时间或者时间差
void ngx_rbtree_insert_timer_value(ngx_rbtree_node_t *root,
    ngx_rbtree_node_t *node, ngx_rbtree_node_t *sentinel);

// 向红黑树中添加数据节点，每个数据节点的关键字可以不唯一，但他们是以字符串作为唯一标识，存放在ngx_str_node_t结构体的str成员中
ngx_rbtree_node_t *ngx_rbtree_next(ngx_rbtree_t *tree,
    ngx_rbtree_node_t *node);

// 设置node节点颜色为红色
#define ngx_rbt_red(node)               ((node)->color = 1)

// 设置node节点颜色为黑色
#define ngx_rbt_black(node)             ((node)->color = 0)

// 判断节点颜色，0表示红色，非0表示黑色
#define ngx_rbt_is_red(node)            ((node)->color)

// 判断节点颜色，0表示黑色，非0表示红色
#define ngx_rbt_is_black(node)          (!ngx_rbt_is_red(node))

// 将n2节点颜色复制到n1节点
#define ngx_rbt_copy_color(n1, n2)      (n1->color = n2->color)

// 初始化哨兵节点，实际上就是将该节点颜色设置为黑色
#define ngx_rbtree_sentinel_init(node)  ngx_rbt_black(node)

// 找到当前节点及其子树中最小节点（按照key关键字）
static ngx_inline ngx_rbtree_node_t *
ngx_rbtree_min(ngx_rbtree_node_t *node, ngx_rbtree_node_t *sentinel)

```

