# GLM-4.1V-Thinking

[Read this in English.](./README.md)

<div align="center">
<img src=resources/logo.svg width="40%"/>
</div>
<p align="center">
    👋 加入我们的 <a href="resources/WECHAT.md" target="_blank">微信</a> 和 <a href="https://discord.com/invite/8cnQKdAprg" target="_blank">Discord</a> 社区。
    <br>
    💡 在线体验 <a href="https://huggingface.co/spaces/THUDM/GLM-4.1V-9B-Thinking-Demo" target="_blank">GLM-4.1V-9B-Thinking</a>
    <br>
    📍 在 <a href="https://open.bigmodel.cn/?utm_campaign=open&_channel_track_key=OWTVNma9">开放平台</a> 使用 GLM-4.1V-9B-Thinking 的 API服务。
</p>

## Demo展示

## 模型介绍

基于 [GLM-4-9B-0414](https://github.com/THUDM/GLM-4) 基座模型，我们推出新版VLM开源模型**GLM-4.1V-Thinking**
，探索推理模型在视觉语言模型的多个领域中的上限。与上一代的 CogVLM2 及 GLM-4V 系列模型相比，**GLM-4.1V-Thinking** 有如下改进：

1. 系列中首个推理模型，不仅仅停留在数学领域，在多个子领域均达到世界前列的水平。
2. 支持 **8K** 文本长度。
3. 支持高达 **1344 * 1344** 的图像分辨率。
4. 提供支持**中英文双语**的开源模型版本。

## 模型信息

### 模型下载地址

| 模型                   | 下载地址                                                                                                                                                                                                                          | 模型类型 |
|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------|
| GLM-4.1V-9B-Thinking | [🤗Hugging Face](https://huggingface.co/THUDM/GLM-4.1V-9B-Thinking)<br> [🤖 ModelScope](https://modelscope.cn/models/ZhipuAI/GLM-4.1V-9B-Thinking)<br> [🧩 Modelers](https://modelers.cn/models/zhipuai/GLM-4.1V-9B-Thinking) | 推理模型 |
| GLM-4.1V-9B-Base     | [🤗Hugging Face](https://huggingface.co/THUDM/GLM-4.1V-9B-Base)<br> [🤖 ModelScope](https://modelscope.cn/models/ZhipuAI/GLM-4.1V-9B-Base)<br> [🧩 Modelers](https://modelers.cn/models/zhipuai/GLM-4.1V-9B-Base)             | 基座模型 |

模型算法代码可以查看 [transformers](https://github.com/huggingface/transformers/tree/main/src/transformers/models/glm4v)
的完整实现。

### 运行要求

#### 推理

| 设备          | 框架           | 最低显存占用 | 速度                 | 精度   |
|-------------|--------------|--------|--------------------|------|
| NVIDIA A100 | transformers | 22GB   | 14 - 22 Tokens / s | BF16 |
| NVIDIA A100 | vLLM         | 25GB   | 38 - 60 Tokens / s | BF16 |

#### 微调

| 设备          | 策略         | 最低显存占用 | Batch Size | 精度   | 冻结情况   |
|-------------|------------|--------|------------|------|--------|
| NVIDIA A100 | LORA       | 21GB   | 1          | BF16 | 冻结 VIT |
| NVIDIA A100 | FULL ZERO2 | 280GB  | 1          | BF16 | 冻结 VIT |
| NVIDIA A100 | FULL ZERO3 | 192GB  | 1          | BF16 | 冻结 VIT |
| NVIDIA A100 | FULL ZERO2 | 304GB  | 1          | BF16 | 不冻结    |
| NVIDIA A100 | FULL ZERO3 | 210GB  | 1          | BF16 | 不冻结    |

> 使用 Zero2 微调可能出现 Loss 为 0 的情况，建议使用 Zero3 进行微调。

## 榜单信息

## 模型推理

模型推理代码均在 `inference` 文件夹中，包含了:

+ `trans_infer_cli.py`: 使用`transformers`库作为推理后端的命令行交互脚本。你可以使用它进行连续对话。
+ `trans_infer_gradio.py`: 使用`transformers`库作为推理后段的 Gradio 界面脚本，搭建一个可以直接使用的 Web
  界面，支持图片，视频，PDF，PPT等多模态输入。
+ 使用`vllm`直接拉起`OpenAI`格式的API服务。并在`vllm_api_request.py`中提供了一个简单的请求示例。

```shell
vllm serve THUDM/GLM-4.1V-9B-Thinking  --allowed-local-media-path /
```

+ `trans_infer_bench`：用于学术复现的推理脚本，支持`GLM-4.1V-9B-Thinking`模型。其核心在于
  + 指定了中断思考的长度，当思考长度超过`8192`时，强制中断思考并补上`</think>\n<answer>`再次发起请求，让模型直接输出答案。
  + 该方案仅提供 `transformers` 版本，vLLM版本需要自行根据该逻辑修改方案。
  + 该例子中使用的一个视频作为输入的测试的例子。

## 模型微调

[LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory) 已经支持本模型的微调。以下是构建数据集的说明，这是一个使用了两张图片的数据集。你需要将数据集整理为
`finetune.json`

```json
[
  {
    "messages": [
      {
        "content": "<image>Who are they?",
        "role": "user"
      },
      {
        "content": "<think>\nUser ask me to observe the image and get the answer. I Know they are Kane and Gretzka from Bayern Munich.</think>\n<answer>They're Kane and Gretzka from Bayern Munich.</answer>",
        "role": "assistant"
      },
      {
        "content": "<image>What are they doing?",
        "role": "user"
      },
      {
        "content": "<think>\nI need to observe what this people are doing. Oh, They are celebrating on the soccer field.</think>\n<answer>They are celebrating on the soccer field.</answer>",
        "role": "assistant"
      }
    ],
    "images": [
      "mllm_demo_data/1.jpg",
      "mllm_demo_data/2.jpg"
    ]
  }
]
```

1. `<think> XXX </think>` 中的部分不会被存放为历史记录和微调。
2. `<image>` 标签会被替换成图片信息。

接着，即可按照 LLaMA-Factory 的微调方式进行微调。

## 模型协议

+ 本仓库代码遵循[Apache License 2.0](LICENSE) 协议。
+ GLM-4.1V-9B-Thinking 和 GLM-4.1V-9B-Base 模型均采用 MIT协议。
