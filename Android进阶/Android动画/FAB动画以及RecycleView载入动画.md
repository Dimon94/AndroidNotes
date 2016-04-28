## FAB 动画
首先，我们需要把FAB按钮移到屏幕下边去，我在Fragment的`onActivityCreated`方法中加入

```java
mFabButton.setTranslationY(2 * getResources().getDimensionPixelOffset(R.dimen.btn_fab_size));//R.dimen.btn_fab_size =
```

然后我在从网络获取到数据后，开始FAB显示动画

```java
mFabButton.animate()
     .translationY(0)
     .setInterpolator(new OvershootInterpolator(1.f))
     .setStartDelay(500)
     .setDuration(ANIM_DURATION_FAB) //ANIM_DURATION_FAB = 400
     .start();
```

在delay 500秒后开始动画，主要是因为要与列表项的加载动画同步。
使用OvershootInterpolator修饰动画，可以实现在向上移动超出原来位置一定值后，再返回到原位置的效果。

## RecycleView加载动画

列表我是用RecycleView实现，我们需要在Adapter里加入动画处理

在`onBindViewHolder`方法中加入

```java
runEnterAnimation(holder.itemView, position);
```
然后我们实现runEnterAnimation方法

```java
private void runEnterAnimation(View view, int position) {
     if (!animateItems || position >= 3) { //3为屏幕显示Item数，animateItems = false 标志位，更新时变true
        return;
     }

     if (position > lastAnimatedPosition) { //lastAnimatedPosition = -1
     lastAnimatedPosition = position;
     view.setTranslationY(Utils.getScreenHeight(getActivity()));
     view.animate()
         .translationY(0)
         .setStartDelay(100 * position)
         .setInterpolator(new DecelerateInterpolator(3.f))
         .setDuration(700)
         .start();
     }
}
```
列表加载动画，不是所有项都需要的，比如我这个例子中，屏幕上只能显示3个Item，
所以`position >= 3`就不用加入动画了。

先将Item向下移出屏幕
`view.setTranslationY(Utils.getScreenHeight(getActivity()));`

接着根据Item的position设定delay时间，这样可以实现Item一个一个出现,
加入`DecelerateInterpolator` 可以实现动画先快后慢的效果。

### 滚动FAB的消失显示动画
创建一个继承自`FloatingActionButton`名为`ScrollAwareFABBehavior.java`的类。目前FAB默认的Behavior是为Snackbar让出空间，继承这个behavior，就可以处理垂直方向上的滚动事件：

