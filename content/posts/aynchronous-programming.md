---
title: 'Asynchronous Programming in Java'
date: 2024-07-21T22:30:12-04:00
draft: false
tags:
  - java
  - async
categories: notes
keywords:
  - java
  - interview
---

## Asynchronous Programming in Java

### Parallel vs. Concurrent vs. Asynchronous

- **Parallel**: Multiple tasks executed at the same time. For example, I can walk and talk at the same time.
- **Concurrent**: I can work on multiple tasks, but I can do only one task at a moment in time. For example, I can talk or drink water at a moment.
- **Asynchronous**: It just means non-blocking. For example, I can brew a coffee, and in the meantime, I could do something else.
  - responsive
  - preemptive
  - performance

> For Async programming JavaScript used **callback**.
> **Callbacks** created a problems of its own like callback hell.
> **Promise** came to solve the problem.

### Promise

State of a promise.

- **Pending** when the promise is executed.
- **Resolved** when the promise is completed.
- **Rejected** when promise failed.

### Completable Future

- CompletableFuture in Java is Promises in JavaScript
- `runAsync` - triggers the promise and forgets.
- `supplyAsync` - get the promise response.
- `thenApply` - maps response of promise
- `thenAccept` - consume the response of promise
- `get` - blocks the thread for promise to resolve or reject
- `thenCompose` - if the response is another promise
- `thenCombine` - to combine multiple promise

```java
import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;

@Slf4j
public class Sample {

	@SneakyThrows
	public static int compute(int n) {
		Thread.sleep(1000);
		return n * 2;
	}

	public static CompletableFuture<Integer> create(int n) {
		return CompletableFuture.supplyAsync(() -> compute(n));
	}

	public static void main(String[] args) throws ExecutionException, InterruptedException {
		log.info("future: {}", create(5)); // Not Complete
		create(4)
				.thenApply(data -> data + 1)
				.thenAccept(r -> log.info("future: {}", r))
				.get();

		var cf1 = create(2);
		var cf2 = create(3);

        // thenCombine
		cf1.thenCombine(cf2, Integer::sum)
				.thenAccept(v -> log.info("sum: {}", v));

        // thenCompose
		create(2)
//				.thenApply(Sample::create)
				.thenCompose(Sample::create)
				.thenAccept(r -> log.info("future: {}", r));
	}
}
```
