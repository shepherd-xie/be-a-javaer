# 生产者和消费者问题

## synchronized 模式

```java
public class Application {

    public static class Resource {
        private int counter;

        /**
         * 模拟临界资源生产(Provider)
         */
        public synchronized void increment() {
            if (counter != 0) {
                // 等待临界资源消费
                try {
                    this.wait();
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
            counter ++;
            System.out.println(Thread.currentThread().getName() + " ==> " + counter);
            this.notifyAll();
        }

        /**
         * 模拟临界资源消费(Consumer)
         */
        public synchronized void decrement() {
            if (counter == 0) {
                // 等待临界资源生产
                try {
                    this.wait();
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
            counter --;
            System.out.println(Thread.currentThread().getName() + " ==> " + counter);
            this.notifyAll();
        }
    }

    public static void main(String[] args) {
        Resource resource = new Resource();

        new Thread(() -> {
            for (int i = 0; i < 20; i ++) {
                resource.increment();
            }
        }, "Provider-1").start();

        new Thread(() -> {
            for (int i = 0; i < 20; i ++) {
                resource.decrement();
            }
        }, "Consumer-1").start();

        new Thread(() -> {
            for (int i = 0; i < 20; i ++) {
                resource.increment();
            }
        }, "Provider-2").start();

        new Thread(() -> {
            for (int i = 0; i < 20; i ++) {
                resource.decrement();
            }
        }, "Consumer-2").start();
    }

}
```

上述代码在测试过程中发生了**虚假唤醒**行为，即：当线程从等待已发出信号的条件变量中醒来时，只会发现它所等待的条件不满足时，就会发生虚假唤醒。

可以发现当 Provider 线程生产之后再唤醒其他线程时，我们期望的状态是仅唤醒等待中的两个 Consumer 线程，实际上它也会唤醒另一个等待中的 Provider 线程，Consumer 线程也是同理，消除这种虚假唤醒的现象我们可以采用**自旋**操作，即：让不满足条件的线程继续等待。

```java
public static class Resource {
    private int counter;

    /**
	  * 模拟临界资源生产(Provider)
	  */
    public synchronized void increment() {
        while (counter != 0) {
            // 等待临界资源消费
            try {
                this.wait();
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
        counter ++;
        System.out.println(Thread.currentThread().getName() + " ==> " + counter);
        this.notifyAll();
    }

    /**
     * 模拟临界资源消费(Consumer)
     */
    public synchronized void decrement() {
        while (counter == 0) {
            // 等待临界资源生产
            try {
                this.wait();
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
        counter --;
        System.out.println(Thread.currentThread().getName() + " ==> " + counter);
        this.notifyAll();
    }
}
```

### ReentrantLock 模式

```java
public static class Resource {
    private int counter;

    private Lock lock;
    private Condition condition;

    public Resource() {
        this.lock = new ReentrantLock();
        this.condition = this.lock.newCondition();
    }

    /**
     * 模拟临界资源生产(Provider)
     */
    public void increment() {
        try {
            this.lock.lock();
            while (counter != 0) {
                // 等待临界资源消费
                condition.await();
            }
            counter ++;
            System.out.println(Thread.currentThread().getName() + " ==> " + counter);
            this.condition.signalAll();
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } finally {
            this.lock.unlock();
        }
    }

    /**
     * 模拟临界资源消费(Consumer)
     */
    public void decrement() {
        try {
            this.lock.lock();
            while (counter == 0) {
                // 等待临界资源生产
                condition.await();
            }
            counter --;
            System.out.println(Thread.currentThread().getName() + " ==> " + counter);
            this.condition.signalAll();
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } finally {
            this.lock.unlock();
        }
    }
}
```

