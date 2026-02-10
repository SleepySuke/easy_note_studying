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

## 格式化输出

![](assets/ssa55.png)

期待模型不再返回一个java类或者json，可以使用record类进行返回

jdk14以后的新特性，record类

record=entity+lombok

![](assets/ssa56.png)

此时使用record类的时候，不再使用string类型的user()，可以使用Consumer类型的user()，它是一个函数式接口，需要重新实现

此时的返回也需要改变，使用entity进行返回record类，填写其字节码

![](assets/ssa57.png)

![](assets/ssa58.png)

## ChatMemory连续对话保存和持久化

![](assets/ssa59.png)

记忆类型：

![](assets/ssa60.png)

持久化可以保存到redis中，消息对话存在一个上限的

需要引入的依赖：

![](assets/ssa61.png)

1.ChatMemoryRepository接口

2.MessageWindowChatMemory消息窗口聊天记忆

3.顾问（Advisors）MessageChatMemoryAdivosr

需要一个RedisMemoryConfig配置类

![](assets/ssa62.png)

![](assets/ssa63.png)

因为会有多个用户进来对其进行询问，此时可以使用Advisors顾问，对其进行回答，当用户进来提问时，会将该用户的id进行保存，然后advisors进行回答时，通过该id进行回答，此时会将其信息id存入redis中

定义配置类的时候需要改变，需要存入redis的时候，需要明确存入地方类型的库，比如如下：



![](assets/ssa64.png)

![](assets/ssa65.png)

## 通义万相-文生图

阿里云的两个文生图

通义千问(Qwen-Image)：擅长渲染复杂的中英文文本

通义万相(Wan系列)：用于生成写实图像和摄影级视觉效果

![](assets/ssa66.png)

## 文生音

语音合成（cosyvoice）

SpeechSynthesizer

![](assets/ssa67.png)

同步调用

![](assets/ssa68.png)

同步调用比较适合短文本语音合成场景，如果是需要长文本，比较推荐使用流式

DashScopeSpeechSynthesisModel模型

![](assets/ssa69.png)

使用操作

![](assets/ssa70.png)

![](assets/ssa71.png)

![](assets/ssa72.png)

## 向量化

何为向量：

![](assets/ssa73.png)

文本向量化：

使用Embedding Model将数据转为数组，同时打到坐标轴上

![](assets/ssa74.png)

![](assets/ssa75.png)

形成一种指征描述

比如汽车可以有轮子、是否有发动机、是否能在陆地上开、最大乘客数

此时使用向量可以如此表示汽车

Car(4,1,1,5)，这样放置坐标轴上描述

**如何确定哪些最相似：**

![](assets/ssa76.png)

使用余弦相似度来比较

大多数相似度测量均依赖于方向或者同时考虑方向和大小

但是一般使用方向加大小进行比较

**余弦相似度公式：**

consine_similarity(A,B) = (A dot B) / (||A|| * ||B||)

![](assets/ssa77.png)

点积：

a=(x1,y1,z1) b=(x2,y2,z2)

a dot b = x1*x2+y1*y2+z1*z2 （从代数角度看）

几何角度如图：

![](assets/ssa78.png)

![](assets/ssa79.png)

**向量数据库：**

一种专门用于存储、管理和检索向量数据（高维数值数组）的额数据库系统

核心功能：

通过高效的索引结构和相似性计算算法，支持大规模向量数据的快速查询与分析，向量数据库维度越高，查询精准度也越高，查询效果也越好

向量数据库中使用的是相似性搜索，不是精确匹配

使用时，会有一个VectorStore将数据与AI模型集成，数据先加载到矢量数据库中，然后当将用户查询发送给AI时，会先检索相似文档，随后将其作为上下文

并与用户查询一起发送给AI模型，此时即为检索增强生成

![](assets/ssa80.png)

可以使用redis作为向量数据库，此时是RedisStack（redis8）

![](assets/ssa81.png)

