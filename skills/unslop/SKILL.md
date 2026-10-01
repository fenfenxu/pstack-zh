---
name: unslop
description: 从任意写作中剔除 AI 痕迹。必须始终应用。
disable-model-invocation: true
---

# Unslop

编辑文本，去除 AI 模式。

## 流程

1. 扫描下列模式。
2. 重写。保留含义，匹配预期语气。

## 应检测并修复的模式

规则编号是其他 skill 引用的稳定 id。删除的规则留空号。

### 内容

3. **肤浅的 -ing 短语。** 「highlighting...」「ensuring...」「reflecting...」「showcasing...」「fostering...」。删除或展开并给出真实来源。
5. **模糊归因。** 「Experts believe」「Industry reports suggest」「Some critics argue」。点名来源或删除。

### 语言

7. **AI 词汇。** Additionally、crucial、delve、enduring、enhance、fostering、garner、interplay、intricate、landscape（抽象）、pivotal、showcase、tapestry（抽象）、testament、underscore、vibrant。换成平实词。
8. **「是」的花式说法。** 「serves as」「stands as」「boasts」「features」。直接说「是」或「有」。
9. **「Not just X, but Y.」** 直接陈述要点。
10. **规则三。** 硬凑三组。用自然数量。
11. **同义轮换。** 一段里 protagonist、main character、central figure、hero 轮换。选一个，重复它。
12. **假范围。** 「from X to Y」而 X、Y 不在有意义尺度上。直接列主题。

### 风格

13. **滥用 em dash。** 完全避免 em dash。只用句号或逗号（不用括号、en dash、连字符冒充破折号）。需要分隔就结束句子或用逗号。
14. **滥用冒号。** 冒号在列表或示例前可以。不作句中连接词。「If you're coming from traditional automation: instead of...」里冒号无增益。重写让要点独立，不要比较 framing。
15. **滥用粗体。** 不要每个专有名词或缩写都 bold。
16. **行内标题列表。** 特征是 bold 标签加冒号复述同一行：「**Performance:** Performance improved...」。改成散文。bold 引导以句号结尾、命名条目且后跟真正新细节的（「**Schema in TypeScript.** Tables live in one file.」）可以，不是 tell。
17. **标题 Title Case。** 用句首大写（sentence case）。
18. **装饰 emoji。** 从标题与 bullet 删除。
19. **弯引号。** 换成直引号。

### 沟通 artifact

20. **聊天机器人套话。** 「I hope this helps!」「Let me know if...」「Of course!」「Certainly!」「Found the smoking gun!」删除。
22. **谄媚语气。** 「Great question! You're absolutely right!」直接回应。

###  filler

23. ** filler 短语。** 「In order to」→「To」。「Due to the fact that」→「Because」。「It is important to note that」删除。
24. **过度 hedge。** 「could potentially possibly be argued that it might」→「may」。
25. **泛化结论。** 「The future looks bright.」陈述具体计划或事实。

###  jargon

26. **抽象隐喻名词。** Substrate、wedge、vector、locus、vantage、nexus、primitive（作名词）、harness（作隐喻）、surface（如「API surface」）、bedrock、scaffolding（作隐喻）、modality、paradigm、gold-plating、ratchet（作隐喻）、evacuate（指搬代码）、endgame、north star、flywheel。读起来像技术词，通常有更平实的具体词。「Substrate」→「base」。「Wedge in」→「add」。「Vector」→「way」或「method」。「Gold-plating」→「more than the job needs」。「Ratchet」→机制真名或「a limit that only tightens」。「Evacuate」→「move out」。「Endgame」→「the last phase」。选具体词。

### 平实表达

27. **说做什么，不说感觉。** 「the database stays close at hand」「SQL you can read」「types that follow your schema」命名的是感觉。修复应命名机制或数字：「`.toSQL()` returns the exact string sent to the database」「a column rename fails the build」。问这句告诉读者做什么或知道什么，然后写那个。若无法 restate 为具体指令、事实或数字，删掉。再检查：若句原封不动可出现在别的项目文档，它对本项目什么也没说。删掉。
28. **缩短或拆分密句。** 读者须回溯才能 parse 就拆两句或删从句。一句一思。
29. **主动语态。** 优先。抓「is/are/was/were + past participle」并点名 actor：「queries are validated」→「the compiler validates queries」。仅 actor 未知或无关时被动可接受。
30. **删副词或用更强动词。** 「runs quickly」→「is fast」或数字。「significantly improves」→实测 delta。副词撑弱动词说明动词选错了。
31. **优先平实词。** 「utilize」→「use」，「leverage」→「use」，「facilitate」→「help」，「numerous」→「many」，「in the event that」→「if」。花哨同义词很少更清晰。
32. **矫饰 prose。** 有字面说法处用隐喻或 flourish：格言（「wire it or delete it」）、为效果的修辞碎片、拟人代码（「the plan holds it」）、比喻动词（「rides along」「stands on」）、stock framing。 「A dial worth turning」→「a parameter worth varying」。说本意。规则 26 覆盖隐喻名词。
33. **过度压缩。** 丢冠词、无动词碎片、符号语、缩写让读者解码而非阅读。「Parser rejects bad date → exit 2, no write」→「The parser rejects a bad date, exits with code 2, and writes nothing.」写完整句，有冠词动词，展开箭头与缩写。
