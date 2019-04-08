---
title: OpenCV for Android编程
date: 2019-02-06 11:55:28
tags:
---
### 显示图片

&emsp;&emsp;代码如下：

``` java
import org.opencv.android.BaseLoaderCallback;
import org.opencv.android.LoaderCallbackInterface;
import org.opencv.android.OpenCVLoader;
import org.opencv.android.Utils;
import org.opencv.core.Mat;
import org.opencv.imgcodecs.Imgcodecs;
​
import android.graphics.Bitmap;
import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
import android.view.WindowManager;
import android.widget.ImageView;
import android.widget.Toast;
​
public class ImageManipulationsActivity extends Activity {
    static {
        System.loadLibrary("opencv_java3");
    }
​
    private static final String TAG = "OCVSample::Activity";
    private ImageView imageview;
​
    private BaseLoaderCallback mLoaderCallback = new BaseLoaderCallback(this) {
        @Override
        public void onManagerConnected(int status) {
            switch (status) {
                case LoaderCallbackInterface.SUCCESS: {
                    Log.i(TAG, "OpenCV loaded successfully");
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
        imageview = (ImageView) this.findViewById(R.id.imageView);
        Mat img = Imgcodecs.imread("/sdcard/pic2.png", 1); /* 读取图片文件 */
        Bitmap bm = Bitmap.createBitmap(img.cols(), img.rows(), Bitmap.Config.ARGB_8888);
        Utils.matToBitmap(img, bm);
        imageview.setImageBitmap(bm);
​
        boolean flag = Imgcodecs.imwrite("/sdcard/new.jpg", img); /* 写入图片文件 */
​
        if (flag) { /* 如果写入成功 */
            Toast.makeText(this, "Write OK", Toast.LENGTH_SHORT).show();
        }
    }
​
    @Override
    public void onResume() {
        super.onResume();
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

需要添加如下权限：

``` xml
<!-- 在SDCard中创建与删除文件权限 -->
<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>
<!-- 往SDCard写入数据权限 -->
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```

如果想要将以灰度图的形式进行读取，则使用如下语句：

``` java
Mat img = Imgcodecs.imread("/sdcard/pic2.png", 0); /* 读取图片文件 */
```

### Canny边缘检测

&emsp;&emsp;代码如下：

``` java
public void onCreate(Bundle savedInstanceState) {
    Log.i(TAG, "called onCreate");
    super.onCreate(savedInstanceState);
    getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
    setContentView(R.layout.image_manipulations_surface_view);
    imageview = (ImageView) this.findViewById(R.id.imageView);
    Mat img = Imgcodecs.imread("/sdcard/face.jpg", 0); /* 读取图片文件 */
​
    Size dsize = new Size(img.cols(), img.rows());
    Mat img2 = new Mat(dsize, CvType.CV_8SC1);
    Imgproc.Canny(img, img2, 123, 250); /* Canny边缘检测 */
​
    Bitmap bm = Bitmap.createBitmap(img.cols(), img.rows(), Bitmap.Config.ARGB_8888);
    Utils.matToBitmap(img2, bm);
    imageview.setImageBitmap(bm);
}
```