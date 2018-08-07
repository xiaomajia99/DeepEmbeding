# Deep Metric Learning
---
Deep Metric Learning mile-stone paper:  
1.[DrLIM:Dimensionality Reduction by Learning an Invariant Mapping](http://yann.lecun.com/exdb/publis/pdf/hadsell-chopra-lecun-06.pdf)  
2.[DeepRanking:Learning fine-graied Image Similarity with DeepRanking](https://users.eecs.northwestern.edu/~jwa368/pdfs/deep_ranking.pdf)  
3.[DeepID2:Deep Learning Face Representation by Joint Identification-Verification](http://yann.lecun.com/exdb/publis/pdf/hadsell-chopra-lecun-06.pdf)  
4.[FaceNet:FaceNet: A Unified Embedding for Face Recognition and Clustering](http://arxiv.org/abs/1503.03832)  
5.[Defense:In Defense of the Triplet Loss for Person Re-Identification](http://arxiv.org/abs/1703.07737)  
6.[N-pair:Improved Deep Metric Learning with Multi-class N-pair Loss Objective](http://www.nec-labs.com/uploads/images/Department-Images/MediaAnalytics/papers/nips16_npairmetriclearning.pdf)  
7.[Sampling:Sampling Matters in Deep Embedding Learning](https://arxiv.org/abs/1706.07567)  
8.[DAML:Deep Adversarial Metric Learning](http://openaccess.thecvf.com/content_cvpr_2018/papers/Duan_Deep_Adversarial_Metric_CVPR_2018_paper.pdf)  
9.[SphereFace:Deep Hypersphere Embedding for Face Recognition](http://ieeexplore.ieee.org/document/8100196/)



# Deep Metric Learning Introduction
---
- 从度量学习，监督或非监督降维理论及流形学习理论以来，诸多学习算法力求学习一种数据从原始空间和低维嵌入空间的映射，在嵌入空间中能保持
原生空间数据的相似距离关系，这种空间转换函数的学习可称之为度量学习；相对于传统的分类问题而言，其种类数目比较固定，如ImageNet根据WordNet分为1000类别，
然而很多现实中的问题不是closeset问题，是openset问题，即在泛化测试时有很多样本和种类类别是从未遇见的，典型的特征是种类多，单个种类样本少，最为典型的是人脸识别（亿级别的类别，单个人最多就抓拍十几张头像）。
将分类和度量对比，分类学习的是一种概念，粗糙的概观，是一种孤立看待问题的观点，度量学习的是精细的尺度，强调的是对隐含属性概念的整体布局，一种尺度。
也可以认为，分类问题对事物的编码是一种one-hot编码格式，而度量是对事物的一种denseVector编码格式。
概念在未见过的种类无法进行描述，而尺度在未见过的种类依然可以按照尺度测量规范进行测量。（举例，前者学习的是每一个的人名，后者学习的是对人的测量方法，遇到向别人描述一个他不认识的人时，通过属性描述的方式，更容易让别人形成画像）

- 现实世界中，很多时候并不需要对所有见过的事物说名道姓，我们不关心他是谁，关心的是他和其他事物的距离关系。这也就是从DeepID2的分类loss转换到FaceNet时triplet loss之后人脸识别效果提升的原因，当然这也是人脸识别，行人重识别，车辆检索（车辆套牌分析），商品检索应用落地的原因。  
- 通过度量学习，我们可以把现实世界中的搜索问题（视觉搜索）定义为简单的两步，将原生数据空间通过学习得来的映射函数映射到嵌入空间，在嵌入空间通过近似临近搜索算法搜索最近点，通过最近点id反查原始空间的数据。
其核心技术在于 __关系高保持的空间映射函数__ 和 __高速高容量的向量搜索引擎__。前者可以保证从原生数据空间映射到嵌入空间之后，数据的相似关系保持的精细，数据在嵌入空间的距离与原生数据的观测属性成正相关。
后者保证能在广泛的搜索空间中高效的查找嵌入空间中距离相近的点（可以在亿级的库中以毫秒级时间搜索出近似最近点）。

-传统的LDA，MDS，IsoMap等方法是一种浅层次的映射函数，而且MDS实际上无法对未见的数据点进行低维度嵌入，只能使用拟合函数进行转换。而SNE算法是一种已指数据样本高维空间距离之后，根据数据距离分布近似的目标，将高位数据点降维，并不适合视觉图片。
深度卷积神经网络通过层层递进特征提取的方式，具备深层次语义特征提取的能力，通过合适的数据加载和目标函数的梯度引导，该卷积神经网络函数能学习一种深层次的特征提取。该方法又被称为深度度量学习，即Deep Metric Learning
以人们分辨狗和猫为例，如果让一个婴儿来学习分辨，是不可能的；必须得是大脑发育完整的儿童，如果让儿童整天看白猫和黑狗，久而久之，儿童会以为白颜色的是猫，黑颜色的是狗，遇到一个黑猫，也认为是狗。当然有的是每次给一对动物来分辨，有的是给一批动物来分辨。
如果一直让儿童去分辨容易分辨的狗和猫，儿童没有经过艰难的判别隔离训练，对于之后遇到的困难样本，就变得无所适从。
从上述例子可知：深度度量学习函数要训练好的三个前提条件分别是：模型结构，原始数据采样方式，目标函数，困难样本。深度度量学习很多提升改进都是基于基本要素的考虑。



## 20180803
## 本周工作
### Street2Shop图像检索问题  
- 使用Margin-based loss 方法train DeepFashion InShop 数据集,recall@1 = 0.7  
- 编写DeepFashion InShop服装数据的Dataset，代码参见：https://github.com/EigenLab/DeepEmbeding/blob/master/data/mxdata/deep_fashion.py
- 分别测试了Stanford_Online_Products的InClass和CrossClass采样数据加载方式，通过训练测试发现，InClass方式训练模型的检索效果较好，
训练log记录参见：http://hpc3.yud.io:8088/ 中mx_Margin_Ebay_InClass和mx_Margin_EbayCross

### DaVinCi Relayout(王鑫远)  
- 开发了标注结果复核功能，可以通过浏览器核查之前标定过的PSD文件。
- 初步验证了搜索+规则调整的方案可行性，在接受604份标注数据后，接收用户的主图和重拍版需求，在长条状排版需求排版效果较好。
- 为提升重排版规则粒度，对标定的PSD文件进行抽象分类。针对每一类人工设计规则。

### 抠图算法（贺浩）  
- 阅读了Mask-rcnn，FCN系列图像分割算法论文及代码实现。
- 查阅graph-cut，grab-cut等系列交互式抠图算法。

## 下周工作
- 调参训练一个检索效果好的模型，使用Mxnet-model-server部署DeepEmbeding.能对服装图片提取128维向量特征。
- 使用之前的sku检索数据，通过DeepEmbeding后建立NNSearch索引，通过A/B test测试和之前基于VGG分类模型bottleneck特征提取的方法进行比较,
比较recall@K和RPS 



                                  1.nn&dl,http://neuralnetworksanddeeplearning.com/ 全部看，中文在tensorflow社区里面有（http://www.tensorfly.cn/home/）。
2.pytorch或者tensorflow教程，着重跑一下mnist cifar-10,数据加载，数据增强,指标计算。中文版本书籍有https://github.com/chenyuntc/pytorch-book 
3.cs231n 斯坦福大学 计算机视觉及深度学习课程. (deeplearning for cv 必看经典，基础内容很多，第七节课才讲卷积)（英语不好的可以看李宏毅也行）
4.深度学习(花书). (描述的很有深度，参数初始化，可视化，优化方法，其中鞍点/通用逼近/局部极小值/hessian也可以参见李宏毅的DeepLearning Theory系列课程)
5.深度学习论文阅读roadmap.Deep-Learning-Papers-Reading-Roadmap，github可以搜到。
6.机器学习（周志华）.可以对照机器学习实战或者scikit-learn源码看,也可以看safari上的scikit-learn&tensorflow教程，orange库好像也不错。
7.动手学习深度学习（李沐）。http://zh.gluon.ai/chapter_introduction/index.html。 有教学视频和源码。
8.博客：http://colah.github.io（google的一个工程师写的） https://zhuanlan.zhihu.com/p/25628246（百度的一个工程师写的）
9.应用方向：大体参见safari-book上的书，deeplearning and computer-vision,17年末出的，引用的paper比较新，基本囊括了流行的应用，该书是用tensorflow写的，包括了目标检测，图像检索，图像分割，图文理解等领域。
10.源码：caffe源码，caffe源码相对比较成熟稳定，尤其是有反向传播的完整代码；可以编译一个debug版本，cpu条件下调试执行mnist案例看其中的数据结构和流程。
         其他代码没看过，mxnet声称兼具静态计算图和动态计算图的优点，应该值得一看，tensorflow不熟。（如果为了看逻辑实现，看mxnet或者pytorch就行，如果看工业实现，推荐caffe
11.论文查找：paper-weekly,google-scholar.
12.一些awesome 资源：https://github.com/kjw0612/awesome-deep-vision  
                     https://github.com/guillaume-chevalier/Awesome-Deep-Learning-Resources
                     yotube网红，siraj ravel。
13.一些库：visdom绘图不错,cupy作为numpy的计算加速,fire作为argparse的替换   