#Android ListView适配器应该这样写
>文／ALIOUS（简书作者）
原文链接：http://www.jianshu.com/p/61b2841a8ab0?utm_campaign=hugo&utm_medium=reader_share&utm_content=note
著作权归作者所有，转载请联系作者获得授权，并标注“简书作者”。

>ListView是我们开发中很重要的控件，在项目中也用的非常多，为了利用ListView展示数据，我们都需要给它新建一个适配器Adapter，一般继承于BaseAdapter，然后重写一些方法，其中最重要的方法是public View getView(int position, View convertView, ViewGroup parent)，当然我们会依次做View的重用，还会利用ViewHolder缓存已经映射完成的子控件，避免重复查找映射。那么这样做，没有任何问题，但是每次都要写这些重复的代码，既没有效率也消磨我们的耐心，能不能进一步优化封装呢？

###ViewHolderHelper

ViewHolder缓存View的思路我们还是要继续沿用，但是我们希望ViewHolderHelper能够适用所有Adapter，而且我们希望它能有更强大功能，例如能够设置显示的文本，能够设置点击事件等。

```java
public class ViewHolderHelper {

private SparseArray<View> mViews;
private Context mContext;
private int position;
private View mConvertView;
private ViewHolderHelper(Context context, ViewGroup parent, int layoutId, int position){
    this.mContext = context;
    this.position = position;
    this.mViews = new SparseArray<View>();
    this.mConvertView = LayoutInflater.from(mContext).inflate(layoutId, parent, false);
    mConvertView.setTag(this);
}
....
}
```

上面这段代码对一些属性进行初始化，类似于我们常规的setTag(ViewHolder)，我们这里setTag(ViewHolderHelper)，然后还要一个静态的方法供adapter获取可以复用的ViewHolderHelper，这是我们ViewHolder的核心思路。

```java
public static ViewHolderHelper (Context context, View convertView, ViewGroup parent, layoutId , position){
  if (convertView == ){
    return ViewHolderHelper(context, parent, layoutId, position);
  }
  ViewHolderHelper existingHelper = (ViewHolderHelper)convertView.getTag();
  existingHelper.position = position; return existingHelper;
}
```
目前为止就是ViewHolderHelper的缓存和读取，都是常规处理，不赘述。接下来我们需要添加一些方法来设置View的常用属性。那么我们首先要提供一个findViewById方法。

```java
private <T extends View> T findViewById(int viewId){
    View view = mViews.get(viewId);
    if (view == null) {
        view = mConvertView.findViewById(viewId);
        mViews.put(viewId, view);
    }
    return (T)view;
}
```
那么接下来就可以添加类似setText, setBackground(), setImageFromUrl, setOnClickListener()等方法，大家可以根据需要进行扩展。

```java

public ViewHolderHelper setText(int viewId, String value){
    TextView view = findViewById(viewId);
    view.setText(value);
    return this;
}

public ViewHolderHelper setBackground(int viewId, int resId){
    View view = findViewById(viewId);
    view.setBackgroundResource(resId);
    return this;
}
public ViewHolderHelper setImageFromUrl(int viewId, String url){
    ImageView imageView = findViewById(viewId);
    Picasso.with(mContext).load(url).into(imageView);
    return this;
}

public ViewHolderHelper setClickListener(int viewId, View.OnClickListener listener){
    View view = findViewById(viewId);
    view.setOnClickListener(listener);
    return this;
}
```

这里我们所有的`set`方法都返回`ViewHolderHelper`对象本身，这样做的好处就是如果我们要调用多次这样的`set`方法，我们就可以用.把它们连接起来，写法上看起来更简洁。

```java
viewHolderHelper
.setText(R.id.text, "base adapter")
.setBackground(R.id.imageView, R.drawable.default_ic)
.setClickListener(R.id.btn, new View.OnClickListener(){})...
```
还有一个细节，我们存储View与它们的id之间的映射，用的是`SparseArray`类，它比`HashMap`要更高效，大家可以去深挖一下，这里不展开了。

