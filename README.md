title: ImageViewWithFullPic
date: 2015-12-28 17:46:59
categories:
- Android
tags: 
- android 
- pic_full_scale


---
### 概述
&nbsp;&nbsp;需求如下:

&nbsp;&nbsp;&nbsp;&nbsp;图片尺寸是不确定的,要动态根据图片尺寸展示,并且图片的宽就是屏幕的宽度,图片的长度要根据比例展示,这样才能不失真.
     
     
### 代码示例
&nbsp;&nbsp;最开始的时候觉得其实根据ImageView 的scaleType设置就能搞定,结果就是:

```
   <ImageView>
   xmlns:android="http://schemas.android.com/apk/res/android"
            android:id="@+id/imageView"
            android:scaleType="centerCrop"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"/>
```
结果发现当图片特别长的时候,发现头部会有一部分没有展示,尤其是当图片在广告机这种大屏尺寸,尤为明显.

```
public static final ImageView.ScaleType  CENTER_CROP

均衡的缩放图像（保持图像原始比例），使图片的两个坐标（宽、高）都大于等于 相应的视图坐标（负的内边距）。图像则位于视图的中央。 在 XML 中可以使用的语法：
```
即便改成CENTER_INSIDE/CENTER就会图片变小,达不到想要的效果.

![image](http://7vihs8.com1.z0.glb.clouddn.com/image1.png)

#### 改善
  后来忍无可忍了,那就只有动态计算图片的尺寸和屏幕尺寸,保证图片的宽和屏幕的宽一致,动态给ImageView height赋值.

 由于我目前将图片放到ListView中,所以以ListView的Adapter为示例代码.

layout文件: 注意是scaleType="fitXY"

 ```
    <ImageView>
     xmlns:android="http://schemas.android.com/apk/res/android"
            android:id="@+id/imageView"
            android:scaleType="fitXY"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"/>
 ```

Adapter:

```

    holder = new ViewHolder();
            convertView = inflater.inflate(R.layout.prodct_detail_lv_item, null);
            holder.imageView = (ImageView) convertView.findViewById(R.id.imageView);
            convertView.setTag(holder);
        } else {
            holder = (ViewHolder) convertView.getTag();
        }

        Photol photol = (Photol) getItem(position);
        if (photol != null) {
            if (photol != null) {
                final ViewHolder finalHoder = holder;
                ImageLoader.getInstance().displayImage(photol.getUrl(), holder.imageView, new SimpleImageLoadingListener() {
                    @Override
                    public void onLoadingComplete(String imageUri, View view, Bitmap loadedImage) {
                        super.onLoadingComplete(imageUri, view, loadedImage);
                        if (loadedImage.getWidth() > 0) {
                            Bitmap BitmapOrg = loadedImage;
                            int width = BitmapOrg.getWidth();
                            int height = BitmapOrg.getHeight();


                            AbsListView.LayoutParams params = new AbsListView.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT);
                            float temp_height = height * (im_width / (width + 0.0f));

                            int im_height = Math.round(temp_height);
                            params.width = im_width;
                            params.height = im_height;
                            finalHoder.imageView.setLayoutParams(params);

                        }


                    }
                });

            }
        }


        return convertView;
    }
```
这里使用ImageLoader这个框架获取网络图片,如果使用的是资源文件或者sdcard中的本地文件.可以使用以下方式:

```
   // 给定的BitmapFactory设置解码的参数
        final BitmapFactory.Options options = new BitmapFactory.Options();
        // 从解码器中获取原始图片的宽高，这样避免了直接申请内存空间
        options.inJustDecodeBounds = true;
        //从资源文件中
        BitmapFactory.decodeResource(res, resId, options);
        //从sdcard中
        // BitmapFactory.decodeFile("/sdcard/pic.png", options);

        // Calculate inSampleSize
        options.inSampleSize = calculateInSampleSize(options, reqWidth,
                reqHeight);

        // 压缩完后便可以将inJustDecodeBounds设置为false了。
        options.inJustDecodeBounds = false;
        return BitmapFactory.decodeResource(res, resId, options);

```
即达成如下效果:
![image](http://7vihs8.com1.z0.glb.clouddn.com/image2.png)

Done!


Welcome To My Website
---

[http://androidso.com](http://androidso.com)
--- 