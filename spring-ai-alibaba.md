## 介绍

简单调用如下图

![](assets/ssa知识1.png)

主要通过与阿里云百炼大模型服务、通义系列大模型做的深度集成与实践

以Spring AI为基础，深度集成百炼平台，支持CharBot、工作流、多智能体应用开发框架

[Spring AI Alibaba 官网(https://java2ai.com/)

[大模型服务平台百炼控制台]https://bailian.console.aliyun.com/?tab=model#/efm/model_experience_center/text)

相关可用链接

![](assets/ssa知识2.png)

~~~text
最新版本可以到GitHub中查找
<dependency>
<groupId>com.alibaba.cloud.ai</groupId>
<artifactId>spring-aii-alibaba-starter-dashscope</artifactId>
<version>1.0.0.2</version>
</dependency>
~~~

![](assets/ssa知识3.png)

spring-ai-alibaba、spring-ai、langchain4j对比

![](assets/ssa知识4.png)

![](assets/ssa知识5.png)

**SpringAI Alibaba、SpringAI、SpringBoot版本依赖**

![](assets/ssa知识6.png)

## 使用模型

**模型调用三件套：**

1. 获取Api-Key(不建议明文存储)
2. 获取模型名
3. 获取baseURL开发地址

bom物料清单管理依赖版本

![](assets/ssa知识7.png)

**配置：**

所有的模型调用均基于OpenAI协议标准或者SpringAIAlibaba官方推荐模型服务灵积(DashScope)整合规则，实现一致的接口设计与规则模型切换的便利性

![](assets/ssa知识8.png)

配置完成之后写yml文件，再者使用config写配置文件注入于程序中

通过DashScopeApi对象注入bean暴露出去

~~~java
package com.suke.ai.config;

import com.alibaba.cloud.ai.dashscope.api.DashScopeApi;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @author 自然醒
 * @version 1.0
 */
@Configuration
public class SaaLLMConfig {

    @Value("${spring.ai.dashscope.chat.api-key}")
    private String apiKey;
    /**
     * 配置LLM
     * 使用yml文件读取配置
     */
    @Bean
    public DashScopeApi dashScopeApi(){
        return DashScopeApi.builder()
                .apiKey(apiKey)
                .build();
    }

    /**
     * 配置LLM
     * 可以通过环境变量读取apiKey
     * System.getenv("DASH_SCOPE_API_KEY")
     */
}
~~~

使用ChatModel对话模型

![](assets/ssa知识9.png)

![](assets/ssa知识10.png)

ChatModel有两个包，一个SpringAi原生，一个SpringAI Alibaba的

SpringAI Alibaba是枚举类

> 注意配置文件yml为
>
> spring.ai.dashscope.api-key=
>
> spring.ai.dashscope.baseurl=(这个也可以省略，alibaba默认存在的)
>
> spring.ai.dashscope.chat.options.model=（默认为qwen-plus）

ChatModel的API调用

```
/**
 * 普通调用
 * 阻塞型
 * @param msg
 * @return
 */
@GetMapping("/hello/dochat")
public String doChat(@RequestParam(name = "msg",defaultValue = "请问你是谁")String msg){
    String callBack = chatModel.call( msg);
    return callBack;
}

/**
 * 流式型调用
 * @param msg
 * @return
 */
@GetMapping("/hello/stream")
public Flux<String> stream(@RequestParam(name = "msg",defaultValue = "请问你是谁")String msg){
    return chatModel.stream(msg);
}
```

**如何切换其他模型：**

![](assets/ssa知识11.png)

DeepSeek主要两个大模型R1和V3

R1具有推理能力，V3主要是对话流

**与OpenAI的协议对比：**

![](assets/ssa知识12.png)

## Ollama

### 本地大模型部署

相关链接：https://ollama.com

ollama相当于docker一样，管理各种大模型

手动创建安装目录

然后在目录中输入cmd命令

OllamaSetup.exe /DIR=所在的目录

创建模型存放目录，配置环境变量

![](assets/ssa知识13.png)

![](assets/ssa知识14.png)

ollama默认端口号：11434

![](assets/ssa知识15.png)

下载命令ollama run 模型名称

![](assets/ssa知识16.png)

/bye退出ollama模型终端

### 微服务对接本地大模型

需要引入的pom

![](assets/ssa知识17.png)

yml文件

![](assets/ssa知识18.png)

同时存在多个ChatModel时需要指定名字

bean注入原理

使用@Resource时指定名字

或者使用

@Resource

@Quarlifier()名字

或者可以用

@Autowired

相对应的bean

OllamaChatModel ollamaChatModel

## 对话模型 ChatClient和ChatModel

ChatClient支持同步和反应式编程模型，提供了Fluent API与AI模型通信

ChatModel只是接收一系列消息作为输入，与模型LLM进行交互，并接收返回的聊天消息作为输出

ChatClient

![](assets/ssa知识19.png)

![](assets/ssa知识20.png)

![](assets/ssa知识21.png)

> ChatClient不支持自动注入，只能手动注入

![](assets/ssa知识22.png)

**构造注入：**

```
private final ChatClient chatClient;

public ChatClientController(ChatModel chatModel) {
    this.chatClient = ChatClient.builder(chatModel).build();
}
```

ChatClient需要使用prompt唤醒

![](assets/ssa知识23.png)

```
@GetMapping("/chatclient/dochat")
public String doChat(@RequestParam(name = "msg",defaultValue = "2加12等于几")String msg){
    String content = chatClient.prompt().user(msg).call().content();
    return content;
}
```

**ChatClient自动注入：**

```
@Bean
@ConditionalOnMissingBean
public ChatClient chatClient(ChatModel chatModel){
    return ChatClient.builder(chatModel).build();
}
```

ChatClient与ChatModel配合使用

![](assets/ssa知识24.png)

## SSE流式输出多模型共存

**流式输出：**

![](assets/ssa25.png)

**SSE服务器发送事件：**

![](assets/ssa26.png)

![](assets/ssa27.png)

![](assets/ssa28.png)

sse下一代(Stream able Http)动态流式，支持双向

sse适用场景：

![](assets/ssa29.png)

**多模型共存：**

![](assets/ssa30.png)

对于ChatClient的实现

![](assets/ssa31.png)

![](assets/ssa32.png)

其实可以不用再去调用api固定其模型名称，因为在上一个ChatModel上面已经实现了，注入ChatClient本身就是需要调用ChatModel，所以不需要再使用ChatOptions去构建要使用的模型，同时在多个模型共存下需要使用注解@Qualifier进行识别

Flux:

![](assets/ssa33.png)

## 提示词Prompt

DeepSeek提示词样例：

https://api-docs.deepseek.com/zh-cn/prompt-library

**提示词：**

![](assets/ssa34.png)

一般使用ChatModel的call方法，该方法接受prompt实例并返回ChatResponse

**prompt>messag>string**

![](assets/ssa35.png)

提示词四大角色：

![](assets/ssa36.png)

它存在于枚举类MessageType中，而MessageType则被使用于Message接口中，Message接口继承于Content文本类

![](assets/ssa37.png)

提示词四大角色

它的主要实现为一个抽象类AbstractMessage

它的四个实现类为如图下的四种

![](assets/ssa38.png)

SYSTEM("system")级别的角色：

设定AI行为边界/角色/定位，指导AI的行为和响应方式，设置如何解释和回复输入

即如果不对其进行设定，它将是原生AI模型，以原生模型进行输出回答但是如果进行设定，比如：你是一个医生，此时它将以医生的角度进行回答问题

USER("user")级别角色：

![](assets/ssa39.png)

ASSISTANT("assistant")级别：

![](assets/ssa40.png)

user和assisant主要是面向对象编程，问什么便打什么，不注重解释，同时可以它是一种历史对话

TOOL("tool")级别角色：主要帮助大模型拓展工具技能

![](assets/ssa41.png)

### 使用

四大角色的分别定义

模版1(ChatClient定义AI能力边界)：

![](assets/ssa42.png)

模版2(ChatModel)：

![](assets/ssa43.png)

ChatResponse以json格式进行返回数据

对ChatResponse数据进行读取

![](assets/ssa44.png)

模版3(AssistantMessage)：

![](assets/ssa45.png)

![](assets/ssa46.png)

模版4(Tool)：

![](assets/ssa47.png)

大模型中的联网查询即是对第三方工具的调用，因为大模型中的数据都是预训练的，都是截止到以前的，不会是现在的或者未来的，比如deepseek的数据现在只存在到2023年的数据，所以如果需要去获取最新的还是要调用第三方工具

### Prompt提示词模版

![](assets/ssa48.png)

提示词模版1：

![](assets/ssa49.png)

模版2(读取模版配置文件)：

![](assets/ssa50.png)

![](assets/ssa51.png)

模版3(设定AI角色，多角色)：

![](assets/ssa52.png)

模版4(人物设定)：

![](assets/ssa53.png)

![](assets/ssa54.png)





