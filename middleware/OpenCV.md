## Open CV

### Java CV
JavaCV不仅支持轮廓绘制和检测，还提供了许多其他的功能和工具。以下是一些JavaCV的主要功能：

- 图像处理：JavaCV支持各种图像处理算法和滤镜，如模糊、锐化、色彩空间转换等。这些功能使得开发者能够轻松地对图像进行各种处理和变换。
- 特征检测和描述符：JavaCV支持多种特征检测算法，如SIFT、SURF、ORB等，并能够生成相应的描述符。这些功能在物体识别、图像匹配等应用中非常有用。
- 对象检测和跟踪：JavaCV可以应用于各种对象检测和跟踪任务，例如人脸检测、行人跟踪等。通过使用JavaCV，开发者可以构建实时或离线的对象检测和跟踪系统。
- 3D重建和可视化：JavaCV支持从多个图像中重建三维场景，并可以进行可视化处理。这在计算机视觉领域的许多应用中都非常有用，如增强现实、虚拟现实等。
- 机器学习和模式识别：JavaCV支持各种机器学习和模式识别算法，如支持向量机、朴素贝叶斯分类器、决策树等。这使得JavaCV在图像分类、识别等任务中非常有用。
- 视频捕获和播放：JavaCV可以轻松地从摄像头或视频文件中捕获和播放视频。这使得JavaCV在实时视频处理、监控系统等领域非常受欢迎。
- 跨平台性：JavaCV是基于Java的，因此它可以在各种操作系统上运行，包括Windows、Linux和Mac OS等。这为开发者提供了极大的灵活性，使得他们可以在不同的平台上使用相同的代码库。
- 多线程支持：JavaCV支持多线程，可以充分利用多核CPU的性能，提高处理速度和效率。
- 良好的文档和社区支持：JavaCV有详细的文档和活跃的社区，可以帮助开发者解决各种问题，并提供技术支持。
请注意，为了充分利用JavaCV的这些功能，开发者需要具备一定的计算机视觉和图像处理的基础知识，以便正确地使用和调整各种算法和参数。此外，由于技术不断进步和更新，建议开发者在使用JavaCV时查阅最新的文档和教程，以确保获得最佳的性能和最新的功能。


### 人脸比对
流程说明：

加载人脸检测模型：用于从图像中检测出人脸的位置。
加载人脸特征提取模型：用于从检测到的人脸中提取特征。
读取待比对图像：加载需要进行人脸比对的图像。
检测并提取待比对图像中的人脸特征：使用加载的检测和特征提取模型对图像进行处理。
读取目标图像：加载目标图像，即与待比对图像进行比对的另一张图像。
检测并提取目标图像中的人脸特征：同样使用加载的模型处理目标图像。
比对人脸特征：比较从待比对图像和目标图像中提取的人脸特征，判断它们是否匹配。

```
import org.bytedeco.javacv.*;
import org.bytedeco.opencv.opencv_core.*;
import org.bytedeco.opencv.opencv_face.*;
import static org.bytedeco.opencv.global.opencv_core.*;
import static org.bytedeco.opencv.global.opencv_face.*;
import static org.bytedeco.opencv.global.opencv_imgproc.*;

public class FaceComparisonExample {
    public static void main(String[] args) {
        // 1. 加载人脸检测模型和人脸特征提取模型
        CascadeClassifier faceDetector = new CascadeClassifier("path/to/haarcascade_frontalface_default.xml");
        FaceRecognizer faceRecognizer = LBPHFaceRecognizer.create();
        faceRecognizer.read("path/to/face_recognizer_model.xml");
        
        // 2. 读取待比对图像
        Mat imageToCompare = imread("path/to/image_to_compare.jpg");
        
        // 3. 检测并提取待比对图像中的人脸特征
        MatOfRect faceDetections = new MatOfRect();
        faceDetector.detectMultiScale(imageToCompare, faceDetections);
        for (Rect rect : faceDetections.toArray()) {
            Mat faceImage = new Mat(imageToCompare, rect);
            Mat faceFeatures = new Mat();
            faceRecognizer.predict(faceImage, faceFeatures);
            // TODO: 保存或处理faceFeatures，例如存储为一个向量
        }
        
        // 4. 读取目标图像并进行相似操作...
        
        // 5. 比对人脸特征
        // 根据从待比对图像和目标图像中提取的特征进行比对，这里需要实现具体的比对逻辑。
        // 可能涉及计算特征之间的欧氏距离或其他度量标准，并根据阈值判断是否匹配。
        
        // 注意：实际使用中需要添加资源释放逻辑
        faceDetector.release();
        faceRecognizer.release();
    }
}
```

