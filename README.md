<div align="center">
<img src="./image/logo.jpg"width="300em" ></img> 
</div>

## 天工开悟-农业问答大模型(KwooLa)


>**天工不遗，以配万物；开悟不止，以成百谷。**


### :fire::fire:News

- [2024/11/20] :boom::boom:**推出了API访问方式**，开发者和企业用户可以轻松集成KwooVA的强大功能，全面赋能农业领域的智能化升级，助力打造多样化、定制化的智慧农业解决方案。
- [2024/11/20] :star2::star2:**更新了KwooVA的APP使用教程和测试用例**，让用户能够快速上手，充分体验KwooVA在农业生产中的高效性与便捷性，推动智能农业的普及和落地。
- [2024/11/02] :rose::rose:**发布了KwooLa**，面向大田作物种植咨询、种植作物推荐、种植全流程管理、病虫草害诊断与防治等农事场景，能够为农业从业人员提供信息丰富、精准度高、可操作性强、农业知识对齐的决策指导！支持[Web端](https://www.tgkwai.com/) 、Android端APP访问(下载APP请点击[这里](#天工开悟app-android版))。

### 模型简介

**天工开悟-农业问答大模型（KwooLa）** 集知识问答、农事决策指导、多轮对话、联网检索等功能于一体，致力于向大田作物作物品种推荐、种植全流程管理、病虫草害诊断与防治等真实的农事场景提供精准可靠、信息丰富、与农业基准一致、可操作的决策指导。

KwooLa融合了**知识协同微调**及**混合检索增强**技术，实现了农业知识的隐式参数注入及生成显式约束。

#### :book:知识协同微调

为更好实现模型在训练过程中对农业专业token的关注，我们借鉴有监督微调（如LoRA）以及Focus Learning提出知识协同微调。我们将标准回复的每个token与对应知识通过余弦相似度计算相关度，通过函数变换后得到每个位置的重要性，相关度越高的位置重要性越高。然后通过增大重要性高的位置的概率，从而增大这个位置对训练中损失的影响程度，最终达到提高模型对此位置关注度的效果。

#### :page_with_curl:混合检索增强

KwooLa从多源知识库中进行混合检索增强，**融合了BGE密集检索及BM25稀疏检索**。**除本地知识库外，KwooLa接入了在线检索功能**，有效利用专业书籍知识、广泛的网页知识为模型回复提供引文标注，实现知识可溯源。



:rice_scene:KwooLa开放的测试版本以Qwen1.5-14B-Chat模型为Base模型，迁移了知识协同微调及混合检索增强。**KwooLa国内首个成功备案的农业类大模型，为构建智慧农业大脑提供了助力。**天工开悟-农业问答大模型具有以下优势：

:one:在农业领域的回复更加细粒度，例如可以较为精准的给出病虫害防治方法及相关药物剂量和配比；

:two:具有一定的可解释性，可以针对问题给出具有相关知识作为引文的回复；

:three:信息量高，知识一致性高在知识引导下生成回复；

:four:流畅性好，可以针对农业问题生成更加合理的回复；

:five:可执行度大，可操作性强，基于农业问题给出的指导决策在一定程度上可直接进行实际应用，如品种推荐，种植流程管理；

:six:领域一致性强，农业数据及知识协同驱动，与农业领域的知识高度对齐，回复置信度高；

:seven:准确性高，与农业标准对照具有极高相似度。



### 数据构建

KwooLa收集了百度百科、种业商务网、《中国农作物病虫害》等相关农业网站和农业专业书籍的知识，在农学专家指导下制定了标注规范并在此基础上构建了**40万单轮问答数据**，同时保留相关知识构建了包含11万条农业知识的本地知识库。数据初版涉及的农作物品种及病虫害类别如下，目前仍在持续扩充中。


<div align="center">
<img src="./image/数据.png" width="100%" ></img> 
</div>

为避免模型灾难性遗忘，同时提升其领域多轮对话能力，**KwooLa使用Chat-GPT-4o-mini自动化构造了20万轮多轮农业对话数据，并混合农业领域单轮、通用领域单轮、农业领域多轮、通用领域多轮进行训练**。



### 模型效果

为对比框架有效性，我们将上述知识注入架构广泛迁移到现有开源和闭源模型如百川、ChatGLM3、Llama2、Qwen1.5、ChatGPT3.5等，实验结果表明我们所提方法在回复生成流畅性、准确性、真实性、领域忠诚度方面都有明显提升，达到sota。

<div align="center">
<img src="./image/模型对比.png" width="80%" ></img> 
</div>

<div align="center">
<img src="./image/04_2_human_evaluate_on_model_zh(1).svg" width="90%" ></img> 
</div>

<div align="center">
<img src="./image/对比.png" width="80%" ></img> 
</div>

### API调用

#### 获取访问密钥

登录https://www.tgkwai.com/ 获取`acces token`。

#### 创建链接

使用WebSocket创建对话链接，`session_id`是会话号（建议一次对话使用一个session_id）。

```
# 如果没有 session_id

wscat -c "wss://api.tgkwai.com/api/v1/qamodel/session?x-token=sk-xxxx"

# 如果有 session_id

wscat -c "wss://api.tgkwai.com/api/v1/qamodel/session?x-token=sk-xxxx&session_id=a8d10ffa-60ba-4af1-916e-6a918c0096e3"
```

#### 发送消息

```
# 在连接建立后，输入以下 JSON 数据并发送

{
  "session_id": "a8d10ffa-60ba-4af1-916e-6a918c0096e3",
  "messages": [
    {
      "role": "user",
      "type": "text",
      "content": "玉米最常遭受的病害是什么？如何防治？",
    }
  ],
  "stream": true
}
```

[API详细说明文档](KwooLa-API使用指南.md)

### 项目参与者

本项目参与成员：[闫莲](https://github.com/YanPioneer?tab=repositories) 、[夏振博](https://github.com/1190201219) 、[刘海峰](https://github.com/GodeCAt)、[殷颢瑄](https://github.com/greenjerry) 、[王松源](https://github.com/hit-wsy) 。

指导老师：[刘劼]() 教授、[姜京池](https://homepage.hit.edu.cn/jiangjingchi) 副教授、[杨洋](https://ai.cust.edu.cn/szdw/zrjs/3f60de2fb4634ee189b2cb9ce84a2d98.htm) 副教授以及[关毅](https://homepage.hit.edu.cn/guanyi) 教授。

### 天工开悟APP (Android版)

欢迎大家扫描下方二维码下载**天工开悟APP**进行体验:sparkles::sparkles:。

使用方法：第一步，打开APP，输入**邀请码(8t6nwq)** 进入登录页面，随后切换到“密码登录”页面输入账号、密码并勾选同意协议选项进行登录。第二步，登录后进入问答页面，在下方输入框中输入想要询问的问题，等待回答。

<div align="center">
<img src="./image/下载.png" width="50%" ></img> 
</div>

### 致谢

我们的工作受到以下工作的启发，在此对项目成员表示诚挚的感谢。

- [BGE](https://github.com/FlagOpen/FlagEmbedding/tree/master/FlagEmbedding/BGE_M3)
- [Focus Learning](https://github.com/deng1fan/LazyProjects)
