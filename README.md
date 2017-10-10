基于自适应显著性融合以及改进词袋模型的人体行为识别研究
摘要
视频人体行为识别研究是计算机视觉领域的前沿方向，同时在运动分析、视频监控和智能交互等领域有着广泛的应用前景。由于人体行为的环境干扰和拍摄条件的不确定性，视频人体行为识别研究仍充满挑战，而其中的难点在于如何合理地描述行为和构建视频表示模型。本文针对不限场景下的单人体行为识别问题进行研究，重点对特征析取进行了研究，提出了自适应融合显著性分析方法和改进的词袋模型。首先，在改进的密集轨迹的基础上，通过自适应融合得到对应的显著图谱，以筛选出显著轨迹集合并提取多种描述符。其次，引入了软量化后的范围化局部聚集描述符向量编码。再者，提出了微运动对模型并设计了对应的描述信息。结果表明，本文算法在KTH和UCF Sports数据集上取得了优于现有方法的识别效果，而在JHMDB和HMDB51数据集上实验结果接近于当前最新成果。
第1章引言
1.1相关背景
计算机视觉技术是研究如何让计算机拥有与人类一样的“感知”视频内容的能力，其中，物体识别、视频分割、目标跟踪和行为识别等都是当前研究热点。另外，视频人体行为识别研究结合了数字图像处理、人工智能、模式识别等多个学科，其发展可以促进相关领域的突破。
当前，大部分视频人体行为识别研究仍停留在自然场景下的单体行为识别阶段，视频人体行为识别研究的挑战集中于：首先，需要保证特征的鲁棒性、有效性和对应描述信息的准确性；随后，利用特征准确描述不同类别的行为的相似性和同类别不同主体的行为的差异性；最后，还需要选择合理的分类算法。
本文提出了自适应显著性融合的显著轨迹集筛选方法以及基于VLAD-k编码和微运动对模型的词袋模型表示方法。得到视频的六种描述符串联而成的视频表示，采用SVM进行分类。
1.2相关综述（related works）
视频人体行为识别研究主要包括目标分割，特征提取与表示和行为分类三个主要阶段。本文研究的焦点是人体行为视频中，让计算机实现自动对行为进行标注，因此本文主要对特征析取进行综述。
特征提取的目的是将输入的视频序列转化为一系列可以用来表征人体行为的特征，进而形成运动表示。主流的特征提取方法有基于数据驱动特征和基于局部特征的方法的方法。
数据驱动特征方面，借助于现阶段流行的神经网络，可以在训练数据中自主挖掘出数据驱动的特征，又可以利用训练结果自主完成识别过程。Gkioxari等人[1]从包含运动主体的主区域邻域中筛选出候选次区域，利用CNN训练得到的随机梯度下降模型获得这些区域的权值向量和特征表示；Lin等人[2]提出了双线性CNN模型，通过外乘的方式将图像各个区域的两种描述符融合，进一步将所有区域的表示池化以描述图像
局部特征是将视频分为多个区域，对其中某些稳定出现并且具有良好可区分性质的点分别地建立描述，进而利用各种模型构造视频表示，对比全局特征而言，具有较强的抗干扰性和减少计算量的优点其中包括特征检测算子、特征描述符和特征模型等方法。Wang等人[3]引入了密集采样的概念，并结合稠密光流[4]跟踪来获得稠密轨迹；Arandjelović等人[5]则利用海林格距离替换了原始SIFT中的距离度量标准，提出了Root-SIFT描述符；Ma[6]等人通过聚类形成简单的动作字典，进一步通过树挖掘、树聚类和树排序，选择出其中富有区分度的树模式。在提取出局部特征后，需要采用自下而上的编码方式对高层语义进行特征模型的建立。除了主流的视觉词袋模型[7][8]，还有基于上下文信息的编码方式[9]和基于VLAD编码的编码方式[10]。
本文中，将采用轨迹特征作为底层特征，并筛选出显著轨迹，随后利用基于VLAD-k编码与微运动的改进BoVW模型对此进行运动表示。
第2章基于自适应显著性融合以及改进词袋模型的人体行为识别算法框架
本文提出了基于自适应显著性融合和改进词袋模型的人体行为识别框架。如图1所示，算法流程如下：对于输入的行为视频，(a)首先逐帧密集采样并依据补偿后的稠密前景光流场进行跟踪，提取轨迹；(b)其次，对各帧逐一分析两种像素级别的显著性值，分别捕获静态特性和运动特性，自适应融合两种显著性，形成各帧的显著性图谱；(c)进而，利用显著性图谱筛选出密集轨迹中的显著轨迹；(d)再而，利用改进的BoVW模型形成视频运动表示；(e)最后，结合SVM完成行为分类。

