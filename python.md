import threading
任何进程默认会启动一个线程，称为主线程，主线程可以创建子线程
print("主线程(%s)" %(threading.current_thread().name))
创建子线程
t = threading.Thread(target=run,args =())
等待子线程结束
t.start()
等待子线程结束
t.join()


线程间全局数据共享
线程锁解决线程混乱(2个线程同时进行)
第一个线程进入锁的位置后加锁
第二个线程进入当前位置时候，发现已经加锁就停止等待
第一个线程解锁，第二个线程开始然后加锁
在需要位置建立锁对象
lock = threading.Lock()

修改完后一定要释放锁
lock.release()



from multiprocessing import Pool


print("父进程启动")
   #创建进程池
   #表示可以同时进行的进程的数量
   #Pool默认是cpu核心数

   pp=Pool(2)
   #让2个cpu执行5个进程
   #创建进程
   for i in range(5):
       #创建进程放入进程池统一管理
       #子进程的执行是没有顺序的
       pp.apply_async(run)
   pp.close()
   #在调用join 之前必须先调用close ,调用close之后不能再继续添加进程
  #等待进程池中的所有子进程结束才调用父进程
   pp.join()



   创建单个进程
   q = Queue()
    pw = Process(target = write,args =(q,))
    pr  =Process(target = read,args = (q,))
    pw.start()
    pr.start()
    pw.join()
