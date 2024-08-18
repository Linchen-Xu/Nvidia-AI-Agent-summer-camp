## NVIDIA AI-AGENT夏季训练营

 

项目名称：AI-AGENT夏季训练营 — RAG智能对话机器人

报告日期：2024年8月18日

项目负责人：Linchen Xu

 

#### 项目概述：



项目旨在利用Nvidia NIM平台结合RAG(Retrieval-Augmented Generation)实现ATS(Applicant Tracking System)系统的简历筛选功能。



Nvidia NIM是NVIDIA提供基于NVIDIA推理软件的预构建容器，使开发者能够将部署时间从几周缩短至几分钟，通过NIM平台可以有效降低大模型的使用和维护成本。



RAG(Retrieval-Augmented Generation)又叫检索增强生成，它将检索系统与大语言模型相结合，通过基于向量的语义搜索获取相关文本信息并反馈给大模型，让大模型基于相关的上下文信息生成最终响应。这一过程可以确保生成的内容不仅仅基于预训练模型所获得的知识，也能够通过外部的数据进行补充，使生成的响应更加健壮和准确，尽可能避免大模型出现幻觉。



ATS(Applicant Tracking System)又叫应聘者跟踪系统，是用于筛选和管理招聘过程中的简历的软件工具。它可以帮助招聘人员快速地将大量的简历数据进行筛选、匹配和分类，同时支持邮件、通知、笔试、面试等各个环节的管理。ATS可以自动化招聘流程并减少人员处理简历的时间和工作量，同时提高简历的质量和准确性。它还可以根据公司设定的招聘职位对简历进行自动筛选，并从中找出最符合需求的候选人。此外，ATS 还支持各种招聘数据分析和报表输出，为公司提供数据支持。



以往筛选简历需要人为设置规则，有的公司甚至需要HR人工完成这一步骤，无论哪种都费时费力。通过RAG辅助大模型进行简历分析可以极大程度提高筛选效率和筛选精确度。



#### 技术方案与实施步骤

l 模型选择：



最初计划通过将pdf转换成图片输入，但是收到网络限制，必定会超时。

经过多次试验，模型最终决定使用meta/llama-3.1-8b-instruct。

 

l 功能整合：  



项目整合了gradio页面，支持上传pdf文件和职位描述进行匹配度分析。



l 数据构建： 



数据共分为两部分：pdf格式的简历和字符串格式的职位描述。



在构建阶段，pdf文件通过embedding进行向量化，职位描述在替换换行符后直接作为文本输入。



#### 实施步骤：

l 环境搭建： 

1. 安装Anaconda
2. 安装python，配置python环境
3. 安装需要用到的包：pip install langchain-nvidia-ai-endpoints jupyterlab langchain langchain_core langchain_community matplotlib numpy faiss-cpu openai gradio



l 代码实现： 



1. 文件向量化：使用PyMuPDFLoader进行文件加载，使用NVIDIAEmbeddings进行embedding

![image-20240818223350077](D:\code\python\Nvidia-AI-Agent-summer-camp\embedding.png)

2. prompt部分：

![image-20240818223608604](D:\code\python\Nvidia-AI-Agent-summer-camp\image-20240818223608604.png)

3. 构建完整chain

![image-20240818223645168](D:\code\python\Nvidia-AI-Agent-summer-camp\image-20240818223645168.png)

4. 适配gradle ui：

![image-20240818223852071](D:\code\python\Nvidia-AI-Agent-summer-camp\image-20240818223852071.png)



l 测试与调优：



通过prompt engineering不断优化prompt，最后得到了一个相对满意的结果。



l 集成与部署：

 

模型本身部署在Nvidia NIM平台，只需本地运行代码即可。

 

#### 项目成果与展示：



l 应用场景展示： HR进行简历筛选。



l 功能演示： 



上传pdf，并输入job description，点击submit，等待几秒后得到结果（敏感信息已作打码处理）

![image-20240818224843200](D:\code\python\Nvidia-AI-Agent-summer-camp\image-20240818224843200.png)

 

#### 问题与解决方案：

1. 问题：大模型返回结果不理想。
   1. 问题分析：
      1. prompt不规范，没有得到理想的结果。
      2. 大模型选型问题，需多次调试后得到结果。
      3. 数据质量较低。
   2. 解决措施：
      1. 优化prompt。
      2. 对多个大模型进行了调试，选出了效果较好的一个。
      3. 提高数据质量，对材料无关部分进行排查（大数据量可以通过工具进行数据蒸馏），持续输入高质量材料。
2. gradio一直报错"[400] Bad Request"
   1. 问题分析：
      1. 机器网络问题。多次尝试确认其他网站可以正常访问，排除该问题。
      2. chain的中间步骤处理有误。在每一步之间加入调试代码后发现报错时并未执行chain。
      3. 请求参数问题。返回400是因为请求不符合要求，排除其他可能的问题后，基本能确认是请求参数不符合要求。经排查，发现是job_description过长。

   2. 解决措施：
      1. 精简输入，只保留关键部分。
      2. 有条件的话可以将大模型放到本地运行，绕开网络请求长度限制。


 

#### 项目总结与展望：



l 项目评估： 



1. 有充分的市场需求，技术可行性较高。
2. 目前数据规模较小，扩大规模后的表现仍需观察。

l 未来方向： 

1. 扩大数据规模，提高数据质量。
2. 技术优化：
   1. 优化模型，提高响应准确度和速度。
   2. 通过prompt engineering提供多元化服务，包括面向候选人的简历修改建议和面向招聘人员的简历打分等功能。
   3. 优化用户交互，提高对用户友好程度，降低应用的使用门槛。

 

#### 附件与参考资料

1. Nvidia Deep Learning Institute (Nvidia DLI): https://learn.nvidia.com/
2. langchain: https://python.langchain.com.cn/docs/
3. pdf2image解析: https://pypi.org/project/pdf2image
4. PyMuPDF: https://pymupdf.readthedocs.io/en/latest/index.html

 

 

 

 

 

 

 