图 1基于自适应显著性融合和改进词袋模型的人体行为识别框架流程图
2.1显著轨迹提取
本文通过分析轨迹的不同显著性并自适应融合，筛选出其中具备视觉关注度的部分。
2.1.1改进密集轨迹提取
记对应于角点、纹理等易于追踪的特征处即为轨迹点P。
引进密集采样技术[10]，并采用文献[11]在帧Fiτ和Fi+1τ上分别提取对遮挡鲁棒性较好的SURF描述符[12]，采用随机采样一致性算法在光流场Φ(Fiτ,Fi+1τ)中移除背景运动部分，得到，则轨迹点追踪方式为，将Fiτ中的点P跟踪到下一帧Fi+1τ中的P’处，其中，M为中值滤波掩模。
将轨迹的追踪长度设置为L。一条轨迹即为L+1个轨迹点的有序序列，被缩放层次τ、帧Fiτ和起始采样点唯一标识，记为×Numτ。
2.1.2 显著性分析
	考虑帧Fiτ，本文将提取静态、动态两种对应的像素级显著图谱，分别是称为基于有辨别力的区域特征整合（DRFI）的静态显著性（Sal-DRFI），和基于光流稀疏编码的动态显著性（Sal-OFSC）。
（1）基于有辨别力的区域特征整合的静态显著性分析
DRFI的基本流程包括多分割层次表示、各区域显著性计算和像素多分割层次显著值融合三个阶段：首先利用基于图分割方法[13]对每一帧进行最细粒度的过分割，随后利用过分割区域的区域显著性、特征对比和边界的几何特征来构成222维特征向量结合决策树来评估相邻区域的相似度[13]，以判断能否合并；合并后，任意分割表示都对应一个93维的特征向量，分别由区域对比描述符、区域背景描述符和区域性质描述符3种特征拼接而成。利用随机森林模型RFR[14]，将的描述向量直接映射为的显著值；对Fiτ上任意像素点P，其必然落在每个分割层次上的某个区域上，而这些区域均有对应的显著值，故定义P的显著值为所在的各个区域的显著值的线性组合，即为。
（2）基于光流稀疏编码的动态显著性分析
对于光流场，本文假设在每个帧间运动都是由对应于前景目标的若干种主像素运动构成的，记为，而所有单像素运动由主像素运动线性合成。由此可以定义主像素运动对P处光流的重建代价理想模型为

其中，costT(Dp)为变换代价，为光流场和单像素运动的误差。随后，将主像素运动对P处光流的重建代价理想模型costRes(Dp)转化为P处基于主像素运动的光流稀疏编码问题[15]。
2.1.3 显著轨迹筛选
为了同时捕获静态、动态显著性，本文提出了自适应的加权显著性（Adaptively Weighted Saliency，Sal-AW）：

而为了减少模型的复杂性，本文将约束α+β=1。当α、β取值合理时，为了更好地适应如非常平缓的运动和灰度强度过于平均的拍摄环境等极限情况。
进而，定义轨迹Tr的显著值为轨迹上所有像素点显著性的均值：

	由此，为了获取到对应于视觉关注区域的轨迹，提取显著轨迹集合：

