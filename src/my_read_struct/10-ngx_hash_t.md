## 散列/哈希函数

```C
// ngx_hash_t 的散列方法 - 函数指针 - 用户自定义
typedef ngx_uint_t (*ngx_hash_key_pt) (u_char *data, size_t len);

// NGX提供了两种散列方法函数
ngx_uint_t ngx_hash_key(u_char *data, size_t len); 	// 使用BKDR算法将任意长度的字符串映射成整型
ngx_uint_t ngx_hash_key_lc(u_char *data, size_t len); // 将字符串小写后，再使用BKDR算法
```

## 基本散列表/完全散列

### ngx_hash_t 结构

```C
typedef struct {
    void             *value;	// 指向用户自定义元素数据的指针，若当前槽位NULL，则value为0
    u_short           len;		// 元素关键字的长度
    u_char            name[1];	// 元素关键字的首地址
} ngx_hash_elt_t;
 
 
typedef struct {     
    ngx_hash_elt_t  **buckets;	// 指向散列表的首地址，意识第一个槽的首地址
    ngx_uint_t        size;		// 散列表中槽的总数
} ngx_hash_t;

```

### ngx_hash_t 接口

```C
/* ngx_hash_t提供的方法 */
// 用于在hash表中查找元素
// 成功返回所指向的用户数据，失败返回NULL
void *ngx_hash_find(ngx_hash_t *hash, ngx_uint_t key, u_char *name, size_t len);
```

## 支持通配符的散列表

### ngx_hash_wildcard_t 结构

```C
typedef struct {
    ngx_hash_t        hash;		// 基本散列表
    void             *value;	// 可以使用这个指向用户数据
} ngx_hash_wildcard_t;

```

### ngx_hash_wildcard_t 接口

```C
/* ngx_hash_wildcard_t提供的方法 */
// 将带查找的关键字name转化为前置散列表规则下的字符串在递归查询
// 成功返回所指向的用户数据，失败返回NULL
void *ngx_hash_find_wc_head(ngx_hash_wildcard_t *hwc, u_char *name, size_t len);
// 将带查找的关键字name转化为后置散列表规则下的字符串在递归查询
// 成功返回所指向的用户数据，失败返回NULL
void *ngx_hash_find_wc_tail(ngx_hash_wildcard_t *hwc, u_char *name, size_t len);

```

### ngx_hash_combined_t 结构

```C
typedef struct {
    ngx_hash_t            hash;		// 用于精确匹配的基本散列表
    ngx_hash_wildcard_t  *wc_head;	// 用于查询前置通配符的散列表
    ngx_hash_wildcard_t  *wc_tail;	// 用于查询后置通配符的散列表
} ngx_hash_combined_t;

```

### ngx_hash_combined_t 接口

```C
/* ngx_hash_combined_t提供的方法 */
// 按照如下顺序查找字符串 ： 完全匹配 -> 前置散列查询 -> 后置散列查询
// 成功返回所指向的用户数据，失败返回NULL
void *ngx_hash_find_combined(ngx_hash_combined_t *hash, ngx_uint_t key,
    u_char *name, size_t len);

```



## 散列表初始化接口

```C
/* ngx_hash_init_t只用于初始化散列表 */
typedef struct {
    ngx_hash_t       *hash;			// 指向普通的完全匹配散列表
    ngx_hash_key_pt   key;			// 用于初始化预添加元素的散列方法
     
    ngx_uint_t        max_size;		// 散列表中槽的最大数目
    ngx_uint_t        bucket_size;	// 散列表中一个槽的空间大小，限制了散列元素关键字的最大长度
     
    char             *name;			// 散列表名称
    ngx_pool_t       *pool;			// 内存池，分配散列表 - 最多3个，普通 前置 后置
    ngx_pool_t       *temp_pool;	// 临时内存池，仅存在于初始化散列表之前
} ngx_hash_init_t;

/* ngx_hash_init_t提供两种初始化方法 */
// 初始化基本散列表
ngx_int_t ngx_hash_init(ngx_hash_init_t *hinit, ngx_hash_key_t *names,
    ngx_uint_t nelts);
// 初始化通配符散列表
ngx_int_t ngx_hash_wildcard_init(ngx_hash_init_t *hinit, ngx_hash_key_t *names,
    ngx_uint_t nelts);

```

## 散列表初始化前必读

```C
typedef struct {
    ngx_str_t         key;		// 关键字元素
    ngx_uint_t        key_hash;	// 由散列函数算出来的关键码
    void             *value;	// 指向实际的用户数据
} ngx_hash_key_t;

typedef struct {
    ngx_uint_t        hsize;	// 散列表槽的个数
 
    ngx_pool_t       *pool;		// 内存池，用于分配永久性内存，到目前版本，该pool未使用
    ngx_pool_t       *temp_pool;		// 临时内存池，下面动态数组由该内存池分配
 
    // 以ngx_hash_key_t结构保存不含统配符关键字的元素
    ngx_array_t       keys;				
    ngx_array_t      *keys_hash;		// 
 
    // 以ngx_hash_key_t结构保存含前置统配符关键字的元素生成的中间关键字
    ngx_array_t       dns_wc_head;		
    ngx_array_t      *dns_wc_head_hash;	//
 
    // 以ngx_hash_key_t结构保存含后置统配符关键字的元素生成的中间关键字
    ngx_array_t       dns_wc_tail;		
    ngx_array_t      *dns_wc_tail_hash;	// 
} ngx_hash_keys_arrays_t;

/* 
 * @detail 	初始化ngx_hash_keys_arrays_t结构体数组 
 * @param 	ha
 * @param 	type
 * @return	
 * */
ngx_int_t ngx_hash_keys_array_init(ngx_hash_keys_arrays_t *ha, ngx_uint_t type);

/* 
 * @detail 	向ha中添加一个元素 
 * @param 	ha
 * @param 	key
 * @param 	value
 * @param 	flags
 * @return	成功返回NGX_OK，失败返回NGX_ERROR
 * */
ngx_int_t ngx_hash_add_key(ngx_hash_keys_arrays_t *ha, ngx_str_t *key,
    void *value, ngx_uint_t flags);

```

