# [Best practice for instantiating a new Android Fragment](https://stackoverflow.com/questions/9245408/best-practice-for-instantiating-a-new-android-fragment)
## https://stackoverflow.com/a/9245510
If Android decides to recreate your Fragment later, it's going to call the no-argument constructor of your fragment. So overloading the constructor is not a solution.

With that being said, the way to pass stuff to your Fragment so that they are available after a Fragment is recreated by Android is to pass a bundle to the setArguments method.

So, for example, if we wanted to pass an integer to the fragment we would use something like:

```
public static MyFragment newInstance(int someInt) {
    MyFragment myFragment = new MyFragment();

    Bundle args = new Bundle();
    args.putInt("someInt", someInt);
    myFragment.setArguments(args);

    return myFragment;
}
```

And later in the Fragment onCreate() you can access that integer by using:

```
getArguments().getInt("someInt", 0);
```

This Bundle will be available even if the Fragment is somehow recreated by Android.

Also note: setArguments can only be called before the Fragment is attached to the Activity.

This approach is also documented in the android developer reference: https://developer.android.com/reference/android/app/Fragment.html