2.2基于改进词袋模型的运动表示构建
2.2.1 描述符计算
对轨迹Tr，首先，提取以其前L个特征点为中心的E×E邻域，时空对齐后，由此得到E×E×L的时空立方体，进一步将时空立方体进一步划分为eXY×eXY×eT个等大的lXY×lXY×lT时空网格。在时空网格中，分别计算对应于HOG、HOF和MBH的3种描述信息。最后，根据时空网格的时空位置顺序，将计算结果串联形成轨迹描述符。
2.2.2 视频表示构建
首先，使用K-means从描述符集合中构造视觉词袋，并采用了VLAD-k编码方法代替原有的硬指派方法，其编码ψVLAD-k中各维度为：
	，	
其中，ωG(xi,cj)为关于描述符xi和视觉词汇cj的高斯加权函数，定义为
	，	
而响应值函数为。
在局部化的过程中，为了加速搜索过程，本文采用了k维树（k dimensional tree，kd-tree）对视觉词袋形成的流型空间进行描述并以此进行范围近邻搜索。 
而为了显式描述微运动的分布情况，本文进一步提出了微运动对模型：
	，	
即对微运动ci和cj，所有与ci关联的轨迹和所有与cj关联的轨迹成对归入模型表示。这种表示的意义在于，既可以区分相似运动，又可以增强同类运动之间的关联性。
位置和时序两个角度对微运动对进行描述。首先，根据轨迹的几何特征，定义轨迹间的位置关系为：
	，	
类似于HOG等描述符的计算过程，轨迹间位置关系同样可以利用直方图进行表示：转化到极坐标系中，并将其软量化到4个bin上。
再者，对轨迹间的时序关系进行度量。轨迹前后关系是固定的，即可以根据轨迹初始点所在帧的先后顺序来确定，即
	。	
同时，为了消除帧率和视频长度等拍摄参数带来的影响，定义了轨迹的“远”、“近”、“前”、“后”四种布尔关系，对应为：

定义轨迹间时序关系Er(TrA,TrB)=[ Er-F(TrA,TrB), Er-N(TrA,TrB), Er-B(TrA,TrB), Er-A(TrA,TrB)]。
第3章实验结果以及分析
本文的实验数据集选择了KTH，UCF Sports，JHMDB和HMDB51四个数据集。
3.1基准数据集、评价标准和固定参数设置
对于KTH数据集，本文沿用留一组交叉验证(Leave-One-Group-Out, LOGO)，在每一轮中，选择一个运动主体的共计24个视频（6×1×4）作为测试数据，剩余24个主体的所有视频作为训练数据，共进行了25轮训练和测试，并汇报最终的平均识别率
	，	(41)
