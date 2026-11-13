# Lab 05 — Conclusion

<!--
Read this before you write.

This file is graded by your instructor, by hand, and it is worth 6 of the 15 points.

- Type it yourself. Do not use a chatbot, an AI assistant, an AI autocomplete or a "rewrite my
  text" tool for any part of it, not even for grammar. English that is plain and a little
  clumsy but yours is better than polished text that is not.
- Use your own numbers. Every number you quote must be one your own notebook printed. Copy
  the number exactly as it appeared. A number your notebook did not print is marked as wrong.
- Answer in your own words, about 80-200 words per question. A short answer that uses your
  numbers beats a long answer that could have been written by anyone.
- Delete these comment lines if you like. Keep the three headings and the last line.
-->

## 1. Which layer changed your results the most?

Give the numbers before -> after for that layer (correct / wrong / no answer, out of 20) and say
whether it changed what the agent *can do* or only what it *admits*. If no layer changed the
number of correct answers, say so and explain what the layers did change instead.

Layer 1 changed my results the most. Before it, the bare agent had 16 correct, 4 wrong and 4 no answer out of 24. After layer 1 it had 18 correct, 6 wrong and 0 no answer. So correct went up by two, but wrong also went up by two, and no answer went to zero. This tells me layer 1 did not make the model smarter. It changed what the program does after a tool error. Before, the whole run stopped and gave nothing. After, the model saw the error and could try again. Two alias questions (q23, q24) became correct this way, but q21 and q22 now gave a wrong answer instead of nothing. Layer 2 changed no numbers at all: 18 correct, 6 wrong, 0 no answer, and the same 48708 tokens as layer 1.

## 2. One failure the full harness did not fix

Pick one question that the full harness (layer 3) still gets wrong or refuses. Give its id
(q01 ... q20). Open its trace in the notebook: at which step did it go wrong, and what did
the model do instead? Name one thing you would add to the harness, and what it would cost
(extra model calls, tokens, time, or a new way to fail).

I pick q08: "which region had the lowest average monthly wage in 2024". Layer 3 still answers Jambyl, and Jambyl is wrong. In the trace the model called compare_regions with indicator avg_monthly_wage and year 2024. The tool returned the list sorted highest first, with Astana on top. The model then wrote "ANSWER: Jambyl". It read the list wrong: the lowest region is the last one, not Jambyl. The harness did not catch it because grounding only checks numbers. A region name is not a number, and "Jambyl" really is inside the tool output, so it looks grounded. One thing I would add: a small check that, for a rank question, compares the answer with the first or last row of the tool list. It would cost almost no tokens, only a few lines of code, and no extra model calls. A new way to fail: if the check is too strict, it may reject a correct answer written with a different spelling.

## 3. Workflow or agent for the monthly ranking?

Dala Insight wants to publish, every month, "which region is highest and which is lowest on one
indicator in one year". Would you ship the fixed workflow or the agent? Use the accuracy, the
number of model calls, the tokens and the time you measured in Part 1. Then say in one sentence
what kind of question would make you choose the other one.

For a monthly ranking I would ship the workflow. On the 15 fixed questions it got 13 correct out of 15, with 15 model calls, 3852 tokens and 15.9 seconds. The bare agent on the same 15 questions got 9 correct, with 26 calls, 19835 tokens and 50.9 seconds. So the workflow was more correct, used fewer calls, fewer tokens and less time. The ranking question has a known shape, so a fixed pipeline is enough. I would choose the agent only if the question can change shape, for example a growth or percentage-point question where the model must decide which tools to call and in what order.

---

I wrote this file myself, without any AI tool.
