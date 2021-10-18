## poll机制原理

​ 中断方式的按键驱动中，用户调用read读取数据时，如果一直没有按键按下，read会阻塞住，不会继续往下跑。加入poll机制后，poll会进入休眠，直到被唤醒或超时。

## 驱动相关结构

```c
//驱动的poll接口定义
unsigned int (*poll) (struct file *, struct poll_table_struct *);
//将wait对象加入poll队列
static inline void poll_wait(struct file * filp, wait_queue_head_t * wait_address, poll_table *p)
{
    if (p && wait_address)
        p->qproc(filp, wait_address, p);
}

//典型的写法， 如果有中断，则返回POLLIN，否则返回0
static unsigned forth_drv_poll(struct file *file, poll_table *wait)
{
    unsigned int mask = 0;
    poll_wait(file, &button_waitq, wait); // 不会立即休眠

    if (ev_press)
        mask |= POLLIN | POLLRDNORM;

    return mask;
}
//加入头文件
#include <linux/poll.h>
```

## 用户层的poll系统调用

```c
NAME
       poll, ppoll - wait for some event on a file descriptor

SYNOPSIS
       #include <poll.h>

       int poll(struct pollfd *fds, nfds_t nfds, int timeout);

       #define _GNU_SOURCE         /* See feature_test_macros(7) */
       #include <signal.h>
       #include <poll.h>

       int ppoll(struct pollfd *fds, nfds_t nfds,
               const struct timespec *tmo_p, const sigset_t *sigmask);

DESCRIPTION
       poll() performs a similar task to select(2): it waits for one of a set of file descriptors to become ready to perform I/O.

       The set of file descriptors to be monitored is specified in the fds argument, which is an array of structures of the following form:

           struct pollfd {
               int   fd;         /* file descriptor */
               short events;     /* requested events */
               short revents;    /* returned events */
           };

       The caller should specify the number of items in the fds array in nfds.
//常用的等待事件
 POLLIN There is data to read.

       POLLPRI
              There is some exceptional condition on the file descriptor.  Possibilities include:

              *  There is out-of-band data on a TCP socket (see tcp(7)).

              *  A pseudoterminal master in packet mode has seen a state change on the slave (see ioctl_tty(2)).

              *  A cgroup.events file has been modified (see cgroups(7)).

       POLLOUT
              Writing is now possible, though a write larger that the available space in a socket or pipe will still  block  (unless  O_NONBLOCK  is
              set).

       POLLRDHUP (since Linux 2.6.17)
              Stream  socket  peer  closed  connection, or shut down writing half of connection.  The _GNU_SOURCE feature test macro must be defined
              (before including any header files) in order to obtain this definition.

       POLLERR
              Error condition (only returned in revents; ignored in events).  This bit is also set for a file descriptor referring to the write  end
              of a pipe when the read end has been closed.

       POLLHUP
              Hang  up (only returned in revents; ignored in events).  Note that when reading from a channel such as a pipe or a stream socket, this
              event merely indicates that the peer closed its end of the channel.  Subsequent reads from the channel will return  0  (end  of  file)
              only after all outstanding data in the channel has been consumed.

       POLLNVAL
              Invalid request: fd not open (only returned in revents; ignored in events).

       When compiling with _XOPEN_SOURCE defined, one also has the following, which convey no further information beyond the bits listed above:

       POLLRDNORM
              Equivalent to POLLIN.

       POLLRDBAND
              Priority band data can be read (generally unused on Linux).

       POLLWRNORM
              Equivalent to POLLOUT.

       POLLWRBAND
              Priority data may be written.
```