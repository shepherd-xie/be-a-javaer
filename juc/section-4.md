# 常用工具类

## CountDownLatch

使用 await() 线程进入等待直到 countDown 归零

```java
public static void main(String[] args) throws InterruptedException {
    CountDownLatch countDownLatch = new CountDownLatch(10);
    for (int i = 0; i < 10; i ++) {
        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + " Out");
            countDownLatch.countDown();
        }, String.valueOf(i)).start();
    }
    countDownLatch.await();
    System.out.println("Down");
}
```

## CyclicBarrier

使用 await() 线程进入等待直到有 n 个线程同时等待

```java
public static void main(String[] args) {
    CyclicBarrier cyclicBarrier = new CyclicBarrier(10, () -> System.out.println("Down"));

    for (int i = 0; i < 10; i++) {
        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + " check");
            try {
                cyclicBarrier.await();
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            } catch (BrokenBarrierException e) {
                throw new RuntimeException(e);
            }
        }, String.valueOf(i)).start();
    }
}
```

## Semaphore

信号量机制，限制同时访问临界资源的数量

```java
public static void main(String[] args) {
    Semaphore semaphore = new Semaphore(4);

    for (int i = 0; i < 10; i++) {
        new Thread(() -> {
            try {
                semaphore.acquire();
                System.out.println(Thread.currentThread().getName() + " get resource");
                TimeUnit.MILLISECONDS.sleep(500);
                System.out.println(Thread.currentThread().getName() + " freed resource");
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            } finally {
                semaphore.release();
            }
        }, String.valueOf(i)).start();
    }
}
```

## ReadWriteLock(读写锁)

```java
public class ReadWriteLockMain {

    public static class MyCache {
        private volatile Map<String, String> cache = new HashMap<>();
        private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();

        public void put(String key, String value) {
            try {
                readWriteLock.writeLock().lock();
                System.out.println(Thread.currentThread().getName() + " put " + key);
                TimeUnit.MILLISECONDS.sleep(50);
                cache.put(key, value);
                System.out.println(Thread.currentThread().getName() + " put " + key + " success");
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            } finally {
                readWriteLock.writeLock().unlock();
            }
        }

        public void get(String key) {
            try {
                readWriteLock.readLock().lock();
                System.out.println(Thread.currentThread().getName() + " get " + key);
                cache.get(key);
                System.out.println(Thread.currentThread().getName() + " get " + key + " success");
            } finally {
                readWriteLock.readLock().unlock();
            }
        }
    }

    public static void main(String[] args) {
        MyCache myCache = new MyCache();

        for (int i = 0; i < 5; i++) {
            new Thread(() -> myCache.put(Thread.currentThread().getName(), Thread.currentThread().getName()), String.valueOf(i)).start();
        }

        for (int i = 0; i < 5; i++) {
            new Thread(() -> myCache.get(Thread.currentThread().getName()), String.valueOf(i)).start();
        }
    }

}
```

