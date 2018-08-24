### ngx_table_elt_t 结构

```C
typedef struct {
    ngx_uint_t        hash;			// 哈希值
    ngx_str_t         key;			// key
    ngx_str_t         value;		// value
    u_char           *lowcase_key;	// 指向全部小写的key字符串
} ngx_table_elt_t;

```

###  为HTTP头部量身定做

