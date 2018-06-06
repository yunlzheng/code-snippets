# Java

> Code Snippets

## 并发编程

### Thread

```Java
public class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Hello thread");
    }
}
```

### Runable

```Java
public class MyRunable implements Runnable {
    @Override
    public void run() {
        System.out.println("Hello runable");
    }
}
```

### Thread Pool

``` Java
ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(0, Integer.MAX_VALUE, 60L, TimeUnit.SECONDS, new SynchronousQueue<>());
// 等价于 Executors.newCachedThreadPool();
threadPoolExecutor.execute(runable);
```

``` Java
public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue)
```

### CountDownLatch

```java
public class CountDownSample {

    public static void main(String[] args) throws InterruptedException {

        ExecutorService executorService = Executors.newFixedThreadPool(2);

        // 定义CountDownLatch的count为2
        CountDownLatch _latch = new CountDownLatch(2);

        executorService.execute(new RemoteCallRunnable(_latch));
        executorService.execute(new RemoteCallRunnable(_latch));

        // 等待count为0，即等待两次remote call调用成功
        _latch.await();
    }

}


class RemoteCallRunnable implements Runnable {

    private CountDownLatch _latch;

    public RemoteCallRunnable(CountDownLatch _latch) {
        this._latch = _latch;
    }

    @Override
    public void run() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            _latch.countDown();
        }

    }

}
```

### Future

```java
ExecutorService executorService = Executors.newCachedThreadPool();
Future<Double> future = executorService.submit(() -> 0.0);

try {
    Double result = future.get(1, TimeUnit.SECONDS);
} catch (InterruptedException e) {
    e.printStackTrace();
} catch (ExecutionException e) {
    e.printStackTrace();
} catch (TimeoutException e) {
    e.printStackTrace();
}
```

### CompletableFuture

一般用法

```java
public Future<Double> getPriceAsync() {
    CompletableFuture<Double> future = new CompletableFuture<>();
    new Thread(() -> {
        future.complete(caculatePrice());
    }).start();
    return future;
}
```

错误处理

```java
public Future<Double> getPriceAsync() {
    CompletableFuture<Double> future = new CompletableFuture<>();
    new Thread(() -> {
        try {
            future.complete(caculatePrice());
        } catch (Exception e) {
            future.completeExceptionally(e);
        }
    }).start();
    return future;
}
```

工厂方法

```java
public Future<Double> getPriceAsync() {
    return CompletableFuture.supplyAsync(()-> caculatePrice());
}
```

并行请求

> 计算密集型建议使用parallelStream,IO密集型建议使用CompletableFuture

```java
public List<String> findPrices() {
    List<CompletableFuture<String>> futures = shops.stream()
        .map((shop) -> CompletableFuture.supplyAsync(() -> shop.getName() + " price is" + shop.getProce())).collect(toList());
    return futures.stream().map(CompletableFuture::join).collect(toList());
}
```

合并异步请求结果

```java
CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> "Hello");
CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> "World");
CompletableFuture<String> future3 = CompletableFuture.supplyAsync(() -> "!");

CompletableFuture<String> result = CompletableFuture
        .allOf(future1, future2, future3)
        .thenApplyAsync((Void) -> String.format("%s%s%s", future1.join(), future1.join(), future3.join()))
        .exceptionally(e -> e.getMessage());

return result.join();
```