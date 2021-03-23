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

DecorView是怎么被添加到系统的Framework层

当Activity准备好后，最终会调用到Activity中的makeVisible，并通过WindowManager添加View,代码如下：
```
//Activity 
 void makeVisible() {
        if (!mWindowAdded) {
            ViewManager wm = getWindowManager();
            wm.addView(mDecor, getWindow().getAttributes());
            mWindowAdded = true;
        }
        mDecor.setVisibility(View.VISIBLE);
    }
```
(下面提到到客户端服务端是Binder通讯中的客户端服务端概念. )  

* ViewRootImpl(客户端):View中持有与WMS链接的mAttachInfo，mAttachInfo持有ViewRootImpl.ViewRootImpl是ViewRoot的的实现,WMS管理窗口时，需要通知客户端进行某种操作，比如事件响应等.ViewRootImpl有个内部类W,W继承IWindow.Stub，实则就是一个Binder，他用于和WMS IPC交互。ViewRootHandler也是其内部类继承Handler，用于与远程IPC回来的数据进行异步调用.


* WindowManger(客户端):客户端需要创建一个窗口，而具体创建窗口的任务是由WMS完成,WindowManger就像一个部门经理，谁有什么需求就告诉它，它和WMS交互，客户端不能直接和WMS交互.


* WindowManagerService(WMS)(服务端):负责窗口的创建,显示等.


## 屏幕绘制机制

## 双缓冲技术

### Choreographer

### VSync

## performMeasure 与measure onMeasure区别

## layout invalidate 执行后的一些流程区别

View重绘和更新可以使用invalidate()和requestLayout()方法，其主要区别如下：

invalidate()方法只会执行onDraw方法  
requestLayout()只会执行onMeasure方法和onLayout方法，并不会执行onDraw方法  
所以当我们进行View更新时，若仅View的显示内容发生改变且新显示内容不影响View的大小、位置，则只需调用invalidate()方法；若View宽高、位置发生改变且显示内容不变，只需调用requestLayout()方法；若两者均发生改变，则需调用两者，按照View的绘制流程，推荐先调用requestLayout()方法再调用invalidate()方法。

与invalidate()方法类似的还有一个postInvalidate()，两者作用都是刷新View，区别在于：

invalidate方法用于UI线程中重新刷新View  
postInvalidate方法用于非UI线程中重新刷新View，这里借助了ViewRootHandler来达成目的  