其中，R为行为总数，AAr表示第r类行为的识别率。
本文对UCF Sports数据集沿用了留一交叉验证(Leave-One-Out, LOO)，在每一轮中，每一个原始视频都被选中为测试数据，而除去该视频及其翻转版本的剩余视频集合作为训练集，共计150轮训练和测试，并汇报最终的平均识别率。
HMDB51沿用初始设置，即设置了三轮训练测试实验，在每轮实验中，每一类行为视频都贡献了70个训练样本和测试样本，最终，汇报三轮实验的平均识别率。
JHMDB数据集为HMDB51数据集的子集。该数据集移除了HMDB51中脸部行为、与物体交互的脸部行为和交互行为3大类行为，而专注于肢体部分的单类型人体行为识别。该数据集还提供了人工精心标注的区域掩模和姿态特征。
本文中一部分参数来源于算法提出者或者一般性设置，如表4-1所示。
表 41 固定参数设置
数据集	行为类别	视频总数	每个类别的视频数量	数据集大小	运动情况	数据集描述
KTH	6	599	10	1.1G	简单	监控场景下的人体行为
UCF Sports	10	300	14~35	155M	复杂	自然场景行为
JHMDB	21	928	101+	186G	非常复杂	来源于电影、在线视频
HMDB51	51	6849	101+	2.1G	非常复杂	来源于电影、在线视频
3.2与最新成果（state-of-the-art）的对比
表 42 KTH数据集上的平均识别率比较（带*结果为本文参照文献进行重现的结果）
算法	年份	平均识别率（%）	类型
Shao等人[147]	2016	97.5	传统方法
Carvajal等人[146]	2016	97.5	
Sun等人[152]	2014	92.1	SFA
Qin等人[153]	2016	94.7	3D CNNs + LSTM
基于密集轨迹的方法[150]	2013	94.2	传统方法
基于改进密集轨迹的方法[151]	2016	95.4*	
TEAB-SAW+VLAD-k（本文）		95.53	传统方法
TEAB-SAW+ARCAB-IBoVW（本文）	-	98	
表 42展示KTH数据集上本文的实验结果与最新成果以及基准方法之间的对比。相对于大部分现有方法，本文提出的TEAB-SAW+ARCAB-IBoVW方法处于领先地位。
表 43展示UCF Sports数据集上本文的实验结果与最新成果以及基准方法之间的对比。类似于在KTH数据集上的结果，相对于大部分现有方法，本文提出的TEAB-SAW+ARCAB-IBoVW方法处于领先地位，在基于改进密集轨迹的方法上提升了3.41%的识别率。
表 43 UCF Sports数据集上的平均识别率比较（带*为本文参照文献重现结果）
算法	年份	平均识别率（%）	类型
Abdulmunem等人 	2016	90.9	传统方法
Lan等人 	2015	83.6	
Sun等人 	2014	86.6	SFA
Peng等人 	2016	91.49	R-CNN
基于密集轨迹的方法 	2013	88.0	传统方法
基于改进密集轨迹的方法 	2016	89.37*	
TEAB-SAW+VLAD-k（本文）		89.53	传统方法
TEAB-SAW+ARCAB-IBoVW（本文）	-	92.94	
表 44展示了JHMDB数据集上本文的实验结果与最新成果以及基准方法之间的对比。本文提出的TEAB-SAW+ARCAB-IBoVW方法在基于改进密集轨迹的方法上提升了2.5%的识别率。同时，相对于大部分现有方法，TEAB-SAW+ ARCAB-IBoVW方法处于领先地位，但与最优秀的算法仍存在差距。
表 44 JHMDB数据集上的平均识别率比较
算法	年份	平均识别率（%）	类型
Jhuang等人 	2013	76.0	传统方法
Xaiohan等人 	2015	61.2	
Krishnan等人 	2016	84.5	LSTM
Cherian等人 	2017	73.3	HOK+iDT
基于密集轨迹的方法 	2013	56.6	传统方法
基于改进密集轨迹的方法 	2014	62.8	
TEAB-SAW+VLAD-k（本文）		62.4	传统方法
TEAB-SAW+ARCAB-IBoVW（本文）	-	65.3	
表 45展示了HMDB51数据集上本文的实验结果与最新成果以及基准方法之间的对比。本文提出的TEAB-SAW+ARCAB-IBoVW方法在基于改进密集轨迹的方法上提升了2.04%的识别率。与最新方法相比，本文提出的TEAB-SAW+ARCAB-IBoVW的效果一般，但是计算复杂度远远低于除两种基准方法和Jain等人 之外的所有比较方法。


