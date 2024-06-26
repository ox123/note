### SRE与DevOps区别于联系

- https://cloud.google.com/blog/products/gcp/sre-vs-devops-competing-standards-or-close-friends
- https://blog.overops.com/devops-vs-sre-whats-the-difference-between-them-and-which-one-are-you/

- The main difference here is that SREs revolves around the concept that operations is a software problem, which led them to define prescriptive ways for measuring availability, uptime, outages, toil, etc.

- SREs also ensure that everyone in the company agrees on how to measure reliability, and what to do when availability falls out of specification. This includes contributors at every level, from developers, through team managers and all the way up to VPs and executives.

---

00>>>>>>

### DevOps

- 如何梳理出一套清晰的 DevOps 理念和完整的知识体系？
- 如何获得一线大厂的实践经验，让 DevOps 真正落地？
- 如何获得一条渐进式的 DevOps 学习曲线，让自己在正确的方向上不断增值？



**敏捷之所以更快，根本原因在于持续迭代和验证节省了大量不必要的浪费和返工**

<img src="assets/image-20200930234328058.png" alt="image-20200930234328058" style="zoom:50%;" />

<img src="assets/image-20200930234145261.png" alt="image-20200930234145261" style="zoom:50%;" />![image-20200930234240227](assets/image-20200930234240227.png)

<img src="assets/image-20200930234410394.png" alt="image-20200930234410394" style="zoom:50%;" />

### **DevOps 是通过平台（Platform）、流程（Process）和人（People）的有机整合，以 C（协作）A（自动化）L（精益）M（度量）S（共享）文化为指引，旨在建立一种可以快速交付价值并且具有持续改进能力的现代化 IT 组织。**

- DevOps要解决的还是软件研发交付能力和业务需求快速多变之间的矛盾
- 无论是精益创业的MVP理论，还是反脆弱中的从不确定中收益的角度来说，企业以可控的成本不断试错，以博取一个无限的收益，已经成为了常态。
- 寻找志同道合的一帮人，达成共识，实现目标，研发，测试，运维的所做所为，这很DevOps
- 以更快更好的构建、测试、发布软件交付价值为目标
- 高效率和高质量是 DevOps 的核心价值，而工具和自动化就是提升效率最直接的手段，让一切都自动化可以说是 DevOps 的行为准则
- **一切软件交付过程中的手动环节，都是未来可以尝试进行优化的方向**。



### DevOps三大支柱

<img src="assets/image-20201001001834939.png" alt="image-20201001001834939" style="zoom:50%;" />

### devops关注点

- 要有全局观
  - 要从软件开发全流程的角度出发制定实施方案，了解每个阶段在全局流程中所处的位置，以及对上下游的影响。比如测试阶段效率的提升可能会导致部署阶段工作任务的积压，对整体交付并不一定产生积极影响
- 要以始为终
  -  在应用 DevOps 过程中，要知道做这件事的目的是什么。应用 DevOps 是为了交付业务价值，是为业务服务的
- 要持续改进
  - 在应用 DevOps 过程中，要抱有持续学习、不断改进的心态，通过不断试验来持续改进。这就需要定义可度量的、有价值的指标来指导改进

---



- **端到端**，是指从需求提出到需求被发布到生产环境交付给用户的整个过程，可以理解为软件开发的全生命周期

- **最佳实践**，是指被业界广泛采用的、被证明有效的方式方法。

![image-20201202225046410](assets/image-20201202225046410.png)

- (**why**) 我们为什么要做这件事？或者我们为什么要开发这个功能？问题的答案正是我们要达到的目标。设立清晰的目标听起来简单，但真正做起来非常不容易。有很多软件项目失败的原因就是目标不清晰。我们要实现的目标一定是业务目标。这个目标要有明确作用：或者能够为企业带来利润、客户，或者能够为其降低成本。
  - 好的目标应该符合 SMART 原则：Specific（明确的），Measurable（可度量的），Action-oriented（面向行动的），Realistic（现实的），Timely（有时限的）。比如：3 个月新增用户 100 万或年底之前完成销售额 1000 万等。