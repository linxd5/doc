# Cartpole示例

本示例主要是基于Cartpole环境来模拟线上的交互环境，演示ESAgent相关接口的使用。

CartPole又叫倒立摆。小车上放了一根杆，杆会因重力而倒下。为了不让杆倒下，我们要通过移动小车，来保持其是直立的，如下图所示。 在每一个时间步，模型的输入是一个4维的向量（小车位置、小车速度、杆子夹角及角变化率），表示当前小车和杆的状态，模型输出的信号用于控制小车往左或者右移动。当杆没有倒下的时候，每个时间步，环境会给1分的奖励；当杆倒下后，环境不会给任何的奖励，游戏结束。

<p align="center">
<img src=".images/Cartpole.gif" width=500/>
</p>



示例路径：./demo/cartpole/

主要代码模块说明：
- `gen_init_model.py`：用于生成一个初始化模型；
- `config.prototxt`：参数配置文件（算法、优化器、奖励归一化器等参数配置）；
- `train.cc`：训练主要文件，包括演示了下面接口的使用：
  - 模拟线上采样
    ```C++
    auto sampling_agent = agent.clone(); // 克隆一个采样agent
    bool success = sampling_agent.add_noise(sampling_info); // 给采样agent的模型增加噪声，并把噪声key保存到SamplingInfo
    sampling_agent.predict(feature);  // 带噪声的采样agent进行推理，和环境进行交互
    ```
  - 模拟线上反馈，定义了`evaluate`函数来模拟线上的评估奖励，这里根据环境完成一个回合的总得分作为奖励。
  - 模拟线下更新
    ```C++
    agent->update(sampling_infos, rewards) // 根据采样agent的噪声和奖励计算模型更新梯度。
    ```
  

## 如何运行
1. 下载代码
    - 在icode上clone代码，仓库路径： http://icode.baidu.com/repos/baidu/nlp/evokit/tree/stable

2. 编译demo
    - 通过bcloud的云端集群编译即可，命令为：
    ```
    bcloud build
    ```

3. 运行demo
    - 编译完成后，我们需要增加动态库查找路径：
    ```
    export LD_LIBRARY_PATH=./output/so/:$LD_LIBRARY_PATH
    ```
    - 运行demo： 
    ```
    ./output/bin/cartpole/train
    ```
    【注意: 运行时需确保当前的路径为项目根路径】

## 运行结果
运行200 epochs后可以达到201最高分。
