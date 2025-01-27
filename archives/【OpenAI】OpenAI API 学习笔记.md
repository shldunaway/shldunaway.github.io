## 获取 OpenAI API Key

### 注册 OpenAI 账号

如果您曾使用过 ChatGPT，您可以直接使用之前的 ChatGPT 账号作为 OpenAI 账号。如果您没有账号，可以在官方网站上进行注册。

请注意，由于国内的一些限制，注册过程可能会有些复杂。互联网上有许多注册教程，可以参考；如果不想麻烦，也可以在电商平台购买账号，或者直接购买一个有额度的 API Key。

### 获取 OpenAI API Key

登录后，将鼠标移动到页面左侧，您会看到一个弹出的侧边栏。点击侧边栏中的“API Keys”以进入 API Keys 页面。

在这里，您可以管理（创建、删除等）所有的 API Key。点击“Create new secret key”创建新 API Key，给它命名后点击确认。请务必立即保存此 Key，因为关闭对话框后将无法再次查看。

保存完成后，点击 Done，您将能够在页面上看到新创建的 API Key。

## 获取 API 使用额度

### 额度查询

点击侧边栏中的“Usage”进入使用情况页面。这个页面的左侧显示了每天的花费，右侧则显示您的可用额度。在 Credit Grants 区域，额度以三种颜色显示：灰色（未使用）、绿色（已使用）、红色（已过期）。只有处于未使用状态（灰色）的额度时，才可以成功调用 API。

### 额度充值

点击侧边栏中的“Setting”下的“Billing”进入账单页面。在这个页面中，您可以管理充值相关事项。

充值前首先需要添加一种付款方式，点击 Payment methods 来管理支付方式。由于某些封锁原因，国内的 Visa 卡可能无法使用，建议使用国外的卡，或在线支付工具。为了方便，我使用的是某种在线支付卡，尽管开卡费较高（$15.99），而且充值会有 3.5% 的手续费。

添加支付方式后，回到概览页面点击 Add to credit balance 进行充值。充值完成后，您可以在 Usage 页面查看可用额度的变化。

## Python 使用测试

### 配置 Python 环境

确保 Python 版本为 3.7.1 或以上。为了方便使用，我使用 Anaconda 创建了一个虚拟环境。

### 安装 OpenAI 库

### 设置您的 API Key

OpenAI 默认将从环境变量中查找“OPENAI_API_KEY”，以用作 API Key。设置 API Key 的方法有以下两种：

1. **为所有项目设置**：
   在系统环境变量中添加 OPENAI_API_KEY（您可以通过搜索“环境变量”来打开这个设置页面）。设置完成后，可以打开命令提示符，输入 `echo %OPENAI_API_KEY%` 检查是否设置成功。
   
   python
   from openai import OpenAI

   client = OpenAI()
   

2. **为单个项目设置**：
   在项目文件夹中创建 `.env` 文件，输入 `OPENAI_API_KEY=（您的 Key）`。

   plaintext
   # Once you add your API key below, make sure to not share it with anyone!
   OPENAI_API_KEY=abc123
   

运行测试时无报错即为成功。

若您希望使用其他环境变量名，可以如下设置：

python
client = OpenAI(
    api_key=os.environ.get("CUSTOM_ENV_NAME"),
)


### 发送请求测试

下面是一个简单的 gpt-3.5 聊天请求示例：

python
import os
import dotenv
from openai import OpenAI

dotenv.load_dotenv()

client = OpenAI(
    api_key=os.environ.get("OPENAI_API_KEY"),
)

# 发送请求
completion = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "system", "content": "You are a poetic assistant, skilled in explaining complex programming concepts with creative flair."},
        {"role": "user", "content": "Compose a poem that explains the concept of recursion in programming."}
    ]
)

# 打印回复
print(completion.choices[0].message.content)


在 [Usage 页面](https://platform.openai.com/usage) 您可以查看这次请求的费用、token 数量等信息（可能会有延迟）。

## 功能介绍（以 Python 为例）

### 文本生成

可以理解语言（GPT-4 版本也可以理解图像）并返回文字。主要的输入是 messages，message对象由 role（system, user 或 assistant）和 content 组成。

- **system**（可选）：用于设置 AI 的行为。
- **user**：AI 要回应的信息。
- **assistant**：之前 AI 回复的信息，也可以用于提供参考。

python
response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Who won the world series in 2020?"},
        {"role": "assistant", "content": "The Los Angeles Dodgers won the World Series in 2020."},
        {"role": "user", "content": "Where was it played?"}
    ]
)


每个回复中包含 **finish_reason**：

- **stop**：API 返回信息已完成。
- **length**：到达了 max_token 参数限制。
- **tool_call**：模型决定调用工具。
- **content_filter**：内容由于过滤器被隐藏。
- **null**：信息尚未完成。

### 图像输入

GPT-4 的视觉版本可以理解图像，您只需在 user message 的 content 中添加 type 为 image_url 的图像 URL。

若您希望模型始终输出 JSON 对象，可以将 response_format 设置为 `{ “type”: “json_object” }`。

### 图像生成

👉 [野卡 | 一分钟注册，轻松订阅海外线上服务](https://bit.ly/bewildcard)