```java
public class ScrollAwareFABBehavior extends FloatingActionButton.Behavior {

    private static final android.view.animation.Interpolator INTERPOLATOR =
            new FastOutSlowInInterpolator();
    private boolean mIsAnimatingOut = false;


    public ScrollAwareFABBehavior(Context context, AttributeSet attrs) {
        super();
    }


    @Override
    public boolean onStartNestedScroll(CoordinatorLayout coordinatorLayout,
                                       FloatingActionButton child, View directTargetChild, View target, int nestedScrollAxes) {
        return nestedScrollAxes == ViewCompat.SCROLL_AXIS_VERTICAL ||
                super.onStartNestedScroll(coordinatorLayout, child, directTargetChild, target,
                        nestedScrollAxes);
    }

    @Override
    public void onNestedScroll(CoordinatorLayout coordinatorLayout, FloatingActionButton child,
                               View target, int dxConsumed, int dyConsumed, int dxUnconsumed, int dyUnconsumed) {
        super.onNestedScroll(coordinatorLayout, child, target, dxConsumed, dyConsumed, dxUnconsumed,
                dyUnconsumed);

        if (dyConsumed > 0 && !this.mIsAnimatingOut && child.getVisibility() == View.VISIBLE) {
            animateOut(child);
        } else if (dyConsumed < 0 && child.getVisibility() != View.VISIBLE) {
            animateIn(child);
        }
    }

    // Same animation that FloatingActionButton.Behavior uses to
    // hide the FAB when the AppBarLayout exits
    private void animateOut(final FloatingActionButton button) {
        if (Build.VERSION.SDK_INT >= 14) {
            ViewCompat.animate(button).scaleX(0.0F).scaleY(0.0F).alpha(0.0F)
                    .setInterpolator(INTERPOLATOR).withLayer()
                    .setListener(new ViewPropertyAnimatorListener() {
                        public void onAnimationStart(View view) {
                            ScrollAwareFABBehavior.this.mIsAnimatingOut = true;
                        }

                        public void onAnimationCancel(View view) {
                            ScrollAwareFABBehavior.this.mIsAnimatingOut = false;
                        }

                        public void onAnimationEnd(View view) {
                            ScrollAwareFABBehavior.this.mIsAnimatingOut = false;
                            view.setVisibility(View.GONE);
                        }
                    }).start();
        } else {
            Animation anim = AnimationUtils.loadAnimation(button.getContext(), R.anim.fab_out);
            anim.setInterpolator(INTERPOLATOR);
            anim.setDuration(200L);
            anim.setAnimationListener(new Animation.AnimationListener() {
                public void onAnimationStart(Animation animation) {
                    ScrollAwareFABBehavior.this.mIsAnimatingOut = true;
                }

                public void onAnimationEnd(Animation animation) {
                    ScrollAwareFABBehavior.this.mIsAnimatingOut = false;
                    button.setVisibility(View.GONE);
                }

                @Override
                public void onAnimationRepeat(final Animation animation) {
                }
            });
            button.startAnimation(anim);
        }
    }

    // Same animation that FloatingActionButton.Behavior
    // uses to show the FAB when the AppBarLayout enters
    private void animateIn(FloatingActionButton button) {
        button.setVisibility(View.VISIBLE);
        if (Build.VERSION.SDK_INT >= 14) {
            ViewCompat.animate(button).scaleX(1.0F).scaleY(1.0F).alpha(1.0F)
                    .setInterpolator(INTERPOLATOR).withLayer().setListener(null)
                    .start();
        } else {
            Animation anim = AnimationUtils.loadAnimation(button.getContext(), R.anim.fab_in);
            anim.setDuration(200L);
            anim.setInterpolator(INTERPOLATOR);
            button.startAnimation(anim);
        }
    }
}

```

## Shared Element Transition

Shared Element Transition是Android 5.0引入的动画效果，可以在两个Activity中实现共享元素变换动画，
在我的这个例子中，就是实现点击Item后，图片可以慢慢放大和移位的效果。

### 设置transitionName

首先，我们需要选定变换开始和结束的元素，给它们设置一个相同的`transitionName`，
我这个例子中，开始变换的元素是列表中的图片

```xml
<ImageView
        android:id="@+id/ivBook"
        android:transitionName="@string/transition_book_img"
        android:layout_width="109dp"
        android:layout_height="135dp"
        android:src="@drawable/book1" />
```

开始变换的元素是图书明细界面中的图片

```xml
<ImageView
          android:id="@+id/ivImage"
          android:layout_width="match_parent"
          android:layout_height="match_parent"
          android:fitsSystemWindows="true"
          android:transitionName="@string/transition_book_img"
          android:scaleType="centerCrop"
          app:layout_collapseMode="parallax"
          app:layout_collapseParallaxMultiplier="0.7" />
```

`transition_book_img`就是一个普通的string，只要值是唯一的就可以了。

### 使用Shared Element Transition

在Activity切换的时候，使用Shared Element Transition

```java
Book book = mAdapter.getBook(position);
Intent intent = new Intent(getActivity(), BookDetailActivity.class);
intent.putExtra("book", book);
ActivityOptionsCompat options =
        ActivityOptionsCompat.makeSceneTransitionAnimation(getActivity(),
                view.findViewById(R.id.ivBook), getString(R.string.transition_book_img));

ActivityCompat.startActivity(getActivity(), intent, options.toBundle());
```

使用`ActivityOptionsCompat`的`makeSceneTransitionAnimation`方法创建动画，
需要指定一个View作为起始变换的View，以及变换的transitionName，
我这里使用的是`ivBook`作为起始View，`transition_book_img`作为变换的transitionName，与XML中的设定一致。

这样就OK了，ImageView的位移和大小变化，Android会自动帮你搞定。
当然我们也可以自定义变换动画，后续再研究~~~

> 这个效果只支持Android 5.0以上