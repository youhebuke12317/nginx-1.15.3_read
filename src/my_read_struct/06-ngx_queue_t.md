### ngx_queue_t 结构

```C
typedef struct ngx_queue_s  ngx_queue_t;     
struct ngx_queue_s {
    ngx_queue_t  *prev;
    ngx_queue_t  *next;
};
```



### 支持的方法

```c++
/*
 * @deatil 宏定义 - 双向链表初始化
 * @param	q	链表容器ngx_queue_t结构体的指针
 * @return	将链表q初始化，这时会自动置为空链表
 * */
ngx_queue_init(q);

/*
 * @detail	宏定义 - 判断链表是否为NULL 
 * @param 	h	链表容器ngx_queue_t结构体的指针
 * @return	链表为空返回非0，不为空返回0
 * */
ngx_queue_empty(h);

/*
 * @detail 宏定义 - 插入元素到链表的头部
 * @param 	h	链表容器ngx_queue_t结构体的指针
 * @param 	x	插入元素结构体中的ngx_queue_t成员指针
 * @return	无
 * */
ngx_queue_insert_head(h, x);

/*
 * @detail 宏定义 - 插入元素到链表的尾部
 * @param 	h	链表容器ngx_queue_t结构体的指针
 * @param 	x	插入元素结构体中的ngx_queue_t成员指针
 * @return	无
 * */
ngx_queue_insert_tail(h, x);

/*
 * @detail 	宏定义 - 返回链表容器中第一个元素的ngx_queue_t结构体指针
 * @param 	h	链表容器ngx_queue_t结构体的指针
 * @return	返回链表容器中第一个元素的ngx_queue_t结构体指针
 * */
ngx_queue_head(h);

/*
 * @detail 	宏定义 - 返回链表容器中最后一个元素的ngx_queue_t结构体指针
 * @param 	h	链表容器ngx_queue_t结构体的指针
 * @return	返回链表容器中最后一个元素的ngx_queue_t结构体指针
 * */
ngx_queue_last(h);

/*
 * @detail 	宏定义 - 返回链表容器结构体指针
 * @param 	h	链表容器ngx_queue_t结构体的指针
 * @return	返回链表容器结构体指针
 * */
ngx_queue_sentinel(h);

/*
 * @detail 	宏定义 - 从容器中移除指定元素
 * @param 	x	删除元素结构体中的ngx_queue_t成员指针
 * @return	无
 * */
ngx_queue_remove(x);

/*
 * @detail 	宏定义 - 以q元素为界拆分链表
 * @param 	h	链表容器ngx_queue_t结构体的指针，h为链表容器
 * @param 	q	q为链表h中的一个元素，拆分之后的后一部分链表的首元素
 * @param 	h	拆分之后的后一部分链表
 * @return	无
 * */
ngx_queue_split(h, q, n);

/*
 * @detail 	宏定义 - 合并链表，将n链表添加到h链表末尾
 * @param 	h	链表容器ngx_queue_t结构体的指针
 * @param 	n	链表容器ngx_queue_t结构体的指针
 * @return	无
 * */
ngx_queue_add(h, n);

/*
 * @detail 	返回链表中心元素，链表中滴（n/2 + 1）个元素
 * @param 	queue 	链表容器ngx_queue_t结构体的指针
 * @return	返回链表中心元素，链表中滴（n/2 + 1）个元素
 * */
ngx_queue_t *ngx_queue_middle(ngx_queue_t *queue);

/*
 * @detail 	使用插入排序对链表进行排序
 * @param 	queue	链表容器ngx_queue_t结构体的指针
 * @return	cmp	函数指针：两个元素的比较方法，需要使用者自己实现，实现如果返回正数表示升序排序
 * @return 	无
 * */
void ngx_queue_sort(ngx_queue_t *queue,
    ngx_int_t (*cmp)(const ngx_queue_t *, const ngx_queue_t *));


/*
 * @detail 	返回q的下一个元素
 * */
ngx_queue_next(q);

/*
 * @detail 	返回q的前一个元素
 * */
ngx_queue_prev(q);

/*
 * @detail 	将指定元素x插入到q元素之后
 * */
ngx_queue_insert_after(q, x);

/*
 * @detail	返回q元素所属结构体的地址 
 * @param 	q	链表容器ngx_queue_t结构体的指针
 * @param	type	链表元素的结构体类型名称 - 该结构体必须包含ngx_queue_t类型的成员
 * @param	link	link是上面这个结构体中ngx_queue_t类型的成员名字
 * @return	返回q元素所属结构体的地址 
 * */
ngx_queue_data(q, type, link);
```