文本向量化模型：

![](assets/ssa82.png)

redis stack的配置：

![](assets/ssa83.png)

![](assets/ssa84.png)

Redis Stack优势

![](assets/ssa.png)

使用docker安装

docker run -d --name redis-stack-server -p 6379:6379 redis/redis-stack-server

![](assets/ssa85.png)

![](assets/ssa86.png)

![](assets/ssa87.png)

接着从向量数据库中查找

![](assets/ssa88.png)

知识图谱的构建：

![](assets/ssa89.png)

## RAG

RAG：检索增强生成

可使用知识图谱，向量化数据库存储，为大模型添加知识

LLM（文件大模型）存在的缺陷：

1.知识不是实时的，不具备知识更新

2.不理解私有的领域/业务知识

3.回答可能合理但实际是错误信息，AI幻觉

知识幻觉：

1.已读乱回 2.已读不回 3.似是而非

RAG流程实现：索引和检索，先建索引再去检索

索引：

![](assets/ssa90.png)

检索：

![](assets/ssa91.png)

初始化向量数据库：

![](assets/ssa93.png)

![](assets/ssa92.png)

预热知识库时，每次重启都会新增，内容都一致并且会造成内存暴涨，对知识库造成压力，因此需要进行去重数据，但是这里会存在一个问题，如果提交的rag文档内容改变，再次去提交时会无法更新知识库

![](assets/ssa94.png)

对于rag文档内容改变暂且不知道如何解决

在Java中的topK的参数为返回相似的数量

以及一个阈值，相似度达多少即可返回

## ToolCalling工具调用

LLM外部的utils工具类

![76430023715](assets/1764300237154.png)

![76430042177](assets/1764300421779.png)

![76430046399](assets/1764300463994.png)

使用Tool注解去调用tool

![76430052087](assets/1764300520876.png)

使用toolcalling需要大模型支持functioncall

## MCP

LLM大模型之间的互相调用协议：MCP

让每个LLM之间拥有一个共同的服务，减少原有的ToolCalling的代码冗余

构建一个统一的接口

![76433360364](assets/1764333603644.png)

https://mcp.so/zh

MCP遵循的是C/S架构

![76433383362](assets/1764333833627.png)

MCP通信协议：

![76433388166](assets/1764333881664.png)

![76433388714](assets/1764333887144.png)

MCP与ToolCalling的对比

![76433426305](assets/1764334263056.png)

需要一个服务端与客户端

**服务端：**

![76433450202](assets/1764334502024.png)

![76433454340](assets/1764334543401.png)

![76433464170](assets/1764334641709.png)

启动的服务不再是Tomcat而是Netty

**客户端：**

![](assets/1764334754554.png)

![76433482198](assets/1764334821989.png)

![](assets/1764334902629.png)

![](assets/1770465812925.png)

![](assets/1770465864710.png)

![](assets/1770465882458.png)

>MCP Client是存在于MCP Host即AI应用中的，实际过程中只需要去实现MCP Server即可，实现之后MCP Client去使用
>
>在MCP Client使用中，需要配置一些信息，比如是MCPServer的地址

![](assets/1770467320913.png)

## Advisor

![](assets/1770085740154.png)

>Advisor主要用于增强顾问，将prompt转为请求，先对请求进行增强，随后调用llm，拿到结果之后，再去增强一遍，返回增强之后的结果
>
>它就类似AOP中的拦截器一样，主要通过拦截并动态调整LLM的输入输出，增强LLM的交互能力

![](assets/1770090698010.png)

![](assets/1770086611032.png)

多轮对话的时候，可以使用advisor，然后通过会话的id，回滚查询到之前的对话，拼接到新的问题中，结合两次对话，传给model，进行回答，两者的对话都需要进行存储

>SpringAI里面的BaseAdvisor接口同时继承了CallAdvisor、StreamAdvisor，这样方便了调用实现，而不用需要不同的需求时进行不同的实现，BaseAdvisor里面都默认实现了两种增强
>
>在实现的时候，对BaseAdvisor的before和after重写即可完成，不同的增强使用

