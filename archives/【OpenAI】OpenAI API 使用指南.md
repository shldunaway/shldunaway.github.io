## 获取 OpenAI API Key

### 创建 OpenAI 账号

如果您之前使用过 ChatGPT，那么 ChatGPT 的账号即为 OpenAI 账号，可以直接使用。如果没有账号，您可以在官网注册。 

由于国内的网络环境，注册过程可能较为复杂，网上有很多注册教程，可以自行查阅。如果您觉得麻烦，也可以选择在一些平台购买账号，或者直接购买一个可用的 API Key。

### 获取 OpenAI API Key 

登录后，将鼠标移至页面左侧，您将看到一个弹出的侧边栏。

点击侧边栏中的“API Keys”以进入 API Keys 页面。

在这里，您可以管理（创建、删除等）所有的 API Key。

点击“Create new secret key”以创建新的 API Key，为其命名并确认。 

弹出的对话框会显示您刚创建的 Key，请记得立即保存，因为关闭对话框后将无法再次查看。

保存后点击 Done，您就可以在该页面查看新创建的 API Key 了。

## 获取 API 使用额度

### 额度查询

点击侧边栏中的“Usage”以进入使用页面。该页面左侧显示了每天的花费，右侧则显示了剩余额度。 

在右侧的 Credit Grants 区域，使用三种颜色显示状态：灰色（未使用）、绿色（已使用）、红色（已过期）。只有当额度处于未使用状态（灰色）时，才能成功使用 Key 调用 API。

### 额度充值

点击侧边栏中的“Setting”下的“Billing”以进入账单页面。您可以在此管理充值相关事项。

要进行充值，首先需要添加一种付款方式，点击 Payment methods 可以管理支付方式。

由于网络封锁，国内的 Visa 卡可能无法使用，建议使用国外的卡或网络卡。在这里，我使用的是一种国际支付卡，付款方便，但开卡费用略高（$15.99），充值还有 3.5% 的手续费，使用时根据需求选择即可。

添加付款方式后，返回 Overview 页面，点击 Add to credit balance 进行充值。充值完成后，回到 Usage 页面可查看可用额度变化。

## Python 使用测试

### 配置 Python

确保您的 Python 版本为 3.7.1 及以上。为了方便使用，我选择使用 Anaconda 创建了一个虚拟环境。

### 安装 OpenAI 库

### 设置 API Key

OpenAI 默认会在环境变量中查找 “OPENAI_API_KEY”，并使用该值作为 OpenAI Key。有以下两种设置方法：

1. **为所有项目设置：**
   在系统环境变量中添加 OPENAI_API_KEY（可以通过 Windows 键搜索环境变量打开该页面）。完成后，可在 cmd 中运行 `echo %OPENAI_API_KEY%` 检查是否设置成功。

   python
   from openai import OpenAI
   
   client = OpenAI()
   

2. **为单个项目设置：**
   在项目目录中创建 `.env` 文件（如需使用 Git 管理，请将其添加到 .gitignore），并输入如下内容：
   
   OPENAI_API_KEY=（您的Key）
   

运行测试，没有错误即表示成功。

OpenAI 默认会在环境变量中查找 “OPENAI_API_KEY”。如需使用其他环境变量名，可以这样设置：

python
client = OpenAI(
    api_key=os.environ.get("CUSTOM_ENV_NAME"),
)


### 发送请求测试

下面是基本的 gpt-3.5 chat 请求示例：

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


现在您可以在 [Usage 页面](https://platform.openai.com/usage) 查看此次请求的费用和 token 数量等信息（可能会有延迟）。

## 功能介绍（以 Python 为例）

### 文本生成

可以理解自然语言并返回文本。

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

response['choices'][0]['message']['content']


每个回应中都有 `finish_reason`，如下所示：

- `stop`：API 返回信息已完成。
- `length`：达到最大 token 限制。
- `tool_call`：模型决定调用工具。
- `content_filter`：因内容过滤而隐藏。
- `null`：信息仍在处理中。

### 图像输入

GPT-4 的版本可以理解图像。通过在用户消息中添加 `type` 为 `image_url` 的图像 URL 来实现。

### JSON 输出

如果希望模型输出 JSON 对象，可以将 `response_format` 设置为 `{ "type": "json_object" }`。

### 图像生成

您可以使用 OpenAI 提供的相关功能生成图像。

👉 [野卡 | 一分钟注册，轻松订阅海外线上服务](https://bit.ly/bewildcard)