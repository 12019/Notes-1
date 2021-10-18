# 点亮LED

## IO地址映射

```c
/*
 * ioremap     -   map bus memory into CPU space
 * @offset:    bus address of the memory
 * @size:      size of the resource to map
 *
 * ioremap performs a platform specific sequence of operations to
 * make bus memory CPU accessible via the readb/readw/readl/writeb/
 * writew/writel functions and the other mmio helpers. The returned
 * address is not guaranteed to be usable directly as a virtual
 * address.
 */
#define ioremap(offset, size)						\
    __ioremap_mode((offset), (size), _CACHE_UNCACHED)
    
#define ioremap_nocache(offset, size)		\
    __ioremap((offset), (size), 0)

#define iounmap(addr)				\
    __iounmap(addr)
```

## 用户空间数据交换

```c
#define copy_from_user(to, from, n)	__copy_from_user (to, from, n)
#define copy_to_user(to, from, n) 	__copy_to_user(to, from, n)
```

## 次设备号获取

```c
struct file {
    /*
     * fu_list becomes invalid after file_free is called and queued via
     * fu_rcuhead for RCU freeing
     */
    union {
        struct list_head	fu_list;
        struct rcu_head 	fu_rcuhead;
    } f_u;
    struct path		f_path;
#define f_dentry	f_path.dentry
#define f_vfsmnt	f_path.mnt
    const struct file_operations	*f_op;
    atomic_t		f_count;
    unsigned int 		f_flags;
    mode_t			f_mode;
    loff_t			f_pos;
    struct fown_struct	f_owner;
    unsigned int		f_uid, f_gid;
    struct file_ra_state	f_ra;

    unsigned long		f_version;
#ifdef CONFIG_SECURITY
    void			*f_security;
#endif
    /* needed for tty driver, and maybe others */
    void			*private_data;

#ifdef CONFIG_EPOLL
    /* Used by fs/eventpoll.c to link all the hooks to this file */
    struct list_head	f_ep_links;
    spinlock_t		f_ep_lock;
#endif /* #ifdef CONFIG_EPOLL */
    struct address_space	*f_mapping;
};

int minor = MINOR(led_file->f_dentry->d_inode->i_rdev);
```