### 训练人脸识别器
在OpenCV中训练人脸识别器通常涉及以下步骤：

收集数据集：首先，你需要一个包含人脸图像的数据集。每个图像应该是一个人的一张脸，并且所有图像应该已经过适当的预处理（例如，裁剪、缩放和归一化）。此外，每个图像都应该与一个唯一的标签（例如，人的ID或名字）相关联。

创建标签和样本：然后，你需要将这些图像和它们的标签转换成OpenCV可以处理的格式。通常，这意味着你需要创建一个包含所有图像样本的数组（或矩阵），以及一个包含相应标签的数组。

训练人脸识别器：使用OpenCV提供的训练函数和算法来训练人脸识别器。OpenCV支持多种人脸识别算法，包括Eigenfaces、Fisherfaces和Local Binary Patterns Histograms (LBPH)。

保存和加载模型：一旦模型训练完成，你可以将其保存为XML文件，以便将来可以在不需要重新训练的情况下加载和使用它。

```
import org.opencv.core.*;
import org.opencv.face.FaceRecognizer;
import org.opencv.face.LBPHFaceRecognizer;
import org.opencv.imgcodecs.Imgcodecs;
import org.opencv.imgproc.Imgproc;

import java.util.ArrayList;
import java.util.List;

public class FaceRecognizerTrainer {
    static { System.loadLibrary(Core.NATIVE_LIBRARY_NAME); }

    public static void main(String[] args) {
        // 假设你已经有了一个包含人脸图像和对应标签的列表
        List<Mat> images = new ArrayList<>();
        List<Integer> labels = new ArrayList<>();

        // 加载图像和标签
        // 这里只是示意，你需要替换成实际的图像加载和标签设置代码
        for (int i = 0; i < 100; i++) {
            String imagePath = "path_to_image_" + i + ".jpg";
            Mat image = Imgcodecs.imread(imagePath);
            if (!image.empty()) {
                // 假设所有图像都已经是合适的大小，例如64x64
                images.add(image);
                labels.add(i); // 假设标签是从0开始的整数
            }
        }

        // 转换标签为Mat格式
        Mat labelsMat = new Mat(labels.size(), 1, CvType.CV_32SC1);
        for (int i = 0; i < labels.size(); i++) {
            labelsMat.put(i, 0, labels.get(i));
        }

        // 创建LBPH人脸识别器
        FaceRecognizer faceRecognizer = LBPHFaceRecognizer.create();
        faceRecognizer.setThreshold(100.0); // 设置阈值，用于决定何时认为两个人脸是匹配的

        // 训练人脸识别器
        faceRecognizer.train(images.toArray(new Mat[0]), labelsMat);

        // 保存模型
        String modelPath = "face_recognizer_model.xml";
        faceRecognizer.save(modelPath);

        // 加载模型（如果需要的话）
        // FaceRecognizer loadedRecognizer = LBPHFaceRecognizer.create();
        // loadedRecognizer.read(modelPath);

        // 释放资源（在实际应用中，确保在不再需要时释放所有资源）
        // for (Mat image : images) {
        //     image.release();
        // }
        // labelsMat.release();
    }
}
```

### 图片处理为灰色
图像灰度化处理方法
- 最大值法
- 平均值法
- 加权平均法
1. 图像灰度化
   
在RGB模型中，如果R=G=B时，则彩色表示一种灰度颜色，其中R=G=B的值叫灰度值，因此，灰度图像每个像素只需一个字节存放灰度值（又称强度值、亮度值），灰度范围为0-255，当灰度为255的时候，表示最亮（纯白）；当灰度为0的时候，表示最暗（纯黑）。

灰度化的好处是：相较于彩色图像灰度图像占内存更小，运行速度更快；灰度图像后可以在视觉上增加对比，突出目标区域。

图像灰度化处理方法
   图像灰度化处理有三种常用方法：最大值法、平均值法和加权平均法。

2.1 最大值法
最大值法，即直接取R,B,G三个分量中数值最大的分量的数值（0视为最小，255视为最大）。公式为：R=G=B=max(R,G,B)。

2.2 平均值法
平均值法，即取R,B,G三个分量中数值的均值。公式为：R=G=B=(R+G+B)/3。

