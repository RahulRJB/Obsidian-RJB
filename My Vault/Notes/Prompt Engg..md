
# Prompt Engg.


DATE:  09-07-24


Tags: [[Tags/LLMs|LLMs]]

# References: 
[Prompt Engineering Tutorial – Master ChatGPT and LLM Responses (youtube.com)](https://www.youtube.com/watch?v=_ZvnD73m40o&t=47s)
[Prompt Engineering 101 - Crash Course & Tips (youtube.com)](https://www.youtube.com/watch?v=aOm75o2Z5-o&t=11s)
https://www.deeplearning.ai/short-courses/chatgpt-prompt-engineering-for-developers/



# Content:


- Role play
- type of response needed, reply with compassion, strictness etc
- What reply needed, neat, limiting 100 words etc.
- ![[Attachments/Pasted image 20240709004542.png]]
- 2. ![[Attachments/Pasted image 20240709005452.png]]
- 3. Specify the output, as summary, list, json, limiting the output to 100 words.

- Elements:
  ![[Attachments/Pasted image 20240709230128.png]]
- ![[Attachments/Pasted image 20240710001954.png]]
- ![[Attachments/Pasted image 20240710002546.png]]![[Attachments/Pasted image 20240710002609.png]]
- ![[Attachments/Pasted image 20240710002706.png]]
- ![[Attachments/Pasted image 20240710003130.png]]

## Deeplearning.ai course:

- Write clear and specific instructions:
	- Use delimiters:![[Attachments/Pasted image 20240726231108.png]] Delimiters make it very clear to the model, the exact  text it should summarise. It can be kind of any clear punctuation that separates specific pieces of text from the rest of the prompt. These could be kind of triple backticks, quotes, XML tags, section titles, anything that just kind of makes this clear to the model that this is a separate section. Using delimiters is also a helpful technique to try and avoid prompt injections. And what a prompt injection is, is if a user is allowed to add some input into your prompt, they might give kind of conflicting instructions to the model that might kind of make it follow the user's instructions rather than doing what you wanted it to do.
	- Ask for structured output like HTML or JSON![[Attachments/Pasted image 20240726231815.png]]
	- Ask the model to check whether conditions are satisfied. So, if the task makes assumptions that aren't necessarily satisfied, then we can tell the model to check these assumptions first. And then if they're not satisfied, indicate this and kind of stop short of a full task completion attempt. Necessary for edge cases and guardrails.![[Attachments/Pasted image 20240726232259.png]]
	- Few-shot prompting:![[Attachments/Pasted image 20240726232527.png]]
- Give model time to think:  If a model is making reasoning errors by rushing to an incorrect conclusion, you should try reframing the query to request a chain or series of relevant reasoning before the model provides its final answer. Another way to think about this is that if you give a model a task that's too complex for it to do in a short amount of time or in a small number of words, it may make up a guess which is likely to be incorrect.
	- Specify the steps required to complete the task![[Attachments/Pasted image 20240726233633.png]]![[Attachments/Pasted image 20240726233907.png]]  
	- Instruct the model to work out its own solution before rushing to a conclusion:![[Attachments/Pasted image 20240726234735.png]]Response wrong!
	  ![[Attachments/Pasted image 20240726234834.png]]![[Attachments/Pasted image 20240726235137.png]]Instead asking the model to solve the problem themself.
- Model limitations:
	- Hallucination: To reduce hallucinations, you want the model to kind of generate answers based on a text, ask the model to first find any relevant quotes from the text and then ask it to use those quotes to kind of answer questions. In this way you can trace the answer back to the source document is often pretty helpful to kind of reduce these hallucinations.

- Iterative prompting:
	- Sentence lenght better than word count