```
package com.suke.ai.advisor;

import lombok.extern.slf4j.Slf4j;
import org.springframework.ai.chat.client.ChatClientRequest;
import org.springframework.ai.chat.client.ChatClientResponse;
import org.springframework.ai.chat.client.advisor.api.AdvisorChain;
import org.springframework.ai.chat.client.advisor.api.BaseAdvisor;
import org.springframework.ai.chat.messages.AssistantMessage;
import org.springframework.ai.chat.messages.Message;
import org.springframework.ai.chat.prompt.Prompt;

import java.util.*;

/**
 * @author 自然醒
 * @version 1.0
 */
@Slf4j
public class SimpleMessageAdvisor implements BaseAdvisor {

    //存储的消息列表
    private static Map<String, List<Message>> messageList = new HashMap<>();

    @Override
    public ChatClientRequest before(ChatClientRequest chatClientRequest, AdvisorChain advisorChain) {
        log.info("请求之前的消息 - >{}", chatClientRequest);
        //通过用户id进行查询之前的对话
        String id = "111";
        List<Message> messages = messageList.get(id);
        //如果之前没有对话，则是新对话，直接存储
        if(messages == null){
            messages = new ArrayList<>();
        }
        //存在上次对话，将这次新对话进行拼接存储
        List<Message> instructions = chatClientRequest.prompt().getInstructions();
        messages.addAll(instructions);
        //对话拼接之后，更新这次请求对话内容
        Prompt oldPrompt = chatClientRequest.prompt();
        Prompt newPrompt = oldPrompt.mutate().messages(messages).build();
        ChatClientRequest clientRequest = chatClientRequest.mutate().prompt(newPrompt).build();
        //拿到新请求之后，需要将本次的对话再次进行存储
        messageList.put(id, messages);
        return clientRequest;
    }

    @Override
    public ChatClientResponse after(ChatClientResponse chatClientResponse, AdvisorChain advisorChain) {
        String id = "111";
        List<Message> historyMsg = messageList.get(id);

        if(historyMsg == null){
            historyMsg = new ArrayList<>();
        }

        if(Objects.isNull(chatClientResponse)){
            return chatClientResponse;
        }
        AssistantMessage output = chatClientResponse.chatResponse().getResult().getOutput();
        historyMsg.add(output);
        messageList.put(id, historyMsg);
        log.info("响应之后的消息 - >{}", chatClientResponse);
        return chatClientResponse;
    }

    @Override
    public int getOrder() {
        return 0;
    }
}
```

>我这里写死的id，其实可以通过threadlocal拿到上下文的id即可
>
>也可以使用advisorcontext，如下两个图，我认为可以使用threadlocal
>
>响应和请求都是一样的操作

![](assets/1770088755673.png)

![](assets/1770088763035.png)

官方也是有一个已经实现的advisor，对于使用的话，看个人需求进行选择

```
public ChatClientController(ChatModel chatModel) {
    MessageWindowChatMemory memory = MessageWindowChatMemory.builder().maxMessages(100).chatMemoryRepository(new InMemoryChatMemoryRepository()).build();
    MessageChatMemoryAdvisor memoryAdvisor = MessageChatMemoryAdvisor.builder( memory).build();
    this.chatClient = ChatClient.builder(chatModel).defaultAdvisors(memoryAdvisor).build();
}
```

还有一个参数可以设置，即存储对话的仓库，比如数据库或者内存数据库都可以

>```
>chatMemoryRepository(new InMemoryChatMemoryRepository())
>该参数默认是设置的话，是存储在内存之中
>MessageWindowChatMemory 上下文窗口
>```

>maxMessages 针对上下文窗口的实现，其实可以通过官方实现的进行AI记忆，然后用户的可以自身进行重写实现，做数据持久化





