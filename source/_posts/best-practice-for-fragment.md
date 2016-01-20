title: Fragment 最佳实践
date: 2016-01-20 16:13:38
tags:
- android
---
##初始化 Fragment
对于不需要接收参数的`Fragment`，只需要实现
```java
public View onCreateView (LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
```
方法即可

##传参给 Fragment
`Fragment`需要提供一个静态的创建方法, 通常叫做`newInstance`
```java
public class DemoFragment extends Fragment {
    public static DemoFragment newInstance(String someTitle) {
        DemoFragment demoFragment = new DemoFragment();
        args.putString("someTitle", someTitle);
        demoFragment.setArguments(args);
        return demoFragment;
    }
    
    ...
}
```
你可以这样获取参数
```java
public class DemoFragment extends Fragment {
    ... 
    
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
       // Get arguments
       String someTitle = getArguments().getString("someTitle", "");
    }
    
    ...
}
```
你可以这样来调用`DemoFragment`
```java
DemoFragment.newInstance("测试").show(getSupportFragmentManager(), "DemoFragment");
```

##Activity 与 Fragment 之间的通讯
###方法1
在`Fragment`里通过`getActivity()`来直接调用`Activity`里面的方法
例如
```java
((MainActivity) getActivity()).doPositiveClick();
```

###方法2
`Activity`通过实现`Fragment`里的接口来与`Fragment`进行交互
例如

####第一步
`NewsItemFragment`定义`OnNewsItemSelectedListener`接口

```java
public class NewsItemFragment extends Fragment {
    OnNewsItemSelectedListener mCallback;

    // Container Activity must implement this interface
    public interface OnNewsItemSelectedListener {
        public void onNewsItemSelected(int position);
    }
    
    @Override
    public void onAttach(Activity activity) {
        super.onAttach(activity);
        
        // This makes sure that the container activity has implemented
        // the callback interface. If not, it throws an exception
        try {
            mCallback = (OnNewsItemSelectedListener) activity;
        } catch (ClassCastException e) {
            throw new ClassCastException(activity.toString()
                    + " must implement OnNewsItemSelectedListener");
        }
    }
    
    ...
}
```

####第二步
`NewsItemFragment`调用`onNewsItemSelected`方法

```java
Override
public void onListItemClick(ListView l, View v, int position, long id) {
    // Send the event to the host activity
    mCallback.onNewsItemSelected(position);
}
```

####第三步
`MainActivity`里实现`OnNewsItemSelectedListener`接口

```java
public class MainActivity extends AppCompatActivity implements NewsItemFragment.OnNewsItemSelectedListener
{
    ...
    
    @Override
    public void onNewsItemSelected(int position)
    {
        //Do something with the position value passed back
        Toast.makeText(activity, "Clicked "+ position, Toast.LENGTH_LONG).show();
        
    }
}
```
方法2比方法1更复杂，但是在某些情况下可能必须要这样实现

##注意
* `Fragment`之间不可直接进行通讯，需通过`Activity`来控制

##参考文档
* [correctly creating fragments](https://plus.google.com/+AndroidDevelopers/posts/bCD7Zvd945d)
* [Best practice for instantiating a new Android Fragment](http://stackoverflow.com/questions/9245408/best-practice-for-instantiating-a-new-android-fragment/9245510#9245510)
* [Communicating between a fragment and an activity - best practices](http://stackoverflow.com/questions/14247954/communicating-between-a-fragment-and-an-activity-best-practices)
* [Communicating with the Activity](http://developer.android.com/guide/components/fragments.html#CommunicatingWithActivity)
* [implementing Alert Dialog](http://developer.android.com/reference/android/app/DialogFragment.html#AlertDialog)
* [Communicating with Other Fragments](http://developer.android.com/training/basics/fragments/communicating.html)
