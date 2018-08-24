```C
typedef intptr_t        ngx_int_t;       
typedef uintptr_t       ngx_uint_t;

typedef struct {
    size_t      len;    // 字符串的有效长度
    u_char     *data;	// 指向字符串的起始地址
} ngx_str_t;

```

