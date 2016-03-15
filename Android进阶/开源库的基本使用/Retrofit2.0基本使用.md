# Retrofit2.0

>以下只是本人对`Retrofit2.0`基本使用方法的归纳整理，引做笔记

### GRADLE

```java
compile 'com.squareup.retrofit2:retrofit:2.0.0-beta4'
```
`Retrofit`至少需要Java 7或者Android 2.3

### Introduction

改造将您的HTTP API到Java接口

```java
public interface GitHubService {
  @GET("users/{user}/repos")
  Call<List<Repo>> listRepos(@Path("user") String user);
}
```
`Retrofit`类对`GitHubService`接口的实现

```java
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl("https://api.github.com")
    .build();

GitHubService service = retrofit.create(GitHubService.class);
```
Here's an example of using the GsonConverterFactory class to generate an implementation of the GitHubService interface which uses Gson for its deserialization.

```java
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl("https://api.github.com")
    .addConverterFactory(GsonConverterFactory.create())
    .build();

GitHubService service = retrofit.create(GitHubService.class);
```

每一个被`GitHubService`创建的`Call`可以同步或异步的HTTP请求发送到远程的网路服务器

```java
Call<List<Repo>> repos = service.listRepos("octocat");
```
这个`Call`对象是从你的`API`接口返回的参数化后的对象。调用跟接口名相同的函数名，你就会得到一个实例出来。我们可以直接调用它的`execute`方法，但是得留意一下，这个方法只能调用一次。

- 另一个例子：

```java
interface GitHubService {
  @GET("/repos/{owner}/{repo}/contributors")
  Call<List<Contributor>> repoContributors(
      @Path("owner") String owner,
      @Path("repo") String repo);
}

Call<List<Contributor>> call =
    gitHubService.repoContributors("square", "retrofit");

response = call.execute();

// This will throw IllegalStateException:
response = call.execute();

Call<List<Contributor>> call2 = call.clone();
// This will not throw:
response = call2.execute();
```
当你尝试调用第二次的时候，就会出现失败的错误。实际上，可以直接克隆一个实例，代价非常低。当你想要多次请求一个接口的时候，直接用`clone`的方法来生产一个新的，相同的可用对象吧。

想要实现异步，需要调用`enqueue`方法。现在，我们就能通过一次声明实现同步和异步了！

```java
Call<List<Contributor>> call =
    gitHubService.repoContributors("square", "retrofit");

call.enqueue(new Callback<List<Contributor>>() {
  @Override void onResponse(/* ... */) {
    // ...
  }

  @Override void onFailure(Throwable t) {
    // ...
  }
});
```

当你将一些异步请求压入队列后，甚至你在执行同步请求的时候，你可以随时调用 cancel 方法来取消请求：

```java
Call<List<Contributor>> call =
    gitHubService.repoContributors("square", "retrofit");

call.enqueue(         );
// or...
call.execute();

// later...
call.cancel();
```










> [Retrofit](http://square.github.io/retrofit/):A type-safe HTTP client for Android and Java
如果希望了解更多新特性可以查看以下网站：
[用 Retrofit 2 简化 HTTP 请求](https://realm.io/cn/news/droidcon-jake-wharton-simple-http-retrofit-2/)

---
- 邮箱 ：[elisabethzhen@163.com](elisabethzhen@163.com)
- Good Luck!