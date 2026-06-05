<p align="center">
  <img src="assets/taxonomy.png" alt="Hand-drawn analogy between neural networks and block ciphers" width="100%">
</p>

# 密码分析式模型提取论文整理

本仓库整理 **cryptanalytic neural-network model extraction** 方向的论文、源码、分类体系和开放问题。

这个方向不同于普通 model stealing。普通 model stealing 往往只追求在测试集或自然分布上的高 fidelity；而 cryptanalytic extraction 更关心恢复参数、神经元 signature、sign、decision-boundary geometry，甚至函数级等价模型。

[English](README.md)

## 收录范围

密码分析式模型提取可以把神经网络看成一个类似密码算法的对象：

- 模型参数类似 secret key；
- 查询输入类似 chosen plaintext；
- label、probability、logit 类似 oracle output；
- critical point、transition point、dual point、boundary normal 是可利用的几何泄漏；
- 攻击目标不是简单模仿模型，而是恢复模型的隐藏结构。

本仓库主要整理：

- ReLU 和 piecewise-linear 神经网络；
- raw-output、soft-label、hard-label oracle；
- MLP、CNN、RNN、GNN、PReLU、非线性激活、max-pooling、PPML 等设置；
- 密码分析式攻击、源码、复现状态、局限性和防御方向。

本仓库不打算覆盖所有普通黑盒模型窃取论文。

## 阅读路线

如果刚进入这个方向，建议按下面顺序阅读。

1. **Raw-output ReLU MLP 提取**
   - 先读 Carlini、Jagielski、Mironov 的 CRYPTO 2020 工作。
   - 再读 EUROCRYPT 2024 线的 polynomial-time 改进。

2. **Hard-label 提取**
   - 先看 hard-label extraction 的基本问题定义。
   - 再看基于 transition point、dual point 和 decision-boundary geometry 的方法。
   - 最后读 output-layer recovery、persistent/dead neuron 和 polynomiality critique。

3. **CNN 提取**
   - 先看 average-pooling CNN extraction。
   - 再看 max-pooling extraction。
   - 注意区分 hard-label、soft-label、raw-logit 三种 oracle。

4. **激活函数和架构扩展**
   - PReLU、LeakyReLU、HardTanh、Step 等激活函数。
   - RNN、GNN、PPML 和 side-channel assisted extraction。

5. **开放问题和防御**
   - hard-label CNN + max-pooling；
   - 未知架构或弱架构知识；
   - top-1 label 下的 event observability；
   - 针对 full-domain geometric extraction 的防御。

## 核心类比和分类体系

这个方向最核心的类比是：神经网络和分组密码都由“带秘密参数的线性变换”和“公开非线性操作”交替组成。在分组密码里，秘密是 round keys；在神经网络里，秘密是 learned weights 和 biases。Cryptanalytic extraction 正是利用这种结构，通过查询 oracle 恢复隐藏的几何或代数信息。

这个方向可以按四条主线分类：

| 维度 | 常见取值 |
|---|---|
| Oracle | hard label、soft label、top-k scores、probabilities、raw logits |
| Signal | critical points、transition points、dual points、boundary normals、side-channel leakage |
| Target | neuron signatures、signs、layer parameters、functional equivalence、geometric substitutes |
| Architecture | MLP、CNN、RNN、GNN、PReLU/non-ReLU networks、PPML/deployed systems |

## 源码状态说明

| 标记 | 含义 |
|---|---|
| Official | 作者公开的官方源码。 |
| Unofficial | 第三方复现或非官方实现。 |
| Mirror | 为归档方便而 fork 或 mirror 的版本。 |
| Gone | 论文给出了代码链接，但当前已经无法访问。 |
| Announced | 论文说会公开代码，但没有可用 URL。 |
| Not found | 未找到公开源码链接。 |

## 论文列表

