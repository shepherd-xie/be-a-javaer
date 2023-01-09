# 线程顺序执行问题

让线程顺序执行的本质是**多个线程监视不同的信号量**

```java
public class OrderThreadApplication {

    public static class Resource {
        private ReentrantLock lock = new ReentrantLock();
        private Condition readCondition = lock.newCondition();
        private Condition writeCondition = lock.newCondition();
        private Condition listenCondition = lock.newCondition();
        private int signal = 0;

        public void doRead() {
            try {
                lock.lock();
                while (signal != 0) {
                    readCondition.await();
                }
                System.out.println(Thread.currentThread().getName() + " ==> Read");
                signal = 1;
                writeCondition.signal();
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            } finally {
                lock.unlock();
            }
        }

        public void doWrite() {
            try {
                lock.lock();
                while (signal != 1) {
                    writeCondition.await();
                }
                System.out.println(Thread.currentThread().getName() + " ==> Write");
                signal = 2;
                listenCondition.signal();
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            } finally {
                lock.unlock();
            }
        }

        public void doListen() {
            try {
                lock.lock();
                while (signal != 2) {
                    listenCondition.await();
                }
                System.out.println(Thread.currentThread().getName() + " ==> Listen");
                signal = 0;
                readCondition.signal();
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            } finally {
                lock.unlock();
            }
        }

    }


    public static void main(String[] args) {
        Resource resource = new Resource();

        for (int i = 0; i < 20; i++) {
            new Thread(resource::doRead, "Read Thread").start();
            new Thread(resource::doWrite, "Write Thread").start();
            new Thread(resource::doListen, "Listen Thread").start();
        }
    }

}
```

