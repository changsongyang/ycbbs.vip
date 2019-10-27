## 引言
`Java 9` 引入了一种新的多分辨率图像 `API`，它支持具有不同分辨率变体的多个图像

这些 `API` 允许将具有不同分辨率的一组图像用作单个多分辨率图像

<table> 
 <thead> 
  <tr> 
   <th align="left">方法</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">getResolutionVariant(double destImageWidth, double destImageHeight)</td> 
   <td align="left">获取特定图像，该图像是表示指定大小的逻辑图像的最佳变体</td> 
  </tr> 
  <tr> 
   <td align="left">getResolutionVariants()</td> 
   <td align="left">以可读列表的形式返回所有分辨率变体</td> 
  </tr> 
 </tbody> 
</table>

接下来我们就来看看两个 API 如何使用吧。

假设存在三张图片
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Java9/Java9-image/img_1.png)
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Java9/Java9-image/img_2.png)
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Java9/Java9-image/img_3.png)

它们的地址分别为

```
https://tech.souyunku.com/static/upload/img/2018/09/03/20180903060845_4.png

https://tech.souyunku.com/static/upload/img/2018/09/03/20180903060900_4.png

https://tech.souyunku.com/static/upload/img/2018/09/03/20180903060914_4.png
```

在当前的工作区中创建一个文件 `MultiResolutionTester.java` 并输入以下内容

```
import java.io.IOException;
import java.net.URL;
import java.net.MalformedURLException;
import java.util.ArrayList;
import java.util.List;
import java.awt.Image;
import java.awt.image.MultiResolutionImage;
import java.awt.image.BaseMultiResolutionImage;

import javax.imageio.ImageIO;

public class MultiResolutionTester {
   public static void main(String[] args) throws IOException, MalformedURLException {

      List<String> imgUrls = List.of("https://tech.souyunku.com/static/upload/img/2018/09/03/20180903060845_4.png",
         "https://tech.souyunku.com/static/upload/img/2018/09/03/20180903060900_4.png",
         "https://tech.souyunku.com/static/upload/img/2018/09/03/20180903060914_4.png");

      List<Image> images = new ArrayList<Image>();

      for (String url : imgUrls) {
         images.add(ImageIO.read(new URL(url)));
      }

      // read all images into one multiresolution image
      MultiResolutionImage multiResolutionImage = 
         new BaseMultiResolutionImage(images.toArray(new Image[0]));

      // get all variants of images
      List<Image> variants = multiResolutionImage.getResolutionVariants();

      System.out.println("Total number of images: " + variants.size());

      for (Image img : variants) {
         System.out.println(img);
      }

      // get a resolution-specific image variant for each indicated size
      Image variant1 = multiResolutionImage.getResolutionVariant(156, 45);
      System.out.printf("\nImage for destination[%d,%d]: [%d,%d]", 
         156, 45, variant1.getWidth(null), variant1.getHeight(null));

      Image variant2 = multiResolutionImage.getResolutionVariant(311, 89);
      System.out.printf("\nImage for destination[%d,%d]: [%d,%d]", 311, 89, 
         variant2.getWidth(null), variant2.getHeight(null));

      Image variant3 = multiResolutionImage.getResolutionVariant(622, 178);
      System.out.printf("\nImage for destination[%d,%d]: [%d,%d]", 622, 178, 
         variant3.getWidth(null), variant3.getHeight(null));

      Image variant4 = multiResolutionImage.getResolutionVariant(300, 300);
      System.out.printf("\nImage for destination[%d,%d]: [%d,%d]", 300, 300, 
         variant4.getWidth(null), variant4.getHeight(null));
   }  
}
```

运行结果如下

```
[penglei@www.ycbbs.vip java9]$ javac MultiResolutionTester.java && java MultiResolutionTester
Total number of images: 3
BufferedImage@c267ef4: type = 6 ColorModel: #pixelBits = 32 numComponents = 4 color space = java.awt.color.ICC_ColorSpace@32eff876 transparency = 3 has alpha = true isAlphaPre = false ByteInterleavedRaster: width = 32 height = 32 #numDataElements 4 dataOff[0] = 3
BufferedImage@9f116cc: type = 6 ColorModel: #pixelBits = 32 numComponents = 4 color space = java.awt.color.ICC_ColorSpace@32eff876 transparency = 3 has alpha = true isAlphaPre = false ByteInterleavedRaster: width = 64 height = 64 #numDataElements 4 dataOff[0] = 3
BufferedImage@12468a38: type = 6 ColorModel: #pixelBits = 32 numComponents = 4 color space = java.awt.color.ICC_ColorSpace@32eff876 transparency = 3 has alpha = true isAlphaPre = false ByteInterleavedRaster: width = 128 height = 128 #numDataElements 4 dataOff[0] = 3

Image for destination[156,45]: [128,128]
Image for destination[311,89]: [128,128]
Image for destination[622,178]: [128,128]
Image for destination[300,300]: [128,128]
```

可以看到，这些 API 还会非常的有趣的


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")