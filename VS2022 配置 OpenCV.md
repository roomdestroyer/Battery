# VS2022 配置 OpenCV

## 1. 环境

~~~
OS Version: Microsoft Windows 10 x86-64 based
VS Version: Microsoft Visual Studio Community 2022 (64 位) 
OpenCV Version: OpenCV-4.5.5 (2021-12-30)
~~~



## 2. 下载 OpenCV

进入 OpenCV 官网：[Releases - OpenCV](https://opencv.org/releases/) 下载最新版本的 `OpenCV`，或 [点此下载](https://bucket011.obs.cn-north-4.myhuaweicloud.com:443/opencv-4.5.5-vc14_vc15.exe?AccessKeyId=S3NBXULCUVSGOWLC75R3&Expires=1684921818&Signature=FN5%2BCRUO0lRR2CrHnM3d1CJ0IKY%3D) 。下载好的安装文件如下：

![image-20220529175113727](https://s2.loli.net/2022/05/29/EZX9wObaTeB5Vj6.png)

解压到 D 盘的根目录下：

![image-20220529175315437](https://s2.loli.net/2022/05/29/i1vWn5GVpM4ZAlB.png)

现在，你的 `opencv` 的路径为 `D:\opencv`。



## 3. 配置 OpenCV

搜索属性管理器，打开：

![image-20220529175913212](https://s2.loli.net/2022/05/29/FonRxqb2pPHIvZY.png)

在属性管理器下，找到 `Debug|x64` 项，右键打开 `属性`。

![image-20220529180042595](https://s2.loli.net/2022/05/29/sh6o1fbliCGjarK.png)

点击 “通用属性 -> VC++ 目录 -> 常规 -> 包含目录"，选择编辑：

![image-20220529180257587](https://s2.loli.net/2022/05/29/FpNS6yCHv4LqQ1V.png)

添加下面两项：

~~~
D:\opencv\build\include
D:\opencv\build\include\opencv2
~~~

![image-20220529180524187](https://s2.loli.net/2022/05/29/f8Xnmt71F2CMDZs.png)

在同级目录下，编辑 “库目录”，添加下面一项：

~~~
D:\opencv\build\x64\vc15\lib
~~~

![image-20220529185655415](https://s2.loli.net/2022/05/29/nLe8ufEdxqFUmZ5.png)

点击 “通用属性 -> 链接器 -> 输入 -> 附加依赖项"，选择编辑：

![image-20220529180454993](https://s2.loli.net/2022/05/29/CIKrZvldwUc87Re.png)

添加下面一项：

~~~
opencv_world455d.lib
~~~

点击应用：

![image-20220529180858162](https://s2.loli.net/2022/05/29/4y5R3zXaAIngYuM.png)

新建系统环境变量：

![image-20220529180543779](https://s2.loli.net/2022/05/29/gt8eAEVcCSdNkuL.png)

添加下列变量名和变量值：

~~~
opencv
D:\opencv\build\x64\vc15\bin
~~~

![image-20220529181059729](https://s2.loli.net/2022/05/29/tqMNzK3QAIc6PJ7.png)

在路径 `D:\opencv\build\x64\vc15\bin` 下，选择下列这三个文件：

![image-20220529181204411](https://s2.loli.net/2022/05/29/mqBGbyHoLVEu5Sv.png)

将它们复制到目录 `C:\Windows\System32` 下。

重启电脑让环境变量生效，打开 `VS2022`，设置 `Debug-x64` 模式：

![image-20220529181605899](https://s2.loli.net/2022/05/29/Lr1Y4fy2RbZVFuD.png)



## 4. 测试环境

将测试图片拷贝到你的工作目录下：

![midnight](https://s2.loli.net/2022/05/29/ZR4Qlgy3mLfIYFk.png)

输入以下测试代码：

~~~
#include <opencv2/core.hpp>
#include <opencv2/imgcodecs.hpp>
#include <opencv2/highgui.hpp>
#include <iostream>
using namespace cv;
int main()
{
    std::string image_path = samples::findFile("midnight.png");
    Mat img = imread(image_path, IMREAD_COLOR);
    if (img.empty())
    {
        std::cout << "Could not read the image: " << image_path << std::endl;
        return 1;
    }
    imshow("Display window", img);
    int k = waitKey(0); // Wait for a keystroke in the window
    if (k == 's')
    {
        imwrite("starry_night.png", img);
    }
    return 0;
}
~~~

输出如下表示配置成功：

![image-20220529185357582](https://s2.loli.net/2022/05/29/reBH8SP3OIysCUY.png)