2.3 加权平均法
根据重要性及其它指标，将三个分量以不同的权值进行加权平均。由于人眼对绿色的敏感最高，对蓝色敏感最低，因此，按下式对RGB三分量进行加权平均能得到较合理的灰度图像：

示例代码
```
def Max_Gray(srcImg_path):
    img = cv.imread(srcImg_path)
    h,w = img.shape[0:2] # 获取图像尺寸
    gray = np.zeros((h,w),dtype=img.dtype) # 自定义空白单通道图像，用于存放灰度图
    # 对原图像进行遍历，然后分别灰度化
    for i in range(h):
        for j in range(w):
            gray[i,j] = max(img[i,j,0],img[i,j,1],img[i,j,2]) # 求3通道中最大值
    gray = cv.cvtColor(gray,cv.COLOR_BGR2RGB)
    plt.imshow(gray)
    plt.title('Max_Gray')
    #plt.axis('on')
    plt.show()

```

### 图像直方图
对图像进行统计,并且绘制他们各个灰度等级对应的直方图就可以的得到图像的直方图

从数学上来说，图像直方图是描述图像的各个灰度级的统计特性，它是图像灰度值的函数，统计图像中各个灰度级出现的次数或频率。
从左至右：纯黑-黑色-阴影-曝光-高光-白色-纯白

直方图横坐标是像素值，范围从 0 到 255，纵坐标表示图像中该像素值的个数。

图像直方图由于其计算代价较小，且具有图像平移、旋转、缩放不变性等众多优点，广泛地应用于图像处理的各个领域，特别是灰度图像的阈值分割、基于颜色的图像检索以及图像分类。

直方图均衡化：是指通过某种灰度映射（如：非线性拉伸）使原始图像的直方图变换为在整个灰度范围内均匀分布；目的是增加像素灰度值的动态范围，增强图像整体对比度；
直方图规定化：就是要调整原始图像的直方图去逼近规定的目标直方图；有选择地增强某个灰度范围内的对比度或使图像灰度值满足某种特定的分布；
归一化直方图：各个灰度级出现的次数除以图像的像素总数，即得到各个灰度级出现的概率，从而得到归一化直方图；

计算直方图的api如下
```
calcHist(List<Mat> images, MatOfInt channels, Mat mask, Mat hist, MatOfInt histSize, MatOfFloat ranges)
```
images：输入图像，类型必须相同
channels：通道索引列表
mask：遮罩层
hist：计算得到直方图数据，
histSize：直方图的大小
ranges：直方图的取值范围


**反向投影**
反向投影是反映直方图模型在目标图像中的分布情况
简单点说就是用直方图模型去目标图像中寻找是否有相似的对象。通常用HSV色彩空间的HS两个通道直方图模型


### 图像边缘检测原理
边缘检测的基本原理
边缘是图像的基本特征，所谓的边缘就是指的图像的局部不连续性。灰度或者结构等信息的突变之处称之为边缘。如灰度级的突变、颜色的突变、纹理结构的突变等。边缘是一个区域的结束，也是另一个区域的开始，利用该特征可以分割图像。
图像的边缘有方向和幅度两种特性，边缘通常可以通过一阶导数或二阶导数检测得到。一阶导数是以最大值作为对应边缘的位置，而二阶导数则以过零点作为对应边缘的位置。
边缘检测是一种常用的图像分割技术，常用的边缘检测算子有Roberts Cross算子、Prewitt算子、Sobel算子、Kirsch算子、Laplacian算子以及Canny算子。


图象的边缘是指 图象局部区域亮度变化显著的部分，该区域的灰度剖面一般可以看作是一个阶跃，既从一个灰度值在很小的缓冲区域内急剧变化到另一个灰度相差较大的灰度值。 边缘有正负之分，就像导数有正值也有负值一样：由暗到亮为正，由亮到暗为负

边缘提取（检测）的原理
在边缘部分，像素值出现”跳跃“或者较大的变化。如果在此边缘部分求取一阶导数，就会看到极值的出现。 而在一阶导数为极值的地方，二阶导数为0，基于这个原理，就可以进行边缘检测 图像的边缘有方向和幅度两种属性。边缘通常可以通过一阶导数或二阶导数检测得到。一阶导数是以最大值作为对应的边缘的位置，而二阶导数则以过零点作为对应边缘的位置。