###AdapterBase
我们下面来实现`AdapterBase`，继承与`BaseAdapter`

```java
public abstract class AdapterBase <T> extends BaseAdapter {
    protected final Context mContext;
    protected List<T> mData;
   protected final int [] mLayoutResArrays;

   public AdapterBase(Context context, int [] layoutResArrays){
    this(context, layoutResArrays, null);
   }

   public AdapterBase(Context context, int [] layoutResArrays, List<T> data){
    this.mData = data == null ? new ArrayList<T>() : data;
    this.mContext = context;
    this.mLayoutResArrays = layoutResArrays;
   }

      public void setData(ArrayList<T> data){
    this.mData = data;
    this.notifyDataSetChanged();
  }

  public void addData(ArrayList<T> data){
    if(data != null){
        this.mData.addAll(data);
    }

    this.notifyDataSetChanged();
  }

  public void addData(T data) {
    this.mData.add(data);
    this.notifyDataSetChanged();
  }

  public ArrayList<T> getAllData() {
    return (ArrayList<T>)this.mData;
  }

    @Override
public int getCount() {
    if(this.mData == null){
        return 0;
    }
    return this.mData.size();
}

@Override
public T getItem(int position) {
    if(position > this.mData.size()){
        return null;
    }
    return mData.get(position);
}

@Override
public long getItemId(int position) {
    return position;
}
 ...
}
```
上面都是经常写代码，都能看明白。接下来我们重写`ListAdapter`的分组方法，毕竟还是有很多时候`ListView`的`cell`样式不止一种。

```java
@Override
public int getViewTypeCount() {
    return this.mLayoutResArrays.length;
}

/**
 * You should always override this method,to return the
 * correct view type for every cell.
 *
 */
public int getItemViewType(int position){
    return 0;
}
```
这里`getItemViewType`我们默认返回0 ，实际业务子类需要根据需求进行重写。接下来是最重要的`getView`方法。

```java
   @Override
public View getView(int position, View convertView, ViewGroup parent) {
    final ViewHolderHelper helper = getAdapterHelper(position, convertView, parent);
    T item = getItem(position);
    convert(helper, item);
    return helper.getView();
}

protected abstract void convert(ViewHolderHelper helper, T item);
protected abstract ViewHolderHelper getAdapterHelper(int position, View convertView, ViewGroup parent);
```
`getView`方法里，我们先获取`ViewHolderHelper`对象，然后根据`position`获取数据实体对象，最后调用我们暴露给业务子类的`convert`接口对我们的`ListView cell`进行定制填充。

###总结
有了`AdapterBase`，让我们新建新的`Adapter`子类轻松愉快到没朋友，我们会类似于下面这样的去组织代码，而且也只需要这么多代码。

```java
BaseAdapter listAdapter = new AdapterBase<Bean>(context, new int [] {R.layout.layout1, R.layout.layout2, R.layout.layout3}){

 public int getItemViewType(int position){
    Bean bean = getItem(position);
    return bean.getViewType();
 }
 protected abstract void convert(ViewHolderHelper helper, T item) {
   helper
   .setText(R.id.text, "base adapter")
   .setBackground(R.id.imageView, R.drawable.default_ic)
   .setClickListener(R.id.btn, new View.OnClickListener(){});
 }
 }
 //set listview adapter
 mListView.setAdapter(listAdapter);
 ```
Github上面有类似的成熟项目 https://github.com/JoanZapata/base-adapter-helper ，但是这个项目不支持多样式cell分组， 大家使用时要注意。最后我还是想多说一句，了解开源项目背后的实现原理非常必要，更重要的是能够在原有的基础上进行改进创新，更难能可贵，在这个过程中，我们自己也会得到提升。

---
- 邮箱 ：[elisabethzhen@163.com](elisabethzhen@163.com)
- Good Luck!