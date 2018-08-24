### ngx_array_t 结构

```C
typedef struct {
    void        *elts;		// 指向数组的首地址
    ngx_uint_t   nelts;		// 数组中已经使用的元素个数
    size_t       size;		// 每个元素占用的内存大小
    ngx_uint_t   nalloc;	// 当前数组能容纳元素个数
    ngx_pool_t  *pool;		// 内存池对象
} ngx_array_t;
```

### 使用方法

```C
// 创建一个动态数组，预分配n个大小为size的内存空间
ngx_array_t *ngx_array_create(ngx_pool_t *p, ngx_uint_t n, size_t size);

// 初始化一个已经存在的动态数组，并预分配n个大小为size的内存空间
static ngx_inline ngx_int_t
ngx_array_init(ngx_array_t *array, ngx_pool_t *pool, ngx_uint_t n, size_t size);

// 销毁一个已经分配的数组元素空间和ngx_array_t动态数组对象
void ngx_array_destroy(ngx_array_t *a);

// 向当前数组中添加一个元素，返回这个新添加元素的地址 - 如果达到上限之后会自动扩容
void *ngx_array_push(ngx_array_t *a);

// 向当前数组中添加n个元素，返回的是新添加的这批元素中第一个元素的地址
void *ngx_array_push_n(ngx_array_t *a, ngx_uint_t n);
```

