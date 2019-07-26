---
title: Camera拍照
date: 2018-12-28 12:17:46
categories: Android
---
&emsp;&emsp;`MainActivity.java`如下：

``` java
import android.content.Intent;
import android.hardware.Camera;
import android.os.Bundle;
import android.app.Activity;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;
​
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
​
public class MainActivity extends Activity {
    private Camera camera = null; /* 声明一个Camera对象 */
    SurfaceView surfaceView = null; /* 用于显示摄像头预览的区域 */
    SurfaceHolder surfaceHolder = null; /* 获取SurfaceHolder */
​
    private SurfaceHolder.Callback cpHolderCallback = new SurfaceHolder.Callback() {
        @Override
        public void surfaceCreated(SurfaceHolder holder) {
            camera = Camera.open(); /* 打开摄像机 */
            try {
                camera.setPreviewDisplay(surfaceHolder);
                camera.setDisplayOrientation(90); /* 让相机旋转90度 */
                camera.startPreview(); /* 开始预览 */
                camera.autoFocus(null); /* 设置自动对焦 */
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
​
        @Override
        public void surfaceChanged(SurfaceHolder holder, int format, int width, int height) {
        }
​
        @Override
        public void surfaceDestroyed(SurfaceHolder holder) {
            camera.stopPreview();
            camera.release();
            camera = null;
        }
    };
​
    private String saveFile(byte[] bytes) { /* 保存临时文件的方法 */
        try {
            File file = File.createTempFile("img", "");
            FileOutputStream fos = new FileOutputStream(file);
            fos.write(bytes);
            fos.flush();
            fos.close();
            return file.getAbsolutePath();
        } catch (IOException e) {
            e.printStackTrace();
        }

        return null;
    }
​
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        surfaceView = (SurfaceView) findViewById(R.id.surfaceView);
        surfaceHolder = surfaceView.getHolder();
        surfaceHolder.addCallback(cpHolderCallback);
        Button button = (Button) findViewById(R.id.button_takephoto);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                camera.takePicture(null, null, new Camera.PictureCallback() {
                    @Override
                    public void onPictureTaken(byte[] data, Camera camera) {
                        String path = null;
                        if ((path = saveFile(data)) != null) {
                            Intent it = new Intent(MainActivity.this, Main3Activity.class);
                            it.putExtra("path", path);
                            startActivity(it);
                        } else {
                            Toast.makeText(MainActivity.this, "图片保存失败", Toast.LENGTH_SHORT).show();
                        }
                    }
                });
            }
        });
    }
}
```

&emsp;&emsp;`Main3Activity.java`如下：

``` java
import android.net.Uri;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.widget.ImageView;
​
import java.io.File;
​
public class Main3Activity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main3);
        ImageView img = new ImageView(this);
        String path = getIntent().getStringExtra("path");
        if (path != null) {
            img.setImageURI(Uri.fromFile(new File(path)));
        }
        setContentView(img);
    }
}
```

还要添加如下权限：

``` xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

---
&emsp;&emsp;修改`SurfaceView`的大小：

``` java
ViewGroup.LayoutParams lp = surfaceView.getLayoutParams();
lp.width = screenWidth / 4;
lp.height = screenHeight / 4;
surfaceView.setLayoutParams(lp);
```