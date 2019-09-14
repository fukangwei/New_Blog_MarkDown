---
title: OpenCV for Android使用相机
date: 2019-02-06 12:03:36
categories: opencv和图像处理
---
&emsp;&emsp;我们想要在应用中通过`OpenCV`的`Java API`实现打开相机，并全屏显示的功能，所以`MainActivity`需要实现`CvCameraViewListener2`接口。一共需要实现三个方法，分别是`onCameraViewStarted`、`onCameraViewStopped`和`onCameraFrame`，而图像处理函数写在`onCameraFrame`函数中。
&emsp;&emsp;修改文件`AndroidManifest.xml`，添加相机的相关权限：

``` xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera" android:required="false" />
<uses-feature android:name="android.hardware.camera.autofocus" android:required="false" />
```

设置应用的界面主题为没有顶部标题栏且全屏显示，修改`application`标签：

``` xml
<application
    android:allowBackup="true"
    android:label="@string/app_name"
    android:theme="@android:style/Theme.NoTitleBar.Fullscreen" >
```

打开`res/layout`下面的布局文件`activity_main.xml`，添加一个`OpenCV`的视觉组件`JavaCameraView`：

``` xml
<org.opencv.android.JavaCameraView
    android:id="@+id/image_manipulations_activity_surface_view"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" />
```

&emsp;&emsp;回到`MainActivity`中，完成`API`的调用。声明一个`CameraBridgeViewBase`对象，用于存放`activity_main.xml`中的`JavaCameraView`组件，并在`OnCreate`中绑定事件监听：

``` java
mOpenCvCameraView = (CameraBridgeViewBase) findViewById(R.id.image_manipulations_activity_surface_view);
mOpenCvCameraView.setVisibility(CameraBridgeViewBase.VISIBLE);
mOpenCvCameraView.setCvCameraViewListener(this);
```

编写图像处理函数`onCameraFrame`，这个函数在相机刷新每一帧都会调用一次，而且每次的输入参数就是当前相机视图信息，我们直接获取其中的`RGBA`信息，作为`Mat`数据返回给显示组件即可：

``` java
/* 图像处理函数都写在这里 */
public Mat onCameraFrame(CameraBridgeViewBase.CvCameraViewFrame inputFrame) {
    mRgba = inputFrame.rgba(); /* 直接返回输入视频预览图的RGB数据，并存放在Mat数据中 */
    return mRgba;
}
```

我们在`OnCreate`函数中已经获取到`mCVCamera`对象，只有在调用`mCVCamera.enableView`之后，预览组件才会显示每一帧的`Mat`图像。但是在显示之前，我们必须先确保`OpenCV`的库文件已经加载完成，所以调用此方法需要进行异步处理：

``` java
/* 通过OpenCV管理Android服务，异步初始化OpenCV */
private BaseLoaderCallback mLoaderCallback = new BaseLoaderCallback(this) {
    @Override
    public void onManagerConnected(int status) {
        switch (status) {
            case LoaderCallbackInterface.SUCCESS: {
                Log.i(TAG, "OpenCV loaded successfully");
                mOpenCvCameraView.enableView();
            }
            break;
            default: {
                super.onManagerConnected(status);
            }
            break;
        }
    }
};
```

所以只有当`mLoaderCallback`收到`LoaderCallbackInterface.SUCCESS`消息的时候，才会打开预览显示。那么这个消息是从哪里发出来的呢？这就需要我们重写`Activity`的`onRusume`方法了，因为每次当前`Activity`激活都会调用此方法，所以可以在此处检测`OpenCV`的库文件是否加载完毕：

``` java
public void onResume() {
    super.onResume();

    if (getRequestedOrientation() != ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE) { /* 强制横屏 */
        setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE);
    } else { /* 横屏后才加载部件 */
        if (!OpenCVLoader.initDebug()) {
            Log.d(TAG, "Internal OpenCV library not found. Using OpenCV Manager for initialization");
            OpenCVLoader.initAsync(OpenCVLoader.OPENCV_VERSION_3_0_0, this, mLoaderCallback);
        } else {
            Log.d(TAG, "OpenCV library found inside package. Using it!");
            mLoaderCallback.onManagerConnected(LoaderCallbackInterface.SUCCESS);
        }
    }
}
```

完整的代码如下：

``` java
import org.opencv.android.BaseLoaderCallback;
import org.opencv.android.CameraBridgeViewBase;
import org.opencv.android.LoaderCallbackInterface;
import org.opencv.android.OpenCVLoader;
import org.opencv.core.CvType;
import org.opencv.core.Mat;
​
import android.content.pm.ActivityInfo;
import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
import android.view.WindowManager;
​
public class ImageManipulationsActivity extends Activity implements CameraBridgeViewBase.CvCameraViewListener2 {
    static {
        System.loadLibrary("opencv_java3");
    }
​
    private String TAG = "OpenCV_Test";
    private CameraBridgeViewBase mOpenCvCameraView; /* OpenCV的相机接口 */
    private Mat mRgba; /* 缓存相机每帧输入的数据 */
​
    /* 通过OpenCV管理Android服务，异步初始化OpenCV */
    private BaseLoaderCallback mLoaderCallback = new BaseLoaderCallback(this) {
        @Override
        public void onManagerConnected(int status) {
            switch (status) {
                case LoaderCallbackInterface.SUCCESS: {
                    Log.i(TAG, "OpenCV loaded successfully");
                    mOpenCvCameraView.enableView();
                }
                break;
                default: {
                    super.onManagerConnected(status);
                }
                break;
            }
        }
    };
​
    @Override
    public void onCreate(Bundle savedInstanceState) {
        Log.i(TAG, "called onCreate");
        super.onCreate(savedInstanceState);
        getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
        setContentView(R.layout.image_manipulations_surface_view);
​
        mOpenCvCameraView = (CameraBridgeViewBase) findViewById(R.id.image_manipulations_activity_surface_view);
        mOpenCvCameraView.setVisibility(CameraBridgeViewBase.VISIBLE);
        mOpenCvCameraView.setCvCameraViewListener(this);
    }
​
    @Override
    public void onResume() {
        super.onResume();
​
        if (getRequestedOrientation() != ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE) { /* 强制横屏 */
            setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE);
        } else { /* 横屏后才加载部件 */
            if (!OpenCVLoader.initDebug()) {
                Log.d(TAG, "Internal OpenCV library not found. Using OpenCV Manager for initialization");
                OpenCVLoader.initAsync(OpenCVLoader.OPENCV_VERSION_3_0_0, this, mLoaderCallback);
            } else {
                Log.d(TAG, "OpenCV library found inside package. Using it!");
                mLoaderCallback.onManagerConnected(LoaderCallbackInterface.SUCCESS);
            }
        }
    }
​
    @Override
    protected void onDestroy() {
        if (mOpenCvCameraView != null) {
            mOpenCvCameraView.disableView();
        }

        super.onDestroy();
    }
​
    /* 对象实例化以及基本属性的设置，包括长度、宽度和图像类型 */
    public void onCameraViewStarted(int width, int height) {
        mRgba = new Mat(height, width, CvType.CV_8UC4);
    }
​
    /* 图像处理都写在这里 */
    public Mat onCameraFrame(CameraBridgeViewBase.CvCameraViewFrame inputFrame) {
        mRgba = inputFrame.rgba(); /* 直接返回输入视频预览图的RGB数据，并存放在Mat数据中 */
        return mRgba;
    }
​
    /* 结束时释放资源 */
    public void onCameraViewStopped() {
        mRgba.release();
    }
}
```