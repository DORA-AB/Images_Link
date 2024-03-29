1. We agree that the Transformer network architecture has been very successful in accomplishing general tasks in many domains, such as natural language processing and computer vision. However, translating binary code across different ISAs is a highly sophisticated task with a very strong requirement for translation correctness. Any subtle ignorance or inappropriate handling of the syntactical and semantical specifications of low-level assembly languages can cause unexpected translation errors. This has been echoed by our experimental results about Transformer-based large language models like the GPT series and Gemini, which show various syntax and semantic errors (see below for more rigorous experiments we conducted during the rebuttal period). To achieve better translation results, the design of ISANet encompasses a couple of novel techniques, including the syntax-aligned instruction representation, the Transfomer-inspired attention mechanisms, and the mask layer. With that said, we believe that it is necessary to develop a dedicated network to address special issues and challenges in cross-ISA binary code translation.

2. During the rebuttal period, we conduct an ablation study to investigate the effectiveness of the two attention mechanisms. Specifically, we use a subset of the training data to retrain the model without one of the two attention mechanisms or both. The reason we use the subset is to reduce the training time, as otherwise, we cannot finish the experiment in the short rebuttal period. The result is presented in [this figure](https://github.com/DORA-AB/Images_Link/assets/165207457/29e59147-ea29-4ad2-ab8d-6eb08c19c8a0). From the figure, we can see that the two attention mechanisms substantially improve translation accuracy. Also, we can observe that the word attention is slightly more effective than the slot attention. The reason is that the word attention provides more semantic relationships between different instruction operands, potentially allowing the model to achieve higher translation accuracy. We will update the paper to include this experimental result.

3. To be clear, ISANet can only *heuristically* mask semantic errors. To give an example, let us consider translating a guest ```add``` instruction. If the translation result is not a host ```add``` or similar instruction, e.g., a host ```mov``` instruction, ISANet considers it a semantic error and corrects the translation by updating the opcode to ```add```. Similarly, if there is no memory operand in guest instructions, while the translated host instructions contain a memory operand, ISANet also considers it a semantic error and replaces the memory operand with a register. It is worth pointing out that, as mentioned at the end of Section 3.4 in the paper, ISANet cannot guarantee the correctness of this process. This is why a rigorous semantic equivalence verification step is required before applying the translation results of ISANet to a real binary code translation system, as shown in Figure 1 in the paper.

4. To clarify, the embedding vectors visualized in Figure 3 and Figure 4 are extracted from the *trained* network of ISANet. In other words, this experiment is conducted after we have completed the entire training process of ISANet, which covers the testing dataset. This is mainly used to demonstrate that ISANet can learn the semantic knowledge of ISAs, i.e., different instructions with similar semantics are clustered together. 

5. It is essentially a sequence-to-sequence match. That means each instruction in the translation result needs to match the corresponding instruction in the ground truth. In other words, instruction order also needs to be considered when calculating the accuracy.

6. Thanks for pointing out this issue. We conduct a quantitative experiment during the rebuttal period. Specifically, we first randomly extract 50 blocks (due to the limited rebuttal period) from each evaluated SPEC benchmark, then ask large language models (LLMs) and ISANet to translate them, and finally check whether the translated code is correct. The results show that LLMs achieve the following translation accuracy on average: 18.33% (GPT-3.5), 30.30% (GPT-4), and 26.45% (Gemini), while the translation accuracy of ISANet reaches 35.12%. This clearly shows the effectiveness of ISANet for the binary code translation task. We will update the paper to include the results of this experiment.

7. Thanks for your suggestion. We will update the paper to include the following case study, which shows that ISANet can generate more efficient code than the rule-based approach.
* Guest ARM instructions:
```
ldr r0, [r1, #8]
add r0, r0, #1
add r2, r2, #1
str r0, [r1, #8]
ldr r0, [r1, #4]
```
* Host x86 instructions translated by the rule-based approach:
```
movl 0x8(%ebx), %eax 
incl %eax
incl %ecx
movl %eax, 0x8(%ebx)
movl 0x4(%ebx), %eax
```
* Host x86 instructions translated by ISANet:
```
incl 0x8(%ebx)
incl %ecx
movl 0x4(%ebx), %eax
```

8. Thanks for your suggestion. Please see our answer to Question 2 above for the ablation study.

9. Thanks for your suggestion. Yes, we will make the data and code of ISANet publicly available to facilitate future research.
