MaterialDesign使用(二)
===

###MaterialList库的运用

>简介：[MaterialList](https://github.com/dexafree/MaterialList)是一个能够帮助所有`Android`开发者获取谷歌UI设计规范中新增的`CardView`（卡片视图）的开源库，支持`Android 2.3+`系统。作为`ListView`的扩展，`MaterialList`可以接收、存储卡片列表，并根据它们的`Android`风格和设计模式进行展示。此外，开发者还可以创建专属于自己的卡片布局，并轻松将其添加到`CardList`中。



- 配置`build.gradle`

```java
dependencies {
    ...
    compile 'com.github.dexafree:materiallist:3.1.3'
}
```
- Step1:在布局文件中声明一个`MaterialListVie`

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin">

    <com.dexafree.materialList.view.MaterialListView
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:id="@+id/material_listview"/>

</RelativeLayout>
```
- Step2: 获取`MaterialListView`实例

```java
MaterialListView mListView = (MaterialListView) findViewById(R.id.material_listview);
```

- Step3：往`MaterialListView`里添加`Cards`

```java
Card card = new Card.Builder(this)
                            .setTag("BASIC_IMAGE_BUTTONS_CARD")
                            .withProvider(BasicImageButtonsCardProvider.class)
                            .setTitle("I'm new")
                            .setDescription("I've been generated on runtime!")
                            .setDrawable(R.drawable.dog)
                            .endConfig()
                            .build()

mListView.add(card);
```
- 实例：

```java
public class MainActivity extends AppCompatActivity {
    private Context mContext;
    private MaterialListView mListView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Save a reference to the context
        mContext = this;

        // Bind the MaterialListView to a variable
        mListView = (MaterialListView) findViewById(R.id.material_listview);
        mListView.setItemAnimator(new SlideInLeftAnimator());
        mListView.getItemAnimator().setAddDuration(300);
        mListView.getItemAnimator().setRemoveDuration(300);

        final ImageView emptyView = (ImageView) findViewById(R.id.imageView);
        emptyView.setScaleType(ImageView.ScaleType.CENTER_INSIDE);
        mListView.setEmptyView(emptyView);
        Picasso.with(this)
                .load("https://www.skyverge.com/wp-content/uploads/2012/05/github-logo.png")
                .resize(100, 100)
                .centerInside()
                .into(emptyView);

        // Fill the array withProvider mock content
        fillArray();

        // Set the dismiss listener
        mListView.setOnDismissCallback(new OnDismissCallback() {
            @Override
            public void onDismiss(@NonNull Card card, int position) {
                // Show a toast
                Toast.makeText(mContext, "You have dismissed a " + card.getTag(), Toast.LENGTH_SHORT).show();
            }
        });

        // Add the ItemTouchListener
        mListView.addOnItemTouchListener(new RecyclerItemClickListener.OnItemClickListener() {
            @Override
            public void onItemClick(@NonNull Card card, int position) {
                Log.d("CARD_TYPE", "" + card.getTag());
            }

            @Override
            public void onItemLongClick(@NonNull Card card, int position) {
                Log.d("LONG_CLICK", "" + card.getTag());
            }
        });

    }

    private void fillArray() {
        List<Card> cards = new ArrayList<>();
        for (int i = 0; i < 35; i++) {
            cards.add(getRandomCard(i));
        }
        mListView.getAdapter().addAll(cards);
    }

    private Card getRandomCard(final int position) {
        String title = "Card number " + (position + 1);
        String description = "Lorem ipsum dolor sit amet";

        switch (position % 7) {
            case 0: {
                return new Card.Builder(this)
                        .setTag("SMALL_IMAGE_CARD")
                        .setDismissible()
                        .withProvider(new CardProvider())
                        .setLayout(R.layout.material_small_image_card)
                        .setTitle(title)
                        .setDescription(description)
                        .setDrawable(R.drawable.sample_android)
                        .setDrawableConfiguration(new CardProvider.OnImageConfigListener() {
                            @Override
                            public void onImageConfigure(@NonNull final RequestCreator requestCreator) {
                                requestCreator.rotate(position * 90.0f)
                                        .resize(150, 150)
                                        .centerCrop();
                            }
                        })
                        .endConfig()
                        .build();
            }
            case 1: {
                return new Card.Builder(this)
                        .setTag("BIG_IMAGE_CARD")
                        .withProvider(new CardProvider())
                        .setLayout(R.layout.material_big_image_card_layout)
                        .setTitle(title)
                        .setSubtitle(description)
                        .setSubtitleGravity(Gravity.END)
                        .setDrawable("https://assets-cdn.github.com/images/modules/logos_page/GitHub-Mark.png")
                        .setDrawableConfiguration(new CardProvider.OnImageConfigListener() {
                            @Override
                            public void onImageConfigure(@NonNull final RequestCreator requestCreator) {
                                requestCreator.rotate(position * 45.0f)
                                        .resize(200, 200)
                                        .centerCrop();
                            }
                        })
                        .endConfig()
                        .build();
            }
            case 2: {
                final CardProvider provider = new Card.Builder(this)
                        .setTag("BASIC_IMAGE_BUTTON_CARD")
                        .setDismissible()
                        .withProvider(new CardProvider<>())
                        .setLayout(R.layout.material_basic_image_buttons_card_layout)
                        .setTitle(title)
                        .setTitleGravity(Gravity.END)
                        .setDescription(description)
                        .setDescriptionGravity(Gravity.END)
                        .setDrawable(R.drawable.dog)
                        .setDrawableConfiguration(new CardProvider.OnImageConfigListener() {
                            @Override
                            public void onImageConfigure(@NonNull RequestCreator requestCreator) {
                                requestCreator.fit();
                            }
                        })
                        .addAction(R.id.left_text_button, new TextViewAction(this)
                                .setText("left")
                                .setTextResourceColor(R.color.black_button)
                                .setListener(new OnActionClickListener() {
                                    @Override
                                    public void onActionClicked(View view, Card card) {
                                        Toast.makeText(mContext, "You have pressed the left button", Toast.LENGTH_SHORT).show();
                                        card.getProvider().setTitle("CHANGED ON RUNTIME");
                                    }
                                }))
                        .addAction(R.id.right_text_button, new TextViewAction(this)
                                .setText("right")
                                .setTextResourceColor(R.color.orange_button)
                                .setListener(new OnActionClickListener() {
                                    @Override
                                    public void onActionClicked(View view, Card card) {
                                        Toast.makeText(mContext, "You have pressed the right button on card " + card.getProvider().getTitle(), Toast.LENGTH_SHORT).show();
                                        card.dismiss();
                                    }
                                }));

                if (position % 2 == 0) {
                    provider.setDividerVisible(true);
                }

                return provider.endConfig().build();
            }
            case 3: {
                final CardProvider provider = new Card.Builder(this)
                        .setTag("BASIC_BUTTONS_CARD")
                        .setDismissible()
                        .withProvider(new CardProvider())
                        .setLayout(R.layout.material_basic_buttons_card)
                        .setTitle(title)
                        .setDescription(description)
                        .addAction(R.id.left_text_button, new TextViewAction(this)
                                .setText("left")
                                .setTextResourceColor(R.color.black_button)
                                .setListener(new OnActionClickListener() {
                                    @Override
                                    public void onActionClicked(View view, Card card) {
                                        Toast.makeText(mContext, "You have pressed the left button", Toast.LENGTH_SHORT).show();
                                    }
                                }))
                        .addAction(R.id.right_text_button, new TextViewAction(this)
                                .setText("right")
                                .setTextResourceColor(R.color.accent_material_dark)
                                .setListener(new OnActionClickListener() {
                                    @Override
                                    public void onActionClicked(View view, Card card) {
                                        Toast.makeText(mContext, "You have pressed the right button", Toast.LENGTH_SHORT).show();
                                    }
                                }));

                if (position % 2 == 0) {
                    provider.setDividerVisible(true);
                }

                return provider.endConfig().build();
            }
            case 4: {
                final CardProvider provider = new Card.Builder(this)
                        .setTag("WELCOME_CARD")
                        .setDismissible()
                        .withProvider(new CardProvider())
                        .setLayout(R.layout.material_welcome_card_layout)
                        .setTitle("Welcome Card")
                        .setTitleColor(Color.WHITE)
                        .setDescription("I am the description")
                        .setDescriptionColor(Color.WHITE)
                        .setSubtitle("My subtitle!")
                        .setSubtitleColor(Color.WHITE)
                        .setBackgroundColor(Color.BLUE)
                        .addAction(R.id.ok_button, new WelcomeButtonAction(this)
                                .setText("Okay!")
                                .setTextColor(Color.WHITE)
                                .setListener(new OnActionClickListener() {
                                    @Override
                                    public void onActionClicked(View view, Card card) {
                                        Toast.makeText(mContext, "Welcome!", Toast.LENGTH_SHORT).show();
                                    }
                                }));

                if (position % 2 == 0) {
                    provider.setBackgroundResourceColor(android.R.color.background_dark);
                }

                return provider.endConfig().build();
            }
            case 5: {
                ArrayAdapter<String> adapter = new ArrayAdapter<>(this, android.R.layout.simple_list_item_1);
                adapter.add("Hello");
                adapter.add("World");
                adapter.add("!");

                return new Card.Builder(this)
                        .setTag("LIST_CARD")
                        .setDismissible()
                        .withProvider(new ListCardProvider())
                        .setLayout(R.layout.material_list_card_layout)
                        .setTitle("List Card")
                        .setDescription("Take a list")
                        .setAdapter(adapter)
                        .endConfig()
                        .build();
            }
            default: {
                final CardProvider provider = new Card.Builder(this)
                        .setTag("BIG_IMAGE_BUTTONS_CARD")
                        .setDismissible()
                        .withProvider(new CardProvider())
                        .setLayout(R.layout.material_image_with_buttons_card)
                        .setTitle(title)
                        .setDescription(description)
                        .setDrawable(R.drawable.photo)
                        .addAction(R.id.left_text_button, new TextViewAction(this)
                                .setText("add card")
                                .setTextResourceColor(R.color.black_button)
                                .setListener(new OnActionClickListener() {
                                    @Override
                                    public void onActionClicked(View view, Card card) {
                                        Log.d("ADDING", "CARD");

                                        mListView.getAdapter().add(generateNewCard());
                                        Toast.makeText(mContext, "Added new card", Toast.LENGTH_SHORT).show();
                                    }
                                }))
                        .addAction(R.id.right_text_button, new TextViewAction(this)
                                .setText("right button")
                                .setTextResourceColor(R.color.accent_material_dark)
                                .setListener(new OnActionClickListener() {
                                    @Override
                                    public void onActionClicked(View view, Card card) {
                                        Toast.makeText(mContext, "You have pressed the right button", Toast.LENGTH_SHORT).show();
                                    }
                                }));

                if (position % 2 == 0) {
                    provider.setDividerVisible(true);
                }

                return provider.endConfig().build();
            }
        }
    }

    private Card generateNewCard() {
        return new Card.Builder(this)
                .setTag("BASIC_IMAGE_BUTTONS_CARD")
                .withProvider(new CardProvider())
                .setLayout(R.layout.material_basic_image_buttons_card_layout)
                .setTitle("I'm new")
                .setDescription("I've been generated on runtime!")
                .setDrawable(R.drawable.dog)
                .endConfig()
                .build();
    }

    private void addMockCardAtStart() {
        mListView.getAdapter().addAtStart(new Card.Builder(this)
                .setTag("BASIC_IMAGE_BUTTONS_CARD")
                .setDismissible()
                .withProvider(new CardProvider())
                .setLayout(R.layout.material_basic_image_buttons_card_layout)
                .setTitle("Hi there")
                .setDescription("I've been added on top!")
                .addAction(R.id.left_text_button, new TextViewAction(this)
                        .setText("left")
                        .setTextResourceColor(R.color.black_button))
                .addAction(R.id.right_text_button, new TextViewAction(this)
                        .setText("right")
                        .setTextResourceColor(R.color.orange_button))
                .setDrawable(R.drawable.dog)
                .endConfig()
                .build());
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.main, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case R.id.action_clear:
                mListView.getAdapter().clearAll();
                break;
            case R.id.action_add_at_start:
                addMockCardAtStart();
                break;
        }
        return super.onOptionsItemSelected(item);
    }
}
```
---
- 邮箱 ：elisabethzhen@163.com
- Good Luck!
