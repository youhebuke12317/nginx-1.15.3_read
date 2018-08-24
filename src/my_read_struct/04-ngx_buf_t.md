### ngx_buf_t 结构

```c
// ngx_thread_task_t
// ngx_event_aio_t

typedef void *            ngx_buf_tag_t;    
typedef struct ngx_buf_s  ngx_buf_t;

typedef int                      ngx_fd_t;      
typedef struct stat              ngx_file_info_t;
struct ngx_file_s {
    ngx_fd_t                   fd;
    ngx_str_t                  name;
    ngx_file_info_t            info;
 
    off_t                      offset;
    off_t                      sys_offset;
 
    ngx_log_t                 *log;
 
#if (NGX_THREADS || NGX_COMPAT)
    ngx_int_t                (*thread_handler)(ngx_thread_task_t *task,
                                               ngx_file_t *file);
    void                      *thread_ctx;
    ngx_thread_task_t         *thread_task;
#endif
 
#if (NGX_HAVE_FILE_AIO || NGX_COMPAT)
    ngx_event_aio_t           *aio;
#endif
 
    unsigned                   valid_info:1;
    unsigned                   directio:1;
};
 


struct ngx_buf_s {
    u_char          *pos;		// 当buf所指向的数据在内存里的时候，pos指向的是这段数据开始的位置
    u_char          *last;		// 当buf所指向的数据在内存里的时候，last指向的是这段数据结束的位置
    off_t            file_pos;	// 当buf所指向的数据是在文件里的时候，file_pos指向的是这段数据的开始位置在文件中的偏移量
    off_t            file_last;	// 当buf所指向的数据是在文件里的时候，file_last指向的是这段数据的结束位置在文件中的偏移量
 
    u_char          *start;         /* start of buffer */
    u_char          *end;           /* end of buffer */
    ngx_buf_tag_t    tag;		   // 使用者可以关联任意的对象上去，只要对使用者有意义
    ngx_file_t      *file;		   // 当buf所包含的内容在文件中时，file字段指向对应的文件对象
    ngx_buf_t       *shadow;	   // 当前缓冲区的影子缓冲区
 
 
    /* the buf's content could be changed */
    unsigned         temporary:1;	/* 标志位，为1时，内存可修改 */
 
    /*  
     * the buf's content is in a memory cache or in a read only memory
     * and must not be changed
     */
    unsigned         memory:1;		/* 标志位，为1时，内存只读 */
 
    /* the buf's content is mmap()ed and must not be changed */
    unsigned         mmap:1;		/* 标志位，为1时，mmap映射过来的内存，不可修改 */
 
    unsigned         recycled:1;	/* 标志位，为1时，可回收 */
    unsigned         in_file:1;		/* 标志位，为1时，表示处理的是文件 */
    unsigned         flush:1;		/* 标志位，为1时，表示需要进行flush操作 */
    unsigned         sync:1;		/* 标志位，为1时，表示可以进行同步操作，容易引起堵塞 */
    unsigned         last_buf:1;	/* 标志位，为1时，表示为缓冲区链表ngx_chain_t上的最后一块待处理缓冲区 */	
    unsigned         last_in_chain:1;/* 标志位，为1时，表示为缓冲区链表ngx_chain_t上的最后一块缓冲区 */
 
    unsigned         last_shadow:1;	 /* 标志位，为1时，表示是否是最后一个影子缓冲区 */
    unsigned         temp_file:1;	 /* 标志位，为1时，表示当前缓冲区是否属于临时文件 */
 
    /* STUB */ int   num;
};

```

### 用途

用于处理大数据的关键数据，及用于内存数据也用于磁盘数据