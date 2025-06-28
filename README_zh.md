# GLM-4.1V-Thinking

[Read this in English.](./README.md)


<div align="center">
<img src=resources/logo.svg width="40%"/>
</div>
<p align="center">
    👋 加入我们的 <a href="resources/WECHAT.md" target="_blank">微信</a> 
    <br>
    💡 在线体验 <a href="https://huggingface.co/spaces/THUDM/GLM-4.1V-9B-Demo" target="_blank">GLM-4.1V-9B-Thinking</a> 
    <br>
    📍 在 <a href="https://open.bigmodel.cn/?utm_campaign=open&_channel_track_key=OWTVNma9">开放平台</a> 使用 GLM-4.1V-9B-Thinking 的 API服务。
</p>

## Demo展示

## 模型介绍

基于[GLM-4-9B-0414](https://github.com/THUDM/GLM-4) 基座模型，我们推出新版VLM开源模型**GLM-4.1V-Thinking**
，探索推理模型在视觉语言模型的多个领域中的上限。与上一代的 CogVLM2 及 GLM-4V 系列模型相比，**GLM-4.1V-Thinking** 有如下改进：

1. 系列中首个推理模型，不仅仅停留在数学领域，在多个子领域均达到世界前列的
2. 支持 **8K** 文本长度。
3. 支持高达 **1344 * 1344** 的图像分辨率。
4. 提供支持**中英文双语**的开源模型版本。

## 模型信息

你可以在这里找到我们开源的模型：

| 模型                   | 下载地址                                                                                                                                                                                                                          | 推理最低显存 | 模型类型 |
|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|------|
| GLM-4.1V-9B-Thinking | [🤗Hugging Face](https://huggingface.co/THUDM/GLM-4.1V-9B-Thinking)<br> [🤖 ModelScope](https://modelscope.cn/models/ZhipuAI/GLM-4.1V-9B-Thinking)<br> [🧩 Modelers](https://modelers.cn/models/zhipuai/GLM-4.1V-9B-Thinking) | 28GB   | 推理模型 |
| GLM-4.1V-9B-Base     | [🤗Hugging Face](https://huggingface.co/THUDM/GLM-4.1V-9B-Base)<br> [🤖 ModelScope](https://modelscope.cn/models/ZhipuAI/GLM-4.1V-9B-Base)<br> [🧩 Modelers](https://modelers.cn/models/zhipuai/GLM-4.1V-9B-Base)             | 28GB   | 基座模型 |

## 榜单信息

## 模型推理

模型推理代码均在 `inference` 中，包含了:

+ `trans_infer_cli.py`: 使用`transformers`库作为推理后端的命令行交互脚本。你可以使用它进行连续对话。
+ `trans_infer_gradio.py`: 使用`transformers`库作为推理后段的 Gradio 界面脚本，搭建一个可以直接使用的 Web
  界面，支持图片，视频，PDF，PPT等多模态输入。
+ 使用`vllm`直接拉起`OpenAI`格式的API服务。并在`vllm_api_request.py`中提供了一个简单的请求示例。

```shell
vllm serve THUDM/GLM-4.1V-9B-Thinking  --allowed-local-media-path /
```

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
  },
  ...
  //这里放更多数据
]
```

1. `<think> XXX </think>` 中的部分不会被存放为历史记录和微调。
2. `<image>` 标签会被替换成图片信息。

接着，即可按照 LLaMA-Factory 的微调方式进行微调。

## 模型协议

+ 本仓库代码遵循[Apache License 2.0](LICENSE) 协议。
+ GLM-4.1V-9B-Thinking 和 GLM-4.1V-9B-Base 模型均采用 MIT协议。
