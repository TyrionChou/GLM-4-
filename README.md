# GLM-4模型微调教程

GLM-4模型微调教程

1、 环境配置

    我的系统环境是:

    OS: Ubuntu 20.04

    Memory: 512GB

    Python: 3.12.7

    CUDA Version: 12.4

    GPU Driver: 550.67

    GPU: NVIDIA GeForce RTX 3090 24G
    
    1）官方代码下载
       代码地址：https://github.com/THUDM/GLM-4/tree/main
       
    2）官方模型下载
        模型地址：https://modelscope.cn/models/ZhipuAI/glm-4-9b-chat

    使用pip install -r requirement.txt安装inference、finetune所需的运行环境，需要注意的是自动安装的torch和torchvision可能和CUDA版本不一致，这里可以自己从官网下载，否则可能会导致训练时bitsandbytes报错！！！
    经过上述步骤后，可以进入inference文件夹，修改trans_cli_demo.py中MODEL_PATH = "THUDM/GLM-4-9B-0414"的路径为自己的模型路径，运行python trans_cli_demo.py，就可以在终端进行对话测试了
2、 微调准确

    1）数据准备，从https://huggingface.co/datasets/qgyd2021/chinese_ner_sft/tree/main/data下载的是一个公开的命名实体识别数据集ccfbdci.jsonl，需要将数据格式转化为

    {
      "message":[
      {
          "role": "system",
          "content": "..."
      },
      {
          "role": "user",
          "content": "..."
      },
      {
           "role": "assistant",
          "content": "..."
      }
      ]
    }
    2) 调整运行参数，调整finetune文件夹config/lora.yaml中的参数，
        减少max_input_length、max_output_lenghth为128， training_args中使用混合精度训练设置bf16：True，添加梯度检查点，用时间换显存设置gradient_checkpointing: true，运行CUDA_VISIBLE_DEVICES="0" python finetune.py  data/AdvertiseGen/  THUDM/GLM-4-9B-0414  configs/lora.yaml即可
        刚开始训练过程中可能会遇到No module named mpi4py自行安装即可，
        评测计算blue值过程中可能会遇到nltk版本和python3.12不兼容的问题，通过删除bleu_score.py中两处_normalize=Truej即可
3、 微调部署

    训练好后，可以直接通过python trans_cli_demo.py调用模型
