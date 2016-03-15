# Dagger入门知识归纳
### 依赖注入（Dependency Injection）简介

在软件工程领域，依赖注入（Dependency Injection）是用于实现控制反转（Inversion of Control）的最常见的方式之一。而控制反转用于解耦,控制反转还有一种常见的实现方式称为依赖查找。

使用依赖注入可以带来以下好处：

- 依赖的注入和配置独立于组件之外。
因为对象是在一个独立、不耦合的地方初始化，所以当注入抽象方法的时候，我们只需要修改对象的实现方法，而不用大改代码库。

- 依赖可以注入到一个组件中：我们可以注入这些依赖的模拟实现，这样使得测试更加简单。
可以看到，能够管理创建实例的范围是一件非常棒的事情。按我的观点，你app中的所有对象或者协作者都不应该知道有关实例创建和生命周期的任何事情，这些都应该由我们的依赖注入框架管理的。

![依赖注入](http://www.jcodecraeer.com/uploads/20150519/1431999088123888.png)

引用一段例子来介绍为什么需要依赖注入

```java
public class MovieLister {
    private MovieFinder finder;

    public MovieLister() {
        finder = new MovieFinderImpl();
    }

    public Movie[] moviesDirectedBy(String arg) {
        List allMovies = finder.findAll();
        for (Iterator it = allMovies.iterator(); it.hasNext();) {
            Movie movie = (Movie) it.next();
            if (!movie.getDirector().equals(arg)) it.remove();
        }
        return (Movie[]) allMovies.toArray(new Movie[allMovies.size()]);
    }
    ...
}

public interface MovieFinder {
    List findAll();
}
```

> 引用[CodeThink](http://codethink.me/2015/08/01/dependency-injection-theory/)中对这段代码的解释：

>我们创建了一个名为`MovieLister`的类来提供需要的电影列表，它`moviesDirectedBy`方法提供根据导演名来搜索电影的方式。真正负责搜索电影的是实现了`MovieFinder`接口的`MovieFinderImpl`，我们的`MovieLister`类在构造函数中创建了一个`MovieFinderImpl`的对象。

>目前看来，一切都不错。但是，当我们希望修改`finder`，将`finder`替换为一种新的实现时（比如为`MovieFinder`增加一个参数表明`Movie`数据的来源是哪个数据库），我们不仅需要修改`MovieFinderImpl`类，还需要修改我们`MovieLister`中创建`MovieFinderImpl`的代码。

>这就是依赖注入要处理的耦合。这种在`MovieLister`中创建`MovieFinderImpl`的方式，使得`MovieLister`不仅仅依赖于`MovieFinder`这个接口，它还依赖于`MovieListImpl`这个实现。 这种在一个类中直接创建另一个类的对象的代码，和硬编码（hard-coded strings)以及硬编码的数字（magic numbers）一样，是一种导致耦合的坏味道，我们可以把这种坏味道称为硬初始化（hard init）。同时，我们也应该像记住硬编码一样记住，new（对象创建）是有毒的。
>Hard Init带来的主要坏处有两个方面：1）上文所述的修改其实现时，需要修改创建处的代码；2）不便于测试，这种方式创建的类（上文中的`MovieLister`）无法单独被测试，其行为和`MovieFinderImpl`紧紧耦合在一起，同时，也会导致代码的可读性问题（“如果一段代码不便于测试，那么它一定不便于阅读。”）。


---
### 依赖注入的实现方式

1. 构造函数注入（Contructor Injection）

修改一下上面代码中`MovieList`的构造函数，使得`MovieFinderImpl`的实现在`MovieLister`类之外创建。这样，`MovieLister`就只依赖于我们定义的`MovieFinder`接口，而不依赖于`MovieFinder`的实现了。

```java
public class MovieLister {
    private MovieFinder finder;

    public MovieLister(MovieFinder finder) {
        this.finder = finder;
    }
    ...
}
```
2. `setter`注入

增加一个`setter`函数来传入创建好的`MovieFinder`对象，这样同样可以避免在`MovieFinder`中`hard init`这个对象

```java
public class MovieLister {
    ...
    public void setFinder(MovieFinder finder) {
        this.finder = finder;
    }
}
```

3. 接口注入

接口注入使用接口来提供`setter`方法，其实现方式如下:

首先要创建一个注入使用的接口

```java
public interface InjectFinder {
    void injectFinder(MovieFinder finder);
}
```
之后，让`MovieLister`实现这个接口。

```java
class MovieLister implements InjectFinder {
    ...
    public void injectFinder(MovieFinder finder) {
      this.finder = finder;
    }
    ...
}
```
最后，我们需要根据不同的框架创建被依赖的`MovieFinder`的实现。

---
### Dagger1


基本特点：

- 多个注入点：依赖，通过injected
- 多种绑定方法：依赖，通过provided
- 多个modules：实现某种功能的绑定集合
- 多个对象图： 实现一个范围的modules集合

Dagger1是在编译的时候实行绑定，不过也用到了反射机制。但这个反射不是用来实例化对象的，而是用于图的构成。Dagger会在运行的时候去检测是否一切都正常工作，所以使用的时候会付出一些代价：偶尔会无效和调试困难。

---
### Dagger2

`Dagger2`与`Dagger1`之间的区别：

- 再也没有使用反射：图的验证、配置和预先设置都在编译的时候执行。
- 容易调试和可跟踪：完全具体地调用提供和创建的堆栈
- 更好的性能：谷歌声称他们提高了13%的处理性能
- 代码混淆：使用派遣方法，就如同自己写的代码一样

当然所有这些很棒的特点都需要付出一个代价，那就是缺乏灵活性，例如：Dagger2没用反射所以没有动态机制。

#### 深入研究

一些依赖注入的基本概念：

- `@Inject`: 通常在需要依赖的地方使用这个注解。换句话说，你用它告诉`Dagger`这个类或者字段需要依赖注入。这样，`Dagger`就会构造一个这个类的实例并满足他们的依赖。
- `@Module`: `Modules`类里面的方法专门提供依赖，所以我们定义一个类，用`@Module`注解，这样`Dagger`在构造类的实例的时候，就知道从哪里去找到需要的 依赖。`modules`的一个重要特征是它们设计为分区并组合在一起（比如说，在我们的app中可以有多个组成在一起的`modules`）。
- `@Provide`: 在`modules`中，我们定义的方法是用这个注解，以此来告诉`Dagger`我们想要构造对象并提供这些依赖。
- `@Component`: `Components`从根本上来说就是一个**注入器**，也可以说是`@Inject`和`@Module`的桥梁，**它的主要作用就是连接这两个部分**。 `Components`可以提供所有定义了的类型的实例，比如：我们必须用`@Component`注解一个接口然后列出所有的`@Modules`组成该组件，如果缺失了任何一块都会在编译的时候报错。所有的组件都可以通过它的`modules`知道依赖的范围。
- `@Scope`: `Scopes`可是非常的有用，`Dagger2`可以通过自定义注解限定注解作用域。后面会演示一个例子，这是一个非常强大的特点，因为就如前面说的一样，没必要让每个对象都去了解如何管理他们的实例。在`scope`的例子中，我们用自定义的`@PerActivity`注解一个类，所以这个对象存活时间就和`activity`的一样。简单来说就是我们可以定义所有范围的粒度(`@PerFragment`, `@PerUser`, 等等)。
- `Qualifier`: 当类的类型不足以鉴别一个依赖的时候，我们就可以使用这个注解标示。例如：在`Android`中，我们会需要不同类型的`context`，所以我们就可以定义`qualifier`注解`“@ForApplication”`和`“@ForActivity”`，这样当注入一个`context`的时候，我们就可以告诉 `Dagger`我们想要哪种类型的`context`。

#### `build.gradle`文件配置:

```java
apply plugin: 'com.neenbedankt.android-apt'

buildscript {
  repositories {
    jcenter()
  }
  dependencies {
    classpath 'com.neenbedankt.gradle.plugins:android-apt:1.4'
  }
}

android {
  ...
}

dependencies {
  apt 'com.google.dagger:dagger-compiler:2.0'
  compile 'com.google.dagger:dagger:2.0'

  ...
}
```
添加了编译和运行库，还有必不可少的`apt`插件，没有这插件，`dagger`可能不会正常工作，特别是在`Android studio`中。

#### 例子

```java
 @Override void initializePresenter() {
    // All this dependency initialization could have been avoided by using a
    // dependency injection framework. But in this case this is used this way for
    // LEARNING EXAMPLE PURPOSE.
    ThreadExecutor threadExecutor = JobExecutor.getInstance();
    PostExecutionThread postExecutionThread = UIThread.getInstance();

    JsonSerializer userCacheSerializer = new JsonSerializer();
    UserCache userCache = UserCacheImpl.getInstance(getActivity(), userCacheSerializer,
        FileManager.getInstance(), threadExecutor);
    UserDataStoreFactory userDataStoreFactory =
        new UserDataStoreFactory(this.getContext(), userCache);
    UserEntityDataMapper userEntityDataMapper = new UserEntityDataMapper();
    UserRepository userRepository = UserDataRepository.getInstance(userDataStoreFactory,
        userEntityDataMapper);

    GetUserDetailsUseCase getUserDetailsUseCase = new GetUserDetailsUseCaseImpl(userRepository,
        threadExecutor, postExecutionThread);
    UserModelDataMapper userModelDataMapper = new UserModelDataMapper();

    this.userDetailsPresenter =
        new UserDetailsPresenter(this, getUserDetailsUseCase, userModelDataMapper);
  }
```
上面的问题的解决办法是使用依赖注入框架。我们要避免像上面这样引用代码：这个类不能涉及对象的创建和依赖的提供。 那我们该怎么做呢，当然是使用`Dagger2`，我们先看看结构图：

![结构图](http://www.jcodecraeer.com/uploads/20150519/1431999102454673.png)

接下来我们会分解这张图，并解释各个部分还有代码。

- `Application Component`: 生命周期跟`Application`一样的组件。可注入到`AndroidApplication`和`BaseActivity`中类中。

```java
@Singleton // Constraints this component to one-per-application or unscoped bindings.
@Component(modules = ApplicationModule.class)
public interface ApplicationComponent {
  void inject(BaseActivity baseActivity);

  //暴露给子图
  Context context();
  ThreadExecutor threadExecutor();
  PostExecutionThread postExecutionThread();
  UserRepository userRepository();
}
```
为这个组件使用了`@Singleton`注解，使其保证唯一性。也许你会问为什么要将`context`和其他成员暴露出去。这正是`Dagger`中 `components`工作的重要性质：如果你不想把`modules`的类型暴露出来，那么你就只能显示地使用它们。在这个例子中，将这些元素暴露给子图， 如果你把他们删掉，编译的时候就会报错。

- `Application Module`: `module`的作用是提供在应用的生命周期中存活的对象。这也是为什么`@Provide`注解的方法要用`@Singleton`限定。

```java
@Module
public class ApplicationModule {
  private final AndroidApplication application;

  public ApplicationModule(AndroidApplication application) {
    this.application = application;
  }

  @Provides @Singleton Context provideApplicationContext() {
    return this.application;
  }

  @Provides @Singleton Navigator provideNavigator() {
    return new Navigator();
  }

  @Provides @Singleton ThreadExecutor provideThreadExecutor(JobExecutor jobExecutor) {
    return jobExecutor;
  }

  @Provides @Singleton PostExecutionThread providePostExecutionThread(UIThread uiThread) {
    return uiThread;
  }

  @Provides @Singleton UserCache provideUserCache(UserCacheImpl userCache) {
    return userCache;
  }

  @Provides @Singleton UserRepository provideUserRepository(UserDataRepository userDataRepository) {
    return userDataRepository;
  }
}
```
- `Activity Component`: 生命周期跟`Activity`一样的组件。

```java
@PerActivity
@Component(dependencies = ApplicationComponent.class, modules = ActivityModule.class)
public interface ActivityComponent {
  //Exposed to sub-graphs.
  Activity activity();
}
```
`@PerActivity`是一个自定义的范围注解，作用是允许对象被记录在正确的组件中，当然这些对象的生命周期应该遵循`activity`的生命周期。这是一个很好的练习，我建议你们都做一下，有以下好处：

- 注入对象到构造方法需要的`activity`。
- 在一个`per-activity`基础上的单例使用。
- 只能在`activity`中使用使得全局的对象图保持清晰。

代码如下：

```java
@Scope
@Retention(RUNTIME)
public @interface PerActivity {}
```

- `Activity Module`: 在对象图中，这个`module`把`activity`暴露给相关联的类。比如在`fragment`中使用`activity`的`context`。

```java
@Module
public class ActivityModule {
  private final Activity activity;

  public ActivityModule(Activity activity) {
    this.activity = activity;
  }

  @Provides @PerActivity Activity activity() {
    return this.activity;
  }
}
```

- `User Component`: 继承于`ActivityComponent`的组件，并用`@PerActivity`注解。我通常会在注入用户相关的`fragment`中使用。因为 `ActivityModule`把`activity`暴露给图了，所以在任何需要一个`activity`的`context`的时候，`Dagger`都可以提供注入， 没必要再在子`modules`中定义了。

```java
@PerActivity
@Component(dependencies = ApplicationComponent.class, modules = {ActivityModule.class, UserModule.class})
public interface UserComponent extends ActivityComponent {
  void inject(UserListFragment userListFragment);
  void inject(UserDetailsFragment userDetailsFragment);
}
```
- `User Module`: 提供跟用户相关的实例。基于我们的例子，它可以提供用户用例。

```java
@Module
public class UserModule {
  @Provides @PerActivity GetUserListUseCase provideGetUserListUseCase(GetUserListUseCaseImpl getUserListUseCase) {
    return getUserListUseCase;
  }

  @Provides @PerActivity GetUserDetailsUseCase provideGetUserDetailsUseCase(GetUserDetailsUseCaseImpl getUserDetailsUseCase) {
    return getUserDetailsUseCase;
  }
}
```
#### 整合在一起

现在我们已经实现了依赖注入图，但是我该如何注入？我们需要知道，Dagger给了我们一堆选择用来注入依赖：

1. 构造方法注入：在类的构造方法前面注释`@Inject`
2. 成员变量注入：在类的成员变量（非私有）前面注释`@Inject`
3. 函数方法注入：在函数前面注释`@Inject`

这个顺序是`Dagger`建议使用的，因为在运行的过程中，总会有一些奇怪的问题甚至是空指针，这也意味着你的依赖在对象创建的时候可能还没有初始化 完成。这在`Android`的`activity`或者`fragment`中使用成员变量注入会经常遇到，因为我们没有在它们的构造方法中使用。

回到我们的例子中，看一下我们是如何在`BaseActivity`中注入一个成员变量。在这个例子中，我们注入了一个叫`Navigator`的类，它是我们应用中负责管理导航的类。

```java
public abstract class BaseActivity extends Activity {

  @Inject Navigator navigator;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    this.getApplicationComponent().inject(this);
  }

  protected ApplicationComponent getApplicationComponent() {
    return ((AndroidApplication)getApplication()).getApplicationComponent();
  }

  protected ActivityModule getActivityModule() {
    return new ActivityModule(this);
  }
}
```

`Navigator`类是成员变量注入的，由`ApplicationModule`里面`@Provide`注解显示提供的。最终我们初始化 `component`然后调用`inject()`方法注入成员变量。我们通过在`Activity`的`onCreate()`方法中调用 `getApplicationComponent()`，完成这些操作。
`getApplicationComponent()`方法放在这儿是为了复用性，它 的主要作用是为了获取实例化的`ApplicationComponent`对象。

在`Fragment`的`presenter`中我们也做了同样的事情，这儿的获取方法有一点不一样，因为我们使用的是`per-activity`范围限定的`component`。所以我们注入到`UserDetailsFragment`中的`UserComponent`其实是驻留在 `UserDetailsActivity`中的。

```java
private UserComponent userComponent;
```
我们必须在`activity`的`onCreate()`方法中用下面的方式初始化。

```java
private void initializeInjector() {
  this.userComponent = DaggerUserComponent.builder()
      .applicationComponent(getApplicationComponent())
      .activityModule(getActivityModule())
      .build();
}
```
`Dagger`会处理我们的注解，为`components`生成实现并重命名加上`“Dagger”`前缀。因为这个是一个组合的`component`，所以在构建的时候，我们必须把所有的依赖传进去（`components`和`modules`）。现在我们的`component`已经准备好了，接着为了可以满足`fragment`的依赖需求，我们写一个获取方法：

```java
@Override public UserComponent getComponent() {
  return userComponent;
}
```
我们现在可以利用`get`方法获取创建的`component`，然后调用`inject()`方法将`Fragment`作为参数传进去，这样就完成了绑定`UserDetailsFragment`依赖。

```java
@Override public void onActivityCreated(Bundle savedInstanceState) {
  super.onActivityCreated(savedInstanceState);
  this.getComponent.inject(this);
}
```
这里面有一些地方重构了的，我可以告诉你一个重要的思想（来自官方的例子）是：

```java
public interface HasComponent<C> {
  C getComponent();
}
```
因此，客户端（例如`fragment`）可以获取并且使用`component`（来自`activity`）：

```java
@SuppressWarnings("unchecked")
protected <C> C getComponent(Class<C> componentType) {
  return componentType.cast(((HasComponent<C>)getActivity()).getComponent());
}
```
这儿使用了强制转换，不论这个客户端不能获取到能用的`component`，但是至少很快就会失败。

---

### Dagger2生成的代码

在了解`Dagger`的主要特征之后，我们再来看看内部构造。为了举例说明，我们还是用`Navigator`类，看看它是如何创建和注入的。首先我们看一下我们的`DaggerApplicationComponent`。

```java
@Generated("dagger.internal.codegen.ComponentProcessor")
public final class DaggerApplicationComponent implements ApplicationComponent {
  private Provider<Navigator> provideNavigatorProvider;
  private MembersInjector<BaseActivity> baseActivityMembersInjector;

  private DaggerApplicationComponent(Builder builder) {
    assert builder != null;
    initialize(builder);
  }

  public static Builder builder() {
    return new Builder();
  }

  private void initialize(final Builder builder) {
    this.provideNavigatorProvider = ScopedProvider.create(ApplicationModule_ProvideNavigatorFactory.create(builder.applicationModule));
    this.baseActivityMembersInjector = BaseActivity_MembersInjector.create((MembersInjector) MembersInjectors.noOp(), provideNavigatorProvider);
  }

  @Override
  public void inject(BaseActivity baseActivity) {
    baseActivityMembersInjector.injectMembers(baseActivity);
  }

  public static final class Builder {
    private ApplicationModule applicationModule;

    private Builder() {
    }

    public ApplicationComponent build() {
      if (applicationModule == null) {
        throw new IllegalStateException("applicationModule must be set");
      }
      return new DaggerApplicationComponent(this);
    }

    public Builder applicationModule(ApplicationModule applicationModule) {
      if (applicationModule == null) {
        throw new NullPointerException("applicationModule");
      }
      this.applicationModule = applicationModule;
      return this;
    }
  }
}
```
有两个重点需要注意:
- 第一个：由于我们要将依赖注入到`activity`中，所以会得到一个注入这个比成员的注入器（由`Dagger`生成的`BaseActivity_MembersInjector`）：

```java
@Generated("dagger.internal.codegen.ComponentProcessor")
public final class BaseActivity_MembersInjector implements MembersInjector<BaseActivity> {
  private final MembersInjector<Activity> supertypeInjector;
  private final Provider<Navigator> navigatorProvider;

  public BaseActivity_MembersInjector(MembersInjector<Activity> supertypeInjector, Provider<Navigator> navigatorProvider) {
    assert supertypeInjector != null;
    this.supertypeInjector = supertypeInjector;
    assert navigatorProvider != null;
    this.navigatorProvider = navigatorProvider;
  }

  @Override
  public void injectMembers(BaseActivity instance) {
    if (instance == null) {
      throw new NullPointerException("Cannot inject members into a null reference");
    }
    supertypeInjector.injectMembers(instance);
    instance.navigator = navigatorProvider.get();
  }

  public static MembersInjector<BaseActivity> create(MembersInjector<Activity> supertypeInjector, Provider<Navigator> navigatorProvider) {
      return new BaseActivity_MembersInjector(supertypeInjector, navigatorProvider);
  }
}
```
这个注入器一般都会为所有`activity`的注入成员提供依赖，只要我们一调用`inject()`方法，就可以获取需要的字段和依赖。

- 第二个重点：关于我们的`DaggerApplicationComponent`类，我们有一个`Provider`，它不仅仅是一个提供实例的接口，它还是被`ScopedProvider`构造出来的，可以记录创建实例的范围。

`Dagger`还会为我们的`Navigator`类生成一个名叫`ApplicationModule_ProvideNavigatorFactory`的工厂，这个工厂可以传递上面提到的范围参数然后得到这个范围内的类的实例。

```java
@Generated("dagger.internal.codegen.ComponentProcessor")
public final class ApplicationModule_ProvideNavigatorFactory implements Factory<Navigator> {
  private final ApplicationModule module;

  public ApplicationModule_ProvideNavigatorFactory(ApplicationModule module) {
    assert module != null;
    this.module = module;
  }

  @Override
  public Navigator get() {
    Navigator provided = module.provideNavigator();
    if (provided == null) {
      throw new NullPointerException("Cannot return null from a non-@Nullable @Provides method");
    }
    return provided;
  }

  public static Factory<Navigator> create(ApplicationModule module) {
    return new ApplicationModule_ProvideNavigatorFactory(module);
  }
}
```
这个类非常简单，它代表我们的`ApplicationModule`（包含`@Provide`方法）创建了`Navigator`类。

总之，上面的代码看起来就像是手敲出来的，而且非常好理解，便于调试。其余还有很多可以去探索，你们可以通过调试去看看Dagger如何完成依赖绑定的。

---
- 邮箱 ：[elisabethzhen@163.com](elisabethzhen@163.com)
- Good Luck!