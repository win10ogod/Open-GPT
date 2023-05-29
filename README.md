# Open GPT Training

本项目提供了一个高效训练中文GPT的代码库。

本项目使用了[nanoGPT]框架、新颖的优化器算法[sophia]。

[nanoGPT]: https://github.com/karpathy/nanoGPT
[sophia]: https://github.com/Liuhong99/Sophia

# 试验效果

我们在GPT2上训练2000步，学习率衰减步数为800，网络参数为：
* n_layer:2
* n_head:2
* n_embd:128

| 优化器 | Train loss     | Dev loss       |
|-------|----------------|----------------|
| AdamW | 2.8763         | 2.7097         |
| sophia | 2.7042(+5.98%) | 2.5157(+7.16%) |

> 结论，本项目代码比传统框架训练效果（训练时长不变，训练损失见效）在训练集上提升5.98%，在测试集上提升7.16%。

# 训练环境依赖

本项目在Colab T4 GPU上测评。  
依赖的python库有：
* python 3.10.11
* transformers==4.29.2
* datasets==2.12.0
* tiktoken==0.4.0

# 试验模型的词表

使用了`GPT-2`的词库，50257个词。试验模型参数量为6.83M。

# 数据预处理

预处理脚本为`prepare.py`。训练集包含9996条，测试集6条记录。

# 训练日志

训练参考命令
```shell
python train.py --device=cuda --compile=True --dtype=float16 --eval_iters=1 --log_interval=10 --block_size=64 --batch_size=12 --n_layer=2 --n_head=2 --n_embd=128 --max_iters=2000 --lr_decay_iters=2000 --dropout=0.10
```

全部试验日志在[gpt_pretraining_on_gpt-tiny.ipynb](colab%2Fgpt_pretraining_on_gpt-tiny.ipynb)。

# 多机训练参考

Start pre-training GPT2 Small (125M):

If you have a machine with 10 A5000 (24GB) GPUs,
```
$ torchrun --standalone --nproc_per_node=10 train_sophiag.py config/train_gpt2_small_sophiag.py --batch_size=8 --gradient_accumulation_steps=6
```
If you have a machine with 8 A100 (40GB) GPUs,
```
$ torchrun --standalone --nproc_per_node=8 train_sophiag.py config/train_gpt2_small_sophiag.py --batch_size=12 --gradient_accumulation_steps=5
```


To reproduce the AdamW baseline following [nanoGPT](https://github.com/karpathy/nanoGPT/):
```
$ torchrun --standalone --nproc_per_node=10 train_adam.py config/train_gpt2_small_adam.py --batch_size=8 --gradient_accumulation_steps=6
```


Please adjust ```nproc_per_node```, ```batch_size```, and ```gradient_accumulation_steps``` accordingly if you use other hardware setup. Make sure their product equals 480.


# 引用

如觉得该项目有用，可引用[论文](https://arxiv.org/abs/2305.14342)。

```text
@article{liu2023sophia,
 title={Sophia: A Scalable Stochastic Second-order Optimizer for Language Model Pre-training},
 author={Liu, Hong and Li, Zhiyuan and Hall, David and Liang, Percy and Ma, Tengyu},
 journal={arXiv preprint arXiv:2305.14342},
 year={2023}
}
```


## 致谢
项目作者： Brian Shen. Twitter @dezhou.

建设该项目过程中参考了如下仓库，在这里表示感谢：
- nanoGPT: https://github.com/karpathy/nanoGPT
- sophia: https://github.com/Liuhong99/Sophia


## 免责声明
本项目并非[sophia官方](https://github.com/Liuhong99/Sophia)发布的sophia算法。
该项目中的内容仅供技术研究参考，不作为任何结论性依据。
使用者可以在许可证范围内任意使用该模型，但我们不对因使用该项目内容造成的直接或间接损失负责。


## 关注我们
欢迎关注知乎专栏号。

[深度学习兴趣小组](https://www.zhihu.com/column/thuil)


## 问题反馈 & 贡献
如有问题，请在GitHub Issue中提交。  
我们没有运营，鼓励网友互相帮助解决问题。  
如果发现实现上的问题或愿意共同建设该项目，请提交Pull Request。

