1.  pool
>http://blog.csdn.net/seetheworld518/article/details/49639651
map(func, iterable[, chunksize=None])
注意，虽然第二个参数是一个迭代器，但在实际使用中，必须在整个队列都就绪后，程序才会运行子进程。\n
主进程阻塞等待子进程的退出，join方法必须在close或terminate之后使用。\n
`import time
from multiprocessing import Pool
def run(fn) :
  time.sleep(2)
  print fn
if __name__ == "__main__" :
  startTime = time.time()
  testFL = [1,2,3,4,5]
  pool = Pool(10)
  pool.map(run,testFL)
  pool.close()
  pool.join()   
  endTime = time.time()
  print "time :", endTime - startTime`
>http://bbs.csdn.net/topics/390474161
最终改用pool.async来代替map
>https://www.zhihu.com/question/39032759?sort=created
Pool不能在__main__那个名字空间里用。把它封进一个函数里就行了，像这样：def f(x):
     return x**2
def shell():
     pool=Pool(4)
     r=pool.map(f,range(10))
     pool.close()
     pool.join()
     return r
map_async(func, iterable[, chunksize[, callback]])
>http://bugs.python.org/issue10015

