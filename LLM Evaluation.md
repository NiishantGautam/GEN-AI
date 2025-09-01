---
tags:
  - GEN-AI
  - LLM
---
> Why even care for an LLM Evaluation ?

> We shouldn't blindly trust any model, even from OpenAI. Benchmarking lets us independently test how well a model works for our specific use case. We can spot weaknesses, biases, or errors that might not show up in general tests. 
> This helps us to decide if we n*eed to fine-tune, retrain or even switch models*.


Common Examples Includes:

- **Customer support bots:** Benchmark LLMs to ensure accurate, helpful responses to user queries.
- **Medical Q&A apps:** Test LLMs for correctness and safety in health-related answers.
- **Educational tools:** Evaluate how well LLMs explain concepts or grade student answers.
- **Content moderation:** Check if LLMs can reliably detect harmful or inappropriate content.
- **Legal document analysis:** Benchmark LLMs for summarizing or extracting key info from contracts.



### LLM Benchmarking

Benchmarking is the process of evaluating the performance of a system or component by comparing it against a set of predefined standards or datasets.
With this it is crucial in understanding <mark style="background: #FFF3A3A6;">how well a model performs in various tasks, identifying its strengths and weaknesses, and guiding improvements. </mark>

We have various types of LLM benchmarking. 

- **Factual QA** (like TriviaQA, SQuAD)
- **Multiple-choice reasoning** (like MMLU, ARC)
- **Truthfulness & bias detection** (like TruthfulQA)
- **Perplexity-based evaluation** (language fluency prediction)
- **Semantic similarity** (embedding-based matching)
- **Domain-specific tests** (custom internal benchmarks)


Suppose we select Factual QA and want to go with TriviaQA. We can go to the TriviaQA website to download the full dataset. -  [TriviaQA website](http://nlp.cs.washington.edu/triviaqa/) to download the full dataset. The github link: https://github.com/mandarjoshi90/triviaqa

The dataset contains pairs of questions and answers.

We need to setup our environment for that:

```bash
uv init
uv venv
source .venv/bin/activate # Activates the virtual env
which python # To check the virutal env python is being used.
uv add openai
uv add python-dotenv
```
Make sure we add a .gitignore and add that in the .gitignore file
```bash

.venv/

venv/

```
After we have our dataset, we can load and read it.


>Python Refresher:
	A safe and clean way to work with files in Python is to use the with statement, as it will automatically closes the file when we are done. f is a variable that refers to the opened file.

- `csv.DictReader(f)` reads each row as a dictionary, with column names as keys (like `{"question": ..., "answer": ...}`).
- Wrapping it with `list(...)` loads all rows into a list, so you can easily loop through or access them.

This makes it simple to work with question-answer pairs by name, not just by position!
```python
import csv

with open("triviaqa.csv") as f:
    qa_pairs = list(csv.DictReader(f))
```



#### Normalized Match Evaluation

To evaluate the performance of an LLM, we have Normalized match. <mark style="background: #BBFABBA6;">This involves comparing the model's response to the correct answer by normalizing both texts. Normalization helps in removing any discrepancies due to case sensitivity or punctuation.</mark>

Normalization strips out spaces, punctuation, and makes everything lowercase!

Normalizing text helps ensure fair comparison by:

- Before: `"Paris!"` → After: `"paris"`
- Before: `" 1912 "` → After: `"1912"`
- Before: `"New-York City."` → After: `"newyorkcity"`

```python
def normalize(text):
 return re.sub(r'[^a-z0-9]', '', text.lower())
```

```python
import re
from openai import OpenAI

# Initialize the OpenAI client
client = OpenAI()

def normalize(text):
    return re.sub(r'[^a-z0-9]', '', text.lower())

correct = 0
for q in qa_pairs:
    prompt = f"Answer the following question with a short and direct fact:\n\n{q['question']}"
    response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[{"role": "user", "content": prompt}]
    ).choices[0].message.content.strip()

    if normalize(q['answer']) == normalize(response):
        correct += 1
```
Here, `normalize` function removes all non-alphanumeric characters and converts the text to lowercase. This ensures that the comparison between the model's response and the correct answer is fair and consistent. We then iterate over each question-answer pair, generate a response using the `openai` library, and compare the normalized texts.

