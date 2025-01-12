
# NLP - UT Austin


DATE:  10-01-25


Tags: [[NLP]]


# References:

[Natural Language Processing at UT Austin, 2023-2024 version (Greg Durrett) - YouTube](https://www.youtube.com/playlist?list=PLofp2YXfp7TZZ5c7HEChs0_wfEfewLDs7)
[Course Materials](https://www.cs.utexas.edu/~gdurrett/courses/online-course/materials.html)
# Content:


## Week1: **Intro and Linear Classification**

### **Introduction to Natural Language Processing (NLP):**

- **Goal of NLP**: To solve problems that require a **deep understanding of text**, going beyond shallow methods like string matching. This involves understanding the meaning of text, not just processing it at a surface level.
- **Deep understanding** is contrasted with **shallow understanding**, such as string matching or regular expressions.
- **Applications requiring deep understanding**:
	- **Dialogue systems:** Systems that can converse with users, understand their requests, and respond appropriately. Examples are personal assistants that can have back-and-forth conversations.
	- **Machine translation:** Translating text from one language to another, requiring understanding of syntax and concepts, not just word-for-word translation.
	- **Automatic summarization:** Systems that can create summaries of longer texts.
	- **Information extraction and question answering**: Systems that can extract information and answer questions, even if the answer is not explicitly stated in a structured database. This can involve retrieving information from the web.
- **Why deep understanding is necessary**:
	- Rules or templates are insufficient to encapsulate all possible dialogue flows.
	- Machine translation requires understanding syntactic structure and concepts, which is not possible with word-by-word translation.
	- Question answering often requires understanding the connection between a query and the information in a database or web page.

**Standard NLP Methodology**

- **Starting point:** The course will primarily focus on text analysis, not speech input.
- **Text Analysis Tools:** A set of tools for producing text, involving layers of annotation on top of the text, including:
	- **Syntactic parses:** Understanding the syntactic structure of the text.
	- **Co-reference resolution**: Understanding which pieces of text refer to the same real-world concepts.
	- **Entity disambiguation**: Figuring out what those concepts are.
	- **Discourse structure**: Understanding the organization and relationship of ideas within a text.
- **Applications enabled by Text Analysis Tools:** Summarization, information extraction, question answering, sentiment analysis, and machine translation.
- **Approach**: These problems will be tackled using statistical approaches and machine learning.
- ![[Attachments/Pasted image 20250112201939.png]]

**Representing Language**

- **Labels**: Assigning discrete labels to text, such as sentiment (positive, negative) or subjectivity.
- **Sequences/Tags**: Recognizing text as a structured object, like named entity recognition and part-of-speech tagging.
- **Tree Structures**: Understanding the recursive hierarchical syntactic structure of language using parse trees.
- **Semantic Representations**: Using tree structures like lambda calculus to represent meaning that can be executed against a database.
- **Machine Learning Techniques**: Used to predict and deal with these different structures of language.
	- **How the representations are used:** Syntactic features for classification with feature-based linear models.
	- Building neural networks with tree structure.
	- Modeling translation as a mapping between syntactic parses using a tree transducer.
- ![[Attachments/Pasted image 20250112202356.png]]

**Ambiguity in Language**

- **Ambiguity:** A key challenge in NLP, with multiple possible interpretations of a sentence or phrase.
- **Types of Ambiguity**:
	- **Part-of-speech ambiguity:** When a word can have different roles (noun, verb, adjective). Example: "teacher strikes idle kids".
	- **Syntactic ambiguity**: When the structure of a sentence can be interpreted in multiple ways. Example: "ban on new dancing on governor's desk".
	- **Semantic ambiguity**: When a word has multiple meanings or senses. Example: "Iraqi head seeks arms".
- **Statistical Models**: Are used to pick the correct interpretation from a large number of possibilities.![[Attachments/Pasted image 20250112202633.png]]
- **Multiple Valid Interpretations**: The complexity in NLP isn't just about binary ambiguities, but about the exponentially large number of possible interpretations.
- **Translation example**: A four word sentence can have billions of possible translations.

**Timeline of NLP**

- **Late 1980s (AI Winter)**: Rule-based or expert systems; non-statistical approaches.
- **Early 1990s**: Shift to **statistical machine translation** using aligned data (Canadian Parliament). Development of the **Penn Treebank** which led to the building of part-of-speech taggers and syntactic parsers.
- **Data is useful**: Led to interest in supervised learning, support vector machines, and conditional random fields applied to named entity recognition and sentiment analysis.
- **Unsupervised Learning**: Topic models and grammar induction were also investigated.
- **Labeled and Unlabeled Data**: Interest grew in combining labeled and unlabeled data.
- **2014-2015: Neural Revolution**: Followed by the revolution of pre-trained models like BERT and GPT.
- **Current Focus:** Supervised learning, semi-supervised methods, and neural network models that can handle ambiguity using data.
- ![[Attachments/Pasted image 20250112202717.png]]

### **Linear Binary Classification:**
- 
- **Classifier Input:** A classifier takes an input, denoted as _x_, which is a point in a d-dimensional real-valued feature space. This space is represented as *x∈R^d*.
- **Feature Extractor:** The notation _f(x)_ represents a feature extractor. This is a crucial step, especially when _x_ is a string, as it transforms the string into a numeric representation [1].
- **Mapping**: The feature extractor, _f_, maps a string input to a set of features in the _d_-dimensional space [1]. This mapping is necessary to use the text in machine learning models [1].
- **Labels**: Each point has a label, _y_, which in binary classification has two possible values: -1 (negative) or +1 (positive) [1].
- **Visual Representation:** Points are represented in a 2-dimensional feature space, where each point is an _x_, and the label (+ or -) represents its class [1].
- **Weight Vector:** A classifier is represented as a weight vector, _w_ (sometimes denoted as theta), which is a vector pointing in a direction in the feature space [1].
- **Decision Boundary:** The decision boundary is **perpendicular to the direction that _w_ is pointing** [1]. This boundary separates the positive and negative classes [1].
- **Classification Decision:** To classify a point, the dot product of the weight vector (_w_) and the feature vector (_f(x)_) is computed [1].
- If the dot product _w_T _f(x)_ is greater than zero, the point is classified as positive [1].
- If the dot product is less than zero, it is classified as negative [1].
- Points exactly on the boundary need a tie-breaking mechanism which is not discussed in the excerpt [1].
- **Bias Term:** The lecture notes that many linear models include a bias term, _b_. However, this course will not use a separate _b_ term. Instead the bias term is incorporated into the feature vector [1].
- This is done by adding a 1 to the end of the feature vector, effectively folding the bias into the feature vector [1].
- This allows for a simplified representation of the classifier without needing to manage separate bias terms [1].

**Next Steps in the Course**

- The lecture introduces linear binary classification as a starting point [1].
- The following segments will cover the perceptron and logistic regression, which are two approaches for learning the weights (_w_) [1].
- These approaches are functionally similar [1].

**Connections to Previous Concepts**

- This section builds on the previous idea of statistical approaches and machine learning, by showing a particular application in the case of linear binary classifiers.
- These classifiers are a way to map from input text to a discrete label. The text is turned into a feature vector, and the classifier then predicts one of two possible labels.
- This classification problem is one of the applications enabled by text analysis tools, which we have previously discussed.

These notes capture the core concepts of linear binary classification discussed in the provided lecture transcript. It builds on the previous discussion of NLP applications, methodologies, and representations, and sets the stage for subsequent learning about specific learning algorithms for these models.

convert_to_textConvert to source