# malicious-URL-detection
基于开源URL数据字符串特征的恶意性检测，本科毕业设计

URL异常检测本质上是一个分类问题，将输入的URL经过处理后得到特征，输入到分类其中，分类器输出分类结果，恶意的还是良性的。
在训练集和验证集的基础上训练了多个分类模型，训练集用于训练、验证集用来调整参数。
## 特征提取
| 基于整体URL的特征                | 基于hostname的特征 | 基于path的特征     | 基于search的特征 | 基于hash的特征 |
| -------------------------------- | ------------------ | ------------------ | ---------------- | -------------- |
| URL长度                          | token个数          | token个数          | token个数        | token个数      |
| 字母比例                         | 特殊字符个数       | path长度           | search长度       |                |
| 数字比例                         | 字母比例           | 目录深度(/)        | 参数个数(&)      |                |
| 特殊符号的种类个数               | 数字比例           | 最长token          | 方差             |                |
| 特殊字符个数                     | 出现点的次数(.)    | 特殊符号的种类个数 |                  |                |
| URL深度(/)                       | 是否是IP地址       | 方差               |                  |                |
| 出现点的次数(.)                  | 熵                 |                    |                  |                |
| [存在@符号](mailto:存在@符号)    | token方差          |                    |                  |                |
| 顶级域名TLD                      | hostname长度       |                    |                  |                |
| 出现恶意词的次数                 | 字母出现次数       |                    |                  |                |
| 出现流行网站名的次数             |                    |                    |                  |                |
| 出现.php或者.exe的次数           |                    |                    |                  |                |
| 在除了开头位置出现http,www的次数 |                    |                    |                  |                |
| 字母出现次数                     |                    |                    |                  |                |

## 特征分析
| 基于整体URL的特征                | 恶意URL平均值 | 良性URL平均值 |
| -------------------------------- | ------------- | ------------- |
| URL长度                          | 55.78         | 47            |
| 字母比例                         | 0.79          | 0.79          |
| 数字比例                         | 0.075         | 0.06          |
| 特殊符号的种类个数               | 0.94          | 1.03          |
| 特殊字符个数                     | 2.07          | 2.58          |
| URL深度(/)                       | 2.78          | 2.32          |
| 出现点的次数(.)                  | 2.219         | 1.66          |
| [存在@符号](mailto:存在@符号)    | 0.022         | 0             |
| 出现恶意词的次数                 | 2.617         | 1.44          |
| 出现流行网站名的次数             | 0.922         | 0.67          |
| 出现.php或者.exe的次数           | 0.279         | 0.04          |
| 在除了开头位置出现http,www的次数 | 0.039         | 0             |

| 基于hostname的特征 | 恶意URL平均值 | 良性URL平均值 |
| ------------------ | ------------- | ------------- |
| token个数          | 2.48          | 2.35          |
| 特殊字符个数       | 0.15          | 0.06          |
| 字母比例           | 0.887         | 0.9           |
| 数字比例           | 0.022         | 0.006         |
| 出现点的次数(.)    | 1.346         | 1.29          |
| 熵                 | 3.398         | 3.31          |
| token方差          | 3.943         | 3.2           |
| hostname长度       | 15.8          | 14.36         |



| 基于path的特征     | 恶意URL平均值 | 良性URL平均值 |
| ------------------ | ------------- | ------------- |
| token个数          | 3.48          | 4.25          |
| path长度           | 21.466        | 23.18         |
| 目录深度(/)        | 1.739         | 1.31          |
| 最长token          | 1.57          | 0.92          |
| 特殊符号的种类个数 | 0.795         | 0.97          |
| 方差               | 2.27          | 1.71          |

| 基于search的特征 | 恶意URL平均值 | 良性URL平均值 |
| ---------------- | ------------- | ------------- |
| token个数        | 1.46          | 0.56          |
| search长度       | 11.04         | 2.75          |
| 参数个数(&)      | 0.27          | 0.076         |
| 方差             | 1.05          | 0.37          |





## 模型训练
在提取特征的基础上，使用sklearn库中自带的分类模型在训练集上进行模型训练，根据各个分类器在验证集上的效果，在投票时设置了不同的权重。精确率高的分类器权重更高，也就是我们认为它的结果更加可信。
阈值在代码里的设置是，随机森林或者梯度提升树中只要有一个认为URL是恶意的，那就是恶意的，因为这两个模型的准确率比较高。剩下的四个分类器需要同时认为该URL是恶意的时候 才认为它是恶意的

## 实验结果
从kdnuggets上收集到了带标签（good/bad）的URL数据集，共416350条，其中异常数据（bad）71556条，占比17.19%；
正常数据（good）344794条，占比82.81%。

将全体数据划分为训练集（70%），验证集（15%）和测试集（15%），并且在每个集合中均保持异常数据所占比例相同。


