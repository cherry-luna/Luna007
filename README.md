# Luna2501
不知道干啥的，诶，创建一个看看是啥子吧。

哦，最近计划将一个项目放在这里，是关于 乄（无）先验的接收端IQ采样序列码元宽度回归预测 的一个工程项目。简单说是，诶这是啥子类型的信号我都不知道，就按一定采样率将接收到的信号以IQ形式记录下来，现在你知道这个信号的码元宽度是多大嘛？我不知道，但可以被估计出来。

闲来无事，学习一下怎么放项目文件上去也不错，记录一下。

————————————————
By 2025.04.02
故地仙游，不多说，先上传一个。

不是哥们，怎么我上传的没认出来是我？坏了，真是那我问我了…………
什么？这些文件是干嘛用的？简单，英文名对应代码的主要内容。

总的来说，该项目依靠 SignalDataset.py 和 DatasetManager.py 制作h5本地化数据集，并保存训练集、验证集与测试集（已设置622比例）索引文件；MAPE_Loss_func.py规定MAPE LOSS函数；IQPhase.py进行IQ相位校正；optim_criter_sched_factory.py设置一些常用的优化器、调节器和判别函数；其余的应该不需要说了。

这个复合网络思路是：Resnet多尺度时域特征提取 + 时频域与统计特征融合 + Transformer回归预测。
首先，IQ数据不等长，因此需要合理保存IQ数据，进行IQ相位校正后本地化为h5数据集。然后，全程采用MAPE损失函数，先使用Radam算法训练100轮让LOSS快速下降，再切换SGD算法训练100轮进行模型调优，最后使用AdamW算法训练100轮进行模型微调。（针对我个人的数据集）LOSS曲线参见loss_curve_1&2.png和loss_curve_3.png。你可根据实际情况进行调整。

可能你会发现，我是代码和DL的小白，甚至GitHub都不会用。但至少能用。以SW分数为指标，最终测试分数达到92分附近，R²为0.97左右，绝大多数预测值均在20%以内（参见- Absolute Error Distribution.png）

倘若你想了解具体的架构，可以去看model_arch.py或者architecture.png（draw.py跑出来的），实在想知道是啥玩意直接私我邮箱。估计也没人会看hhh，就这样吧。

对了，这个项目怎么拉人来着？改日学习学习。
________________
By 2025.04.03
好玩，贡献者遁入虚空，不愧是我！
无妨，至少文件还在，能用就行，过段时候把PPT讲解附上，说不定哪天又能拿起来这个网络用。
该启动仙战了……

实现了Hi和666的贡献，成功拉拢合伙人（不是）
还是随手打个贴士吧！

***********************  
该工程主要是针对以csv文件形式保存的10种已知调制信号的IQ序列样本，解析出码元宽度。已知数据的采样率为20MHz，涵盖不同的信号长度、码元宽度、SNR及多径的样本数据，其中第一二列分别为仿真IQ波形，第五列为仿真码元宽度。
因此，如何利用IQ波形采样信号序列预测出码元宽度将是本项目的核心课题。

即便如此，仍然能够利用DL的深层次抽象特征的特性，直接爆破解析不等长IQ信号的码元宽度。依据我的理解，该深度学习模型必定能够发现IQ波形当中代表码元宽度的隐藏抽象特征，而且回归预测的效果还不赖。
那么，接下来改进的工作方向其实也很明显，如何进一步让模型更容易、更精确地找到这个抽象特征，就能让SW分数更高，回归预测效果更好。目前我的想法是改进数据预处理（当前是IQ相位校正）方式，替换为可学习的预处理网络模块。

SW 分数的计算方式如下：
先计算单样本误差比ER：
$$
\ER_{i}=\frac{\left | y_{i} -y_{i}^{'}  \right | }{y_{i}^{'}}  
$$
ER在5%内得100分；在20%外得0分；区间内线性下降。
完毕。
***********************  
