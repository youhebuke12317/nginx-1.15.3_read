### ngx_radix_tree_t 结构

```C
#define NGX_RADIX_NO_VALUE   (uintptr_t) -1

typedef struct ngx_radix_node_s  ngx_radix_node_t;
 
struct ngx_radix_node_s {
    ngx_radix_node_t  *right;
    ngx_radix_node_t  *left;
    ngx_radix_node_t  *parent;
    uintptr_t          value;	// 用户自定义结构， 如果未使用， value的值将是NGX_RADIX_NO_VALUE
};
 
 
typedef struct {
    ngx_radix_node_t  *root;	// 指向根节点
    ngx_pool_t        *pool;	// 内存池对象，负责给基数树的节点分配内存
    ngx_radix_node_t  *free;	// 管理员已经分配但未使用的节点，实际上是所有不在树中节点的单链表
    char              *start;	// 已分配内存中还未使用内存的首地址
    size_t             size;	// 已分配内存中还未使用的内存大小
} ngx_radix_tree_t;

```

### 方法

```C
// 创建一颗基数树
ngx_radix_tree_t *ngx_radix_tree_create(ngx_pool_t *pool,
    ngx_int_t preallocate);
 
// 向基数树中添加一个节点
// 成功返回NGX_OK，内存池无法分配内存返回NGX_ERROR，掩码设置错误返回NGX_BUSY
ngx_int_t ngx_radix32tree_insert(ngx_radix_tree_t *tree,
    uint32_t key, uint32_t mask, uintptr_t value);

// 从基数树中删除一个节点
// 成功返回NGX_OK，失败返回NGX_ERROR
ngx_int_t ngx_radix32tree_delete(ngx_radix_tree_t *tree,
    uint32_t key, uint32_t mask);

// 在基数树中查找一个节点
// 对于uintptr_t类型的指针地址，可以强制转化为实际数据结构的指针使用
// 如果没有查询到，返回NGX_RADIX_NO_VALUE
uintptr_t ngx_radix32tree_find(ngx_radix_tree_t *tree, uint32_t key);

```

