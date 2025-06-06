

https://www.confident-ai.com/blog/a-step-by-step-guide-to-evaluating-an-llm-text-summarization-task

https://neptune.ai/blog/llm-evaluation-text-summarization


```python
from deepeval import evaluate
from deepeval.test_case import LLMTestCase
from deepeval.metrics import SummarizationMetric

test_case = LLMTestCase(
  input="the original text...", 
  actual_output="the summary..."
)
summarization_metric = SummarizationMetric()
evaluate([test_case], [summarization_metric])
```