### javaCv测试代码
```
package com.soonphe.timber.portal.util.images;

import com.alibaba.fastjson.JSON;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.StringUtils;
import org.bytedeco.ffmpeg.global.avcodec;
import org.bytedeco.javacpp.DoublePointer;
import org.bytedeco.javacpp.IntPointer;
import org.bytedeco.javacpp.Loader;
import org.bytedeco.javacv.*;
import org.bytedeco.opencv.opencv_core.*;
import org.bytedeco.opencv.opencv_face.FaceRecognizer;
import org.bytedeco.opencv.opencv_face.FisherFaceRecognizer;
import org.bytedeco.opencv.opencv_objdetect.CascadeClassifier;
import org.opencv.core.MatOfFloat;
import org.opencv.core.MatOfInt;
import org.opencv.imgproc.Imgproc;

import javax.imageio.ImageIO;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.IOException;
import java.net.URL;
import java.nio.IntBuffer;
import java.util.*;

import static org.bytedeco.opencv.global.opencv_core.CV_32SC1;
import static org.bytedeco.opencv.global.opencv_imgcodecs.*;
import static org.bytedeco.opencv.global.opencv_imgcodecs.IMREAD_GRAYSCALE;
import static org.bytedeco.opencv.global.opencv_imgproc.*;
import static org.opencv.imgcodecs.Imgcodecs.IMREAD_COLOR;

/**
 * OPENCV工具类
 *
 * @author soonphe
 * @since 1.0
 */
@Slf4j
public class OpenCvImagesUtil {

    /**
     * 人脸检测模型地址
     */
    private static final String CLASSIFIERNAME = "/Users/luoxiaosheng/Downloads/haarcascade_frontalface_alt.xml";
    /**
     * 人脸检测模型
     */
    private static CascadeClassifier classifier;

    static {
        String classifierName = loadClassifierName(null);
        // We can "cast" Pointer objects by instantiating a new object of the desired class.
        classifier = new CascadeClassifier(classifierName);
        if (classifier == null) {
            System.err.println("Error loading classifier file \"" + classifierName + "\".");
            System.exit(1);
        }
    }

    /**
     * 加载人脸检测模型
     *
     * @param classifierUrl
     * @return
     */
    private static final String loadClassifierName(String classifierUrl) {
        String classifierName = null;
        File file = null;
        if (StringUtils.isNotBlank(classifierUrl)) {
            try {
                URL url = new URL("https://raw.github.com/opencv/opencv/master/data/haarcascades/haarcascade_frontalface_alt.xml");
                file = Loader.cacheResource(url);
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        } else {
            file = new File(CLASSIFIERNAME);
        }
        classifierName = file.getAbsolutePath();
        return classifierName;
    }

    /**
     * 图像操作
     *
     * @param path
     * @return
     */
    public static boolean imageOps(String path){
        try {
            // 读取图像
            Mat image = imread(path);
            // 使用三通道RGB加载图像
//            Mat image = imread(path, IMREAD_COLOR);
            // 使用单通道灰度图加载图像
//            Mat image = imread(path, IMREAD_GRAYSCALE);
            // 检查图像是否正确加载
            if (image.empty()) {
                System.out.println("图像加载失败！");
                return false;
            }
            //创建窗口
            CanvasFrame canvas = new CanvasFrame("Image");
            canvas.setDefaultCloseOperation(javax.swing.JFrame.EXIT_ON_CLOSE);
            //显示图像
//            canvas.showImage(image);
//            canvas.showImage(new OpenCVFrameConverter.ToMat().convert(image));
            //将图像转换为灰度图像
            Mat grayImage = new Mat();
            cvtColor(image, grayImage, COLOR_BGR2GRAY);
//            canvas.showImage(new OpenCVFrameConverter.ToMat().convert(grayImage));
//            canvas.showImage(new OpenCVFrameConverter.ToMat().convert(image));

            //边缘检测
//            Mat edges = new Mat();
//            Canny(grayImage, edges, 50, 150);
//            File file1 = new File(path);
//            imwrite("edges-" + file1.getName(), edges);
//            canvas.showImage(new OpenCVFrameConverter.ToMat().convert(edges));

            // 阈值分割
            Mat thresholded = new Mat();
            // 二值化
            threshold(grayImage, thresholded, 127, 255, THRESH_BINARY);
            //轮廓检测
            MatVector contours = new MatVector();
            findContours(thresholded, contours, RETR_TREE, CHAIN_APPROX_SIMPLE);
            // 绘制轮廓
            Mat dst = new Mat();
            image.copyTo(dst);
            // 绘制轮廓（绿色）, -1表示绘制所有轮廓
            drawContours(dst, contours, -1, new Scalar(0, 255, 0, 0));
            // 按层级绘制，按层级绘制导出图片
//            for (int i = 0; i < contours.size(); i++) {
//                Mat contour = contours.get(i);
            // 绘制轮廓（绿色）
//                drawContours(dst, contours, i, new Scalar(0, 255, 0, 0));
//                File file1 = new File(path);
//                imwrite("dst-"+i + file1.getName(), dst);
//            }
            canvas.showImage(new OpenCVFrameConverter.ToMat().convert(dst));

            //创建ORB特征检测器
//            ORB orb = ORB.create();
//            MatOfKeyPoint keyPoints = new MatOfKeyPoint();
//            orb.detect(grayImage, keyPoints);

            //等待用户关闭
//            canvas.waitKey();
            // 等待用户关闭窗口
            while (canvas.isVisible()) {
                Thread.sleep(50);
            }

            // 释放资源
            image.release();
            grayImage.release();
            thresholded.release();
//            contours.release();
            dst.release();
            canvas.dispose();
        }catch (Exception e) {
            e.printStackTrace();
        }
        return true;
    }

    /**
     * 图像灰度处理
     * @param filename
     */
    public static Mat grayImage(String filename) {
        Mat image = imread(filename);
        Mat grayImage = new Mat();
        cvtColor(image, grayImage, COLOR_BGR2GRAY);
//        File file1 = new File(filename);
//        imwrite("gray-" + file1.getName(), smoothImage);
        return grayImage;
    }

    /**
     * 图像平滑+导出
     * @param filename
     */
    public static Mat smooth(String filename) {
        Mat image = imread(filename);
        Mat smoothImage = new Mat();
        //高斯核大小。数值越大越模糊。ksize.width和ksize.height可以不同，但它们都必须是正数和奇数。或者，它们可以是零，然后根据西格玛计算。
        GaussianBlur(image, smoothImage, new Size(81, 81), 0);
//        File file1 = new File(filename);
//        imwrite("smooth-" + file1.getName(), smoothImage);
        return smoothImage;
    }

    /**
     * 人脸检测+导出长方形框
     *
     * @param filePath 图片地址
     */
    public static void faceChecker(String filePath,String content) {
        Mat image = imread(filePath);
        RectVector faceDetections = new RectVector();
        classifier.detectMultiScale(image, faceDetections);
        for (int i = 0; i < faceDetections.size(); i++) {
            Rect rect = faceDetections.get(i);
            System.out.println("打印人脸模型数据："+JSON.toJSONString(rect));
            //图像裁剪
//            rect.height(245).width(169);
            File file1 = new File(filePath);
            imwrite("face-checker-part-" + file1.getName(), new Mat(image, rect));
            // 绘制轮廓（绿色）
            rectangle(image, rect, new Scalar(0, 255, 0, 0));
            if (StringUtils.isNotBlank(content)) {
                int pos_x = Math.max(rect.tl().x()-10, 0);
                int pos_y = Math.max(rect.tl().y()-10, 0);
                // 在图像上写文字
                putText(image, content, new Point(pos_x, pos_y), FONT_HERSHEY_PLAIN, 1.5, new Scalar(0,255,0,2.0));
            }
        }
        File file1 = new File(filePath);
        imwrite("face-checker-" + file1.getName(), image);
    }

    /**
     * 人脸训练+比对
     *
     * @param filePath     需要比对的人脸数据
     * @param filePathList 人脸训练数据
     */
    public static void faceCompare(String filePath, List<String> filePathList, List<String> filePathList2) {
        Map<Integer, String> kindNameMap = new HashMap<>();
        Mat image = imread(filePath, IMREAD_GRAYSCALE);

        //抓取人脸数据
//        RectVector faceDetections = new RectVector();
//        classifier.detectMultiScale(image, faceDetections);
//        for (int j = 0; j < faceDetections.size(); j++) {
//            Rect rect = faceDetections.get(j);
//            if (rect.width()>169 && rect.height()>245) {
//                rect.width(169).height(245);
//            }
//            image = new Mat(image, rect);
//        }
        //指定大小
        MatVector images = new MatVector(filePathList.size());
        Mat labels = new Mat(filePathList.size(), 1, CV_32SC1);
        IntBuffer labelsBuf = labels.createBuffer();
        for (int i = 0; i < filePathList.size(); i++) {
            Mat testImage = imread(filePathList.get(i) , IMREAD_GRAYSCALE);
            //抓取人脸数据
//            classifier.detectMultiScale(testImage, faceDetections);
//            for (int j = 0; j < faceDetections.size(); j++) {
//                Rect rect = faceDetections.get(j);
//                images.put(i, new Mat(testImage, rect));
//            }
            images.put(i, testImage);
            labelsBuf.put(i, i);
            File file1 = new File(filePathList.get(i));
            kindNameMap.put(i, "number:"+i+"-"+file1.getName());
        }
//        images.put(0,testImage);
//        labelsBuf.put(0, 0);
//        images.put(1,testImage2);
//        labelsBuf.put(1, 1);
//        images.put(2,testImage3);
//        labelsBuf.put(2, 2);

        // 读取人脸特征提取模型(至少需要两张图模型)
        FaceRecognizer faceRecognizer = FisherFaceRecognizer.create();
        //读取训练好的系统文件
//        faceRecognizer.read("path/to/face_recognizer_model.xml");
        faceRecognizer.train(images, labels);
        faceRecognizer.write("/Users/luoxiaosheng/Downloads/trainer.yml");


        MatVector images2 = new MatVector(filePathList.size());
        Mat labels2 = new Mat(filePathList2.size(), 1, CV_32SC1);
        IntBuffer labelsBuf2 = labels2.createBuffer();
        for (int i = 0; i < filePathList2.size(); i++) {
            Mat testImage = imread(filePathList2.get(i) , IMREAD_GRAYSCALE);

            images2.put(i,testImage);
            labelsBuf2.put(i, 3+i);
            File file1 = new File(filePathList2.get(i));
            kindNameMap.put(i, "number:"+3+i+"-"+file1.getName());
        }
        faceRecognizer.train(images2, labels2);

        IntPointer label = new IntPointer(1);
        //分配给定大小的本机双数组。
        DoublePointer confidence = new DoublePointer(1);
        //预测给定图片的标签和相关置信度，src–从中获取预测的示例图像。label–给定图像的预测标签。置信度–预测标签的相关置信度（例如距离）。
        faceRecognizer.predict(image, label, confidence);

        //也可以识别多个人脸
//        RectVector faceDetections = new RectVector();
//        classifier.detectMultiScale(image, faceDetections);
//        for (int i = 0; i < faceDetections.size(); i++) {
//            Rect rect = faceDetections.get(i);
//            faceRecognizer.predict(new Mat(image, rect), label, confidence);
//        }

        System.out.println("label: " + label);
        int predictedLabel = label.get(0);
        System.out.println("Predicted label: " + predictedLabel + " with confidence: " + confidence);
        if (Objects.isNull(predictedLabel)){
            System.out.println("Predicted label is null, confidence: " + confidence);
        }
//        faceChecker(filePath, String.valueOf(predictedLabel));
        faceChecker(filePath, kindNameMap.get(predictedLabel));
    }

    /**
     * 人脸对比2(待测试)
     *
     * @param filePath
     * @param filePath2
     */
    public static void faceCompare(String filePath, String filePath2) {
        // 2. 读取待比对图像
        Mat imageToCompare = imread(filePath);
        Mat image2 = imread(filePath2);

        // 灰度化人脸
        Mat grayImage = new Mat();
        Mat grayImage2 = new Mat();
        cvtColor(imageToCompare, grayImage, COLOR_BGR2GRAY);
        cvtColor(image2, grayImage2, COLOR_BGR2GRAY);

        Mat faceFeatures = new Mat();
        Mat faceFeatures2 = new Mat();

        // 3. 检测并提取待比对图像中的人脸特征
//        MatOfRect faceDetections = new MatOfRect();
        RectVector faceDetections = new RectVector();
        classifier.detectMultiScale(grayImage, faceDetections);
        for (int i = 0; i < faceDetections.size(); i++) {
            Rect rect = faceDetections.get(i);
//            rectangle(image, rect, new Scalar(0, 255, 0, 0));
            // 提取人脸区域（rect是检测到的人脸矩形区域）
            faceFeatures = new Mat(grayImage, rect);
            //如果需要导出照片
            File file1 = new File(filePath);
            imwrite("checker-"+i + file1.getName(), faceFeatures);
        }

        RectVector faceDetections2 = new RectVector();
        classifier.detectMultiScale(grayImage2, faceDetections2);
        for (int i = 0; i < faceDetections2.size(); i++) {
            Rect rect = faceDetections2.get(i);
            // 提取人脸区域（rect是检测到的人脸矩形区域）
            faceFeatures2 = new Mat(grayImage, rect);
            //如果需要导出照片
            File file2 = new File(filePath2);
            imwrite("checker-"+i + file2.getName(), faceFeatures2);
        }

        //颜色范围
        MatOfFloat ranges = new MatOfFloat(0f, 256f);// pixel values range for grayscale
        //直方图大小， 越大匹配越精确 (越慢)
//        MatOfInt histSize = new MatOfInt(10000000);
        MatOfInt histSize = new MatOfInt(256); // 256 bins for the grayscale histogram
        MatOfInt channels = new MatOfInt(0); // channel [0] is used for grayscale

        Mat hist_1 = new Mat();
        Mat hist_2 = new Mat();

        //List<Mat> images, MatOfInt channels, Mat mask, Mat hist, MatOfInt histSize, MatOfFloat ranges)
//        Imgproc.calcHist(java.util.Arrays.asList(faceFeatures), new MatOfInt(0), new Mat(), hist_1, histSize, ranges);
//        Imgproc.calcHist(java.util.Arrays.asList(faceFeatures2), new MatOfInt(0), new Mat(), hist_2, histSize, ranges);

        // Mat images, int nimages, IntPointer channels, Mat mask, Mat hist, int dims, IntPointer histSize,
        //  PointerPointer ranges, @Cast("bool") boolean uniform/*=true*/, @Cast("bool") boolean accumulate/*=false*/
//        calcHist(faceFeatures, 0, new MatOfInt(0), new Mat(), hist_1, null,  histSize, ranges,true, false);

//        System.out.println("Histogram data: " + hist.dump());

        // CORREL 相关系数
        double res = compareHist(hist_1, hist_2, Imgproc.CV_COMP_CORREL);


        // 5. 比对人脸特征
        // 根据从待比对图像和目标图像中提取的特征进行比对，这里需要实现具体的比对逻辑。
        // 可能涉及计算特征之间的欧氏距离或其他度量标准，并根据阈值判断是否匹配。

        // 注意：实际使用中需要添加资源释放逻辑
//        faceDetector.release();
//        faceRecognizer.release();

    }


    /**
     * 拉取指定时长的视频-保存为mp4
     *
     * @param rtspUrl    流地址
     * @param duration   时长（秒）（TimeUnit Seconds）
     * @param outputFile 输出位置
     */
    public static void videoPuller(String rtspUrl, int duration, String outputFile) {
        // 创建抓取器
        FFmpegFrameGrabber grabber = new FFmpegFrameGrabber(rtspUrl);
        try {
            grabber.start();
            // 创建录制器-设置的文件保存位置以及视频画面的宽度和高度
            FFmpegFrameRecorder recorder = new FFmpegFrameRecorder(outputFile, grabber.getImageWidth(), grabber.getImageHeight());
            recorder.setVideoCodec(avcodec.AV_CODEC_ID_H264); // 设置视频编解码器
            recorder.setFormat("mp4"); // 设置视频输出格式

            // 设置音频相关参数
            recorder.setAudioCodec(avcodec.AV_CODEC_ID_AAC);
            //这里直接写2以及后续写固定值也可以，当然我们可以从grabber对象中get出来
            grabber.getAudioChannels();
            recorder.setAudioChannels(grabber.getAudioChannels());
//            recorder.setAudioChannels(2);
            grabber.getSampleRate();
//            recorder.setSampleRate(44100);
            grabber.getAudioBitrate();
//            recorder.setAudioBitrate(192000);
            recorder.start();

            Frame frame;
            long startTime = System.currentTimeMillis();
            long endTime = startTime + (duration * 1000);

            while ((frame = grabber.grabFrame()) != null && System.currentTimeMillis() <= endTime) {
                recorder.record(frame);
            }
            recorder.stop();
            grabber.stop();
            recorder.close();
            grabber.close();
        } catch (FrameGrabber.Exception | FrameRecorder.Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 只保留音频
     * @param rtspUrl
     * @param outputPath
     * @param maxDurationSeconds
     * @throws Exception
     */
    public static void extractAudioFromRtsp(String rtspUrl, String outputPath, int maxDurationSeconds) throws Exception {
        FFmpegFrameGrabber grabber = new FFmpegFrameGrabber(rtspUrl);
        grabber.start();

        //1.从视频流中取出读取到的参数
        int audioChannels = grabber.getAudioChannels();//音频通道
        int sampleRate = grabber.getSampleRate();
        int audioBitrate = grabber.getAudioBitrate();

        //创建recorder对象，设置编码器和解码器
        //这里也是所有AI都回答错误的地方
        //通义和GPT会回答为这样
        //FFmpegFrameRecorder recorder = new FFmpegFrameRecorder(outputPath, audioChannels, sampleRate);
        //上面这种错误传参方式导致，audioChannels变为为宽，sampleRate为高
        FFmpegFrameRecorder recorder = new FFmpegFrameRecorder(outputPath, audioChannels);
        recorder.setSampleRate(sampleRate);
        recorder.setAudioBitrate(audioBitrate);
        recorder.setAudioCodec(avcodec.AV_CODEC_ID_PCM_S16LE); // 使用PCM 16位小端模式编码
        recorder.start();

        long startTime = System.currentTimeMillis();
        Frame frame;
        while ((frame = grabber.grabSamples()) != null && (System.currentTimeMillis() - startTime) / 1000 < maxDurationSeconds) {
            recorder.record(frame, 0);
        }

        recorder.stop();
        recorder.release();
        grabber.stop();
        grabber.release();
    }

    public static void main(String[] args) {
//        blackScreenChecker("/Users/luoxiaosheng/Downloads/20240328093947_in.jpg");
//        blackScreenChecker("/Users/luoxiaosheng/Downloads/20240514987969_in.jpg");
        // 图片操作
//        imageOps("/Users/luoxiaosheng/Downloads/20240514987969_in.jpg");
//        imageOps("/Users/luoxiaosheng/Downloads/face-test.png");
        //图像平滑
//        smooth("/Users/luoxiaosheng/Downloads/face-test.png");
        //人脸检测
//        faceChecker("/Users/luoxiaosheng/Downloads/face-test.png");
//        faceChecker("/Users/luoxiaosheng/Downloads/face-test2.png", "face-test2");
//        faceChecker("/Users/luoxiaosheng/Downloads/face-test3.png", "face-test3");
//        faceChecker("/Users/luoxiaosheng/Downloads/20240328093947_in.jpg");
        //人脸训练对比
        List<String> fileList = new ArrayList<String>();
//        fileList.add("/Users/luoxiaosheng/Downloads/face-test.png");
//        fileList.add("/Users/luoxiaosheng/Downloads/face-test22.png");
//        fileList.add("/Users/luoxiaosheng/Downloads/face-test3.png");
        fileList.add("/Users/luoxiaosheng/Downloads/410105200510290096-1717487037677.jpg");
        fileList.add("/Users/luoxiaosheng/Downloads/421083199701014933-1717486943308.jpg");
        fileList.add("/Users/luoxiaosheng/Downloads/430527200601247222-1717486943046.jpg");
        List<String> fileList2 = new ArrayList<String>();
        fileList2.add("/Users/luoxiaosheng/Downloads/510823200504103754-1717486863841.jpg");
        fileList2.add("/Users/luoxiaosheng/Downloads/469028200403150924-1717486824746.jpg");
        fileList2.add("/Users/luoxiaosheng/Downloads/421083200304055614-1717486684493.jpg");

//        fileList.add("/Users/luoxiaosheng/Downloads/421081200505212978-1717487277211.jpg");
//        fileList.add("/Users/luoxiaosheng/Downloads/500221199906290422-1717486828111.jpg");
//        fileList.add("/Users/luoxiaosheng/Downloads/130633200501136818-1717486752571.jpg");
//        fileList.add("/Users/luoxiaosheng/Downloads/420982200412207234-1717486699725.jpg");
        faceCompare("/Users/luoxiaosheng/Downloads/421083199701014933-1717486943308.jpg", fileList,fileList2);

//        faceCompare("/Users/luoxiaosheng/Downloads/face-test3.png", fileList);
//        faceCompare("/Users/luoxiaosheng/Downloads/face-test22.png","/Users/luoxiaosheng/Downloads/face-test.png");

        // 抓取推流
//        videoPuller("rtsp流地址",60, "D:\\va\\a.mp4");
//        videoPuller("http://qvs-live.jiaxiao.fun:1360/statictest/7674029525193673_in.flv",15, "/Users/luoxiaosheng/Downloads/7674029525193673_in.mp4");
    }
}
```



