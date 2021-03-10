# View绘制原理

## Activity DecorView WidnowPhone之间关系

```
//Activity
 /**
     * Set the activity content from a layout resource.  The resource will be
     * inflated, adding all top-level views to the activity.
     *
     * @param layoutResID Resource ID to be inflated.
     *
     * @see #setContentView(android.view.View)
     * @see #setContentView(android.view.View, android.view.ViewGroup.LayoutParams)
     */
    public void setContentView(@LayoutRes int layoutResID) {
        getWindow().setContentView(layoutResID);
        initWindowDecorActionBar();
    }
    
     public Window getWindow() {
        return mWindow;
    }
```


View的创建：
```
//Activity
private Window mWindow;

final void attach(Context context, ActivityThread aThread,····） {
        attachBaseContext(context);
        mFragments.attachHost(null /*parent*/);
        mWindow = new PhoneWindow(this, window, activityConfigCallback);
}
```

setContentView
```
//PhoneWindow
  @Override
    public void setContentView(int layoutResID) {
        if (mContentParent == null) {
            installDecor();
        } else if (!hasFeature(FEATURE_CONTENT_TRANSITIONS)) {
            mContentParent.removeAllViews();
        }

        if (hasFeature(FEATURE_CONTENT_TRANSITIONS)) {
            final Scene newScene = Scene.getSceneForLayout(mContentParent, layoutResID,
                    getContext());
            transitionTo(newScene);
        } else {
            mLayoutInflater.inflate(layoutResID, mContentParent);
        }
    }

```

installDecor

```
//PhoneWindow
 // This is the view in which the window contents are placed. It is either
    // mDecor itself, or a child of mDecor where the contents go.
  ViewGroup mContentParent;
  
  private DecorView mDecor;
  
  private void installDecor() {
        mForceDecorInstall = false;
        if (mDecor == null) {
            mDecor = generateDecor(-1);
        } else {
            mDecor.setWindow(this);
        }
        if (mContentParent == null) {
           mContentParent = generateLayout(mDecor);
        }
  }

  protected DecorView generateDecor(int featureId) {
        return new DecorView(context, featureId, this, getAttributes());
    }

```
### 三者关系
* Activity包含了一个PhoneWindow，
* PhoneWindow就是继承于Window
* Activity通过setContentView将View设置到了PhoneWindow上
* PhoneWindow里面包含了DecorView，最终布局被添加到Decorview上.


## ViewRootImpl,WindowManager,WindowManagerService(WMS)之间的关系

## 屏幕绘制机制

## 双缓冲技术

### Choreographer

### VSync

## performMeasure 与measure onMeasure区别

## layout invalidate 执行后的一些流程区别