| 年份 | 论文 | 归档 / 会议 | Oracle | 架构 | 核心思想 | 源码 |
|---|---|---|---|---|---|---|
| 2020 | [Cryptanalytic Extraction of Neural Network Models](https://arxiv.org/abs/2003.04884) | CRYPTO 2020 / arXiv | Raw logits | ReLU MLP | 基于 critical point 的 differential extraction | [Official](https://github.com/google-research/cryptanalytic-model-extraction) |
| 2023 | [Polynomial Time Cryptanalytic Extraction of Neural Network Models](https://eprint.iacr.org/2023/1526) | ePrint 2023/1526 / EUROCRYPT 2024 线 | Raw logits | ReLU MLP | polynomial-time sign recovery 和提取改进 | [Official](https://github.com/Crypto-TII/deti) |
| 2024 | [Hard-Label Cryptanalytic Extraction of Neural Network Models](https://eprint.iacr.org/2024/1403) | ePrint 2024/1403 | Hard label | ReLU MLP | label-only 下的 functionally equivalent extraction | [Official](https://github.com/AI-Lab-Y/NN_cryptanalytic_extraction) |
| 2024 | [Polynomial Time Cryptanalytic Extraction of Deep Neural Networks in the Hard-Label Setting](https://eprint.iacr.org/2024/1580) | ePrint 2024/1580 / EUROCRYPT 2025 线 | Hard label | Deep ReLU MLP | transition point、dual point、signature 和 sign recovery | [Official](https://github.com/Jchavezsaab/hard-label-dnn-extraction) |
| 2024 | [A Hard-Label Cryptanalytic Extraction of Non-Fully Connected Deep Neural Networks using Side-Channel Attacks](https://eprint.iacr.org/2024/1870) | ePrint 2024/1870 | Hard label + side channel | Non-FC DNN / CNN-like models | side-channel assisted extraction | [Official](https://github.com/bcoqueret/Side_channel_cryptanalytic_extraction_of_DNN) |
| 2025 | [Extracting Some Layers of Deep Neural Networks in the Hard-Label Setting](https://eprint.iacr.org/2025/1118) | ePrint 2025/1118 | Hard label | ReLU MLP | 结构条件下的 output-layer / partial-layer extraction | [Official](https://github.com/deividafonso281/hard-label-contract-output), [Related](https://github.com/Jchavezsaab/hard-label-dnn-extraction) |
| 2025 | [Is the Hard-Label Cryptanalytic Model Extraction Really Polynomial?](https://eprint.iacr.org/2025/1868) | ePrint 2025/1868 | Hard label | ReLU MLP | persistent/dead neuron 和 polynomiality critique | Not found |
| 2025 | [Delving into Cryptanalytic Extraction of PReLU Neural Networks](https://eprint.iacr.org/2025/1970) | ePrint 2025/1970 | Raw / hard-label line | PReLU networks | PReLU-specific extraction 和局限性 | [Official](https://github.com/AI-Lab-Y/Extracting_PReLU_NN) |
| 2026 | [Geometric Critical Point Screening: Clustering-Free Cryptanalytic Extraction of Neural Network Models](https://eprint.iacr.org/2026/1025) | ePrint 2026/1025 | Raw logits | ReLU networks | 筛选 useful critical points，减少聚类成本 | [Official](https://github.com/1983321048/Geometric-CriticalPoint-Screening) |
| 2026 | [Breaking Slope and Structure Restrictions: Broadening Hard-Label Cryptanalytic Extraction of PReLU Neural Networks](https://eprint.iacr.org/2026/1066) | ePrint 2026/1066 | Hard label | PReLU networks | 放宽 PReLU hard-label extraction 的 slope 和结构限制 | Not found |
| 2026 | [Cryptanalytic Extraction of Convolutional Neural Networks](https://eprint.iacr.org/2026/139) | ePrint 2026/139 | Hard label | CNN with average pooling | 利用卷积结构和 kernel recovery 做 CNN extraction | Gone: anonymous 4open link returns 410 |
| 2026 | [Cryptanalytic Extraction of Recurrent Neural Network Models](https://eprint.iacr.org/2026/168) | ePrint 2026/168 | Raw / hard-label line | RNN | 将 cryptanalytic extraction 扩展到 recurrent models | Not found |
| 2026 | [Cryptanalytic Extraction of Neural Networks with Various Activation Functions](https://eprint.iacr.org/2026/178) | ePrint 2026/178 | Raw / hard-label line | Various activations | ReLU 之外多种激活函数的提取 | [Official](https://github.com/qixiaokang1-stack/cryptanalytic-model-various-functions) |
| 2026 | [Algebraic Attack on Convolutional Neural Network with Max Pooling](https://eprint.iacr.org/2026/241) | ePrint 2026/241 | Raw / soft-output line | CNN with max pooling | PSP/RPCP 风格 max-pooling extraction | Announced, no URL |
| 2026 | [Cryptanalytic Extraction of Deep Neural Networks with Non-Linear Activations](https://eprint.iacr.org/2026/253) | ePrint 2026/253 | Raw-output line | Non-linear activations | pseudo-normal 和非线性激活处理 | [Official](https://github.com/mstealercryptocrypto-ops/mod_stealer26) |
| 2026 | [Navigating the Deep End: End-to-End Extraction on Deep Neural Networks](https://eprint.iacr.org/2026/296) | ePrint 2026/296 | Raw logits | Deep ReLU MLP | 深层网络 end-to-end extraction | [Official](https://github.com/PsyduckLiu/End-to-End-Deep-Neural-Network-Extraction) |
| 2026 | [Model Extraction of Convolutional Neural Networks with Max-Pooling](https://eprint.iacr.org/2026/464) | ePrint 2026/464 | Raw / soft-output line | CNN with max pooling | max-pooling CNN extraction 和 receptive-field structure | Not found |
| 2026 | [Polynomial-Time Cryptanalytic Extraction of Graph Neural Networks in the Hard-Label Setting](https://eprint.iacr.org/2026/719) | ePrint 2026/719 | Hard label | GNN | message-passing 和 graph-structure extraction | [Official](https://github.com/springli07/GNN_MP_CEA) |
| 2026 | [PPML Is More Vulnerable to Cryptanalytic Extraction Attacks](https://eprint.iacr.org/2026/848) | ePrint 2026/848 | PPML setting | Protected inference systems | privacy-preserving ML 部署中的提取风险 | Not found |
| 2026 | [End-to-End Polynomial-Time Cryptanalytic Extraction of Convolutional Neural Networks in the Hard-Label Setting](https://eprint.iacr.org/2026/902) | ePrint 2026/902 | Hard label | CNN with average pooling | hard-label CNN end-to-end extraction | Announced, no URL found |

## 按 Oracle 分类

### Raw logits

Raw-logit 攻击最接近 CRYPTO 2020 的原始设置。攻击者可以直接观察 logits 的导数变化，因此更容易定位 critical points 和恢复 neuron signatures。

代表工作：

- CRYPTO 2020 cryptanalytic extraction；
- polynomial-time raw-output extraction；
- deep end-to-end extraction；
- raw/soft-output 下的 CNN 和 max-pooling extraction。

### Hard label

Hard-label 攻击只能看到 top-1 class。攻击者必须从 decision boundary、transition point、dual point 中间接恢复内部几何。

代表工作：

- hard-label cryptanalytic extraction；
- polynomial-time hard-label deep extraction；
- partial/output-layer hard-label extraction；
- hard-label CNN 和 GNN extraction。

### Soft labels / probabilities

Soft-label 介于 hard-label 和 raw-logit 之间。它泄漏的信息比 top-1 label 多，但通常少于 exact pre-softmax logits。

关键问题：

- softmax、top-k 截断或概率量化后，哪些 critical / pooling events 仍然可观测？

## 按架构分类

| 架构 | 研究状态 | 代表论文 |
|---|---|---|
| ReLU MLP | 最成熟 | CRYPTO 2020、EUROCRYPT 2024、hard-label deep extraction |
| Deep MLP | 活跃 | end-to-end extraction、persistent/dead neuron analysis |
| Average-pooling CNN | 新兴 | CNN extraction、hard-label CNN end-to-end extraction |
| Max-pooling CNN | 仍然困难 | algebraic max-pooling attack、max-pooling extraction |
| PReLU / non-ReLU activations | 活跃 | PReLU extraction、various activation functions、non-linear activations |
| RNN | 早期 | RNN extraction |
| GNN | 新兴 | hard-label GNN extraction |
| PPML / deployed systems | 早期 | PPML vulnerability、side-channel extraction |

## 开放问题

### 1. Hard-label CNN + max-pooling

Hard-label CNN extraction 在 average pooling 上已经有较强结果。Max-pooling 的强结果更多出现在 raw 或 soft-output 设置。top-1 label 下 PSP/RPCP 是否可观测、winner pattern 如何定位、event purity 如何保证，仍然很难。

可以做的问题：

- max-pooling switch points 能否仅从 top-1 labels 中观测到？
- boundary walking 能否隔离 pooling events？
- hard-label max-pooling 是否存在 event-observability barrier？

### 2. 未知架构或弱架构知识

几乎所有强攻击都假设 architecture known。真实 API 威胁模型更接近 architecture recovery + parameter extraction。

可以做的问题：

- 能否从 boundary geometry 推断 layer type、width、kernel size、stride、pooling type？
- architecture recovery 能否接到 cryptanalytic extraction pipeline 前面？
- 每类攻击最低需要多少 architecture knowledge？

### 3. Event purity

很多攻击依赖干净的 critical / transition / dual points。一旦候选事件中混入大量 spurious events，clustering、sign recovery 和 linear solve 都会变得困难。

可以做的问题：

- extraction pipeline 对 event pollution 有多敏感？
- event purity 能否作为独立指标？
- 防御能否在不损害 clean accuracy 的情况下污染 event geometry？

### 4. 针对 full-domain geometry 的防御

数据流形上的任务行为并不唯一决定整个输入空间上的 piecewise-linear 延拓。但 cryptanalytic extraction 经常依赖这种全域延拓。

可以做的问题：

- 能否保持 clean behavior，同时掩码化 off-manifold geometry？
- dormant chaff neurons 或 normal-jet masking 能否提高提取成本？
- 如何区分 behavioral fidelity 和 geometric extraction utility？

### 5. 更真实的 API 设置

很多论文依赖理想化高精度 oracle。

可以做的问题：

- rate limit、quantized output、randomized preprocessing、batching、abstention 下攻击是否仍然成立？
- 哪些攻击能承受商业 API 的实际限制？
- 在噪声和限制下，攻击者还能否恢复有用的 white-box substitute？

## 源码管理建议

本仓库建议只链接官方源码，不直接把所有源码 vendoring 进来。

原因：

- 不同项目 license 不同；
- 直接复制会让仓库很大；
- 上游仓库可能更新；
- paper list 仓库应该保持轻量、易维护。

推荐策略：

- 有官方源码时贴 official repository；
- 重要仓库可以 fork 作为 archival mirror；
- fork 要标注为 mirror，不要冒充 official code；
- 除非要做 reproducibility benchmark，否则不要使用 git submodule。

## 如何贡献

欢迎补充论文和源码。

请尽量提供：

- 论文标题；
- 年份和会议 / ePrint 编号；
- 论文链接；
- oracle model；
- 目标架构；
- extraction target；
- 核心技术；
- 源码链接以及是否官方；
- 主要假设和局限性。

如果代码链接失效，请标记为 `Gone`，并保留最后已知 URL。

## BibTeX

BibTeX 条目后续放在 `bib/cryptanalytic_extraction.bib`。

## 免责声明

本仓库用于学术研究和防御性分析，目的是理解模型提取风险、攻击假设、复现状态和防御方向。
