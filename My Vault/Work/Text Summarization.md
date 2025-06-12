

https://www.confident-ai.com/blog/a-step-by-step-guide-to-evaluating-an-llm-text-summarization-task

https://neptune.ai/blog/llm-evaluation-text-summarization

https://medium.com/data-science/how-to-evaluate-llm-summarization-18a040c3905d

https://www.confident-ai.com/blog/llm-evaluation-metrics-everything-you-need-for-llm-evaluation

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

Deepeval summarization score
https://deepeval.com/docs/metrics-summarization


GEval:
https://deepeval.com/docs/metrics-llm-evals
https://github.com/thamsuppp/summary-eval-article/blob/main/summary_eval_article_notebook.ipynb

