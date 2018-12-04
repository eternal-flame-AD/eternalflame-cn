---
title: Python使用multiprocessing实现多进程
author: Eternal-flame-AD
layout: post
date: 2018-03-26T11:02:33+00:00
featured_image: /images/Python_logo_and_wordmark.svg_.png
categories:
  - 日常

---
Python由于GIL锁的缘故，多线程进行CPU密集任务会导致严重的性能问题，而multiprocessing库模仿了threading的使用方法，但使用了多进程避开了GIL锁的问题。

首先，先来看一个最简单的Process实现，和threading几乎一模一样的使用方法：

{{< highlight python >}}
import multiprocessing
import time
def watch(owner):
    print(owner,"is looking at his/her watch")
    time.sleep(3)
    now=time.asctime(time.localtime())
    print(owner,"'s Watch says it is ",now,sep="")


def main():
    p=multiprocessing.Process(target=watch,args=("Mike",))
    p.start()
    p.join()

if __name__=="__main__":
    main()
{{< / highlight >}}


{{< highlight powershell>}}
PS>python .\example.py
Mike is looking at his/her watch
Mike's Watch says it is Mon Mar 26 17:55:06 2018
{{< / highlight >}}


加入显示pid之后可以看到watch在不同的进程上进行：

{{< highlight python >}}
import multiprocessing
import time
import os
def watch(owner):
    print(owner,"(",os.getpid(),")"," is looking at his/her watch",sep="")
    time.sleep(3)
    now=time.asctime(time.localtime())
    print(owner,"(",os.getpid(),")","'s Watch says it is ",now,sep="")


def main():
    print("I(",os.getpid(),") am asking time.",sep="")
    p=multiprocessing.Process(target=watch,args=("Mike",))
    p.start()
    p.join()

if __name__=="__main__":
    main()
{{< / highlight >}}

{{< highlight python >}}
PS>python .\example.py
I(12800) am asking time.
Mike(16696) is looking at his/her watch
Mike(16696)'s Watch says it is Mon Mar 26 18:01:00 2018
{{< / highlight >}}

因为是在不同进程中，所以global变量并不能跨程序,这里main尝试读取watch里的now就出现了错误：

{{< highlight python >}}
import multiprocessing
import time
import os
def watch(owner):
    global now
    print(owner,"(",os.getpid(),")"," is looking at his/her watch",sep="")
    time.sleep(3)
    now=time.asctime(time.localtime())
    print(owner,"(",os.getpid(),")","'s Watch says it is ",now,sep="")


def main():
    global now
    print("I(",os.getpid(),") am asking time.",sep="")
    p=multiprocessing.Process(target=watch,args=("Mike",))
    p.start()
    p.join()
    print("I see Mike's watch says:",now)

if __name__=="__main__":
    main()
{{< / highlight >}}

{{< highlight python >}}
PS>python .\example.py
I(17536) am asking time.
Mike(12136) is looking at his/her watch
Mike(12136)'s Watch says it is Mon Mar 26 18:04:44 2018
Traceback (most recent call last):
  File ".\example.py", line 21, in &lt;module>
    main()
  File ".\example.py", line 18, in main
    print("I see Mike's watch says:",now)
NameError: name 'now' is not defined
{{< / highlight >}}

multiprocessing中有multiprocessing.Pipe()可以建立一个Pipe跨进程传送变量,调用一次multiprocessing.Pipe()可以得到一个双向pipe：

{{< highlight python >}}
import multiprocessing
import time
import os
def watch(owner,conn):
    print(owner,"(",os.getpid(),")"," is looking at his/her watch",sep="")
    time.sleep(3)
    now=time.asctime(time.localtime())
    conn.send(now)
    print(owner,"(",os.getpid(),")","'s Watch says it is ",now,sep="")


def main():
    print("I(",os.getpid(),") am asking time.",sep="")
    conn_for_me,conn_for_mike=multiprocessing.Pipe()
    p=multiprocessing.Process(target=watch,args=("Mike",conn_for_mike))
    p.start()
    p.join()
    print("I see Mike's watch says:",conn_for_me.recv())

if __name__=="__main__":
    main()
{{< / highlight >}}

{{< highlight python >}}
python .\example.py
I(9812) am asking time.
Mike(7288) is looking at his/her watch
Mike(7288)'s Watch says it is Mon Mar 26 18:15:46 2018
I see Mike's watch says: Mon Mar 26 18:15:46 2018
{{< / highlight >}}

multiprocess还仿照queue实现了进程安全的multiprocess.Queue

{{< highlight python >}}
from multiprocessing import Process, Queue

def f(q):
    q.put([42, None, 'hello'])

if __name__ == '__main__':
    q = Queue()
    p = Process(target=f, args=(q,))
    p.start()
    print(q.get())    # prints "[42, None, 'hello']"
    p.join()
{{< / highlight >}}

也有可以用进程锁：

{{< highlight python >}}
from multiprocessing import Process, Lock

def f(l, i):
    l.acquire()
    try:
        print('hello world', i)
    finally:
        l.release()

if __name__ == '__main__':
    lock = Lock()

    for num in range(10):
        Process(target=f, args=(lock, num)).start()
{{< / highlight >}}

multiprocess的功能很强大，还有进程池管理，共享内存，服务器进程等的支持，如果有时间学习了再码出来~