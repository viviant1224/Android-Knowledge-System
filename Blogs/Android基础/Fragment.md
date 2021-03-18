# Fragment

![image](https://user-images.githubusercontent.com/7577770/111627459-093e1680-882a-11eb-8599-5490468c1e33.png)

<li>
<strong>onAttach() ：</strong><br>
当Fragment与Activity发生关联时调用。</li>
<li>
<strong>onCreate()：</strong><br>
创建Fragment时被回调。</li>
<li>
<strong>onCreateView()：</strong><br>
每次创建、绘制该Fragment的View组件时回调该方法，Fragment将会显示该方法返回的View 组件。</li>
<li>
<strong>onActivityCreated()：</strong><br>
当 Fragment 所在的Activity被启动完成后回调该方法。</li>
<li>
<strong>onStart()：</strong><br>
启动 Fragment 时被回调，此时Fragment可见。</li>
<li>
<strong>onResume()：</strong><br>
恢复 Fragment 时被回调，获取焦点时回调。</li>
<li>
<strong>onPause()：</strong><br>
暂停 Fragment 时被回调，失去焦点时回调。</li>
<li>
<strong>onStop()：</strong><br>
停止 Fragment 时被回调，Fragment不可见时回调。</li>
<li>
<strong>onDestroyView()：</strong><br>
销毁与Fragment有关的视图，但未与Activity解除绑定。</li>
<li>
<strong>onDestroy()：</strong><br>
销毁 Fragment 时被回调。</li>
<li>
<strong>onDetach()：</strong><br>
与onAttach相对应，当Fragment与Activity关联被取消时调用。</li>