表 45 HMDB51数据集上的平均识别率比较
算法	年份	平均识别率（%）	类型
Peng等人的FV+SFV错误! 未找到引用源。	2014	66.79	传统方法
Jain等人错误! 未找到引用源。	2013	52.1	
Jain等人错误! 未找到引用源。 	2015	71.3	CNN+iDT+FV-SFV
Simonyan等人错误! 未找到引用源。	2014	59.4	CNN
基于密集轨迹的方法错误! 未找到引用源。	2013	46.6	传统方法
基于改进密集轨迹的方法	2014	57.2 	
TEAB-SAW+VLAD-k（本文）		56.88	传统方法
TEAB-SAW+ARCAB-IBoVW（本文）	-	59.24	
第4章总结与展望
本文提出了基于自适应显著性融合和改进词袋模型的人体行为识别算法框架，并提出了微运动对（MP）模型以刻画轨迹特征间的时空关联结构，在KTH、UCF Sports、JHMDB和HMDB51四个基准数据集上，对本文提出的算法的参数进行优化、固定，对有效性进行验证。结果表明，本文提出的TEAB-SAW+ARCAB-IBoVW算法在KTH和UCF Sports数据集上取得当前最佳的识别效果。而在更富有挑战的JHMDB和HMDB51这两个数据集上，本文方法的结果和部分最新研究成果接近。
在KTH、UCF Sports、JHMDB和HMDB51四个数据集上进行了广泛的实验，并对本文提出的TEAB-SAW+和ARCAB-IBoVW方法进行有效性验证，最后与当前最新的研究成果做了对比。结果表明，本文提出的TEAB-SAW+ARCAB-IBoVW算法在KTH、UCF Sports数据集上具有一定的优势，而在JHMDB和HMDB51两个数据集上，本文的算法与最新的成果有一定的可比性，但与最优秀的算法仍存在差距。
未来工作的重点在于研究对人体行为更有效的显著性分析方法，提高底层特征的有效性，进行更富有语义的中高层特征构建，提升分类算法的识别效果，尝试与数据驱动特征结合。
引用文献
[1]Gkioxari G, Girshick R, Malik J. Contextual action recognition with r*CNN[C]// IEEE International Conference on Computer Vision, 2015: 1080-1088.
[2]Lin T Y, RoyChowdhury A, Maji S. Bilinear cnn models for fine-grained visual recognition[C]// IEEE International Conference on Computer Vision, 2015: 1449-1457.
[3]Wang H, Kläser A, Schmid C, et al. Action recognition by dense trajectories[C]// IEEE Conference on Computer Vision and Pattern Recognition, 2011: 3169-3176.
[4]Farnebäck G. Two-frame motion estimation based on polynomial expansion[C]// Scandinavian Conference on Image Analysis, 2003: 363-370.
[5]Arandjelović R, Zisserman A. Three things everyone should know to improve object retrieval[C]// IEEE Conference on Computer Vision and Pattern Recognition, 2012: 2911-2918.
[6]Ma S, Sigal L, Sclaroff S. Space-time tree ensemble for action recognition[C]// IEEE Conference on Computer Vision and Pattern Recognition, 2015: 5024-5032.
[7]Wang H, Kläser A, Schmid C, et al. Action recognition by dense trajectories[C]// IEEE Conference on Computer Vision and Pattern Recognition, 2011: 3169-3176.
[8]Byrne J, Shi J. Nested shape descriptors[C]// IEEE International Conference on Computer Vision, 2013: 1201-1208.
[9]Hasan M, Roy-Chowdhury A K. Context aware active learning of activity recognition models[C]// IEEE International Conference on Computer Vision, 2015: 4543-4551.
[10]Wang H, Kläser A, Schmid C, et al. Action recognition by dense trajectories[C]// IEEE Conference on Computer Vision and Pattern Recognition, 2011: 3169-3176.
[11]Wang H, Schmid C. Action recognition with improved trajectories[C]// IEEE International Conference on Computer Vision, 2013: 3551-3558.
[12]Bay H, Tuytelaars T, Van Gool L. Surf: Speeded up robust features[C]// European Conference on Computer Vision, 2006: 404-417.
[13]Felzenszwalb P F, Huttenlocher D P. Efficient graph-based image segmentation[J]. International Journal of Computer Vision, 2004, 59(2): 167-181. 
[14]Jiang H, Wang J, Yuan Z, et al. Salient object detection: A discriminative regional feature integration approach[C]// IEEE Conference on Computer Vision and Pattern Recognition, 2013: 2083-2090.
[15]Yang J, Yu K, Gong Y, et al. Linear spatial pyramid matching using sparse coding for image classification[C]// IEEE Conference on Computer Vision and Pattern Recognition, 2009: 1794-1801.
