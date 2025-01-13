
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
- ![[Attachments/Pasted image 20250113030021.png]]
- **Classifier Input:** A classifier takes an input, denoted as _x_, which is a point in a d-dimensional real-valued feature space. This space is represented as *x∈R^d*.
- **Feature Extractor:** The notation _f(x)_ represents a feature extractor. This is a crucial step, especially when _x_ is a string, as it transforms the string into a numeric representation i.e. vector in d-dim.
- **Mapping**: The feature extractor, _f_, maps a string input to a set of features in the _d_-dimensional space. This mapping is necessary to use the text in machine learning models.
- **Labels**: Each point has a label, _y_, which in binary classification has two possible values: [-1 or +1].
- **Visual Representation:** Points are represented in a 2-dimensional feature space, where each point is an _x_, and the label (+ or -) represents its class.
- **Weight Vector:** A classifier is represented as a weight vector, _w_ (sometimes denoted as theta), which is a vector pointing in a direction in the feature space.
- **Decision Boundary:** The decision boundary is **perpendicular to the direction that _w_ is pointing**. This boundary separates the positive and negative classes.
- **Classification Decision:** To classify a point, the dot product of the weight vector (_w_) and the feature vector (_f(x)_) is computed. If the dot product _wT . f(x)_ >0, the point is classified as positive. If <0, it is classified as negative.
- **Bias Term:** Many linear models include a bias term, _b_. However, we will not use a separate _b_ term. Instead the bias term is incorporated into the feature vector. This is done by adding a 1 to the end of the feature vector, effectively folding the bias into the feature vector. This allows for a simplified representation of the classifier without needing to manage separate bias terms.


**Connections to Previous Concepts**

- This section builds on the previous idea of statistical approaches and machine learning, by showing a particular application in the case of linear binary classifiers.
- These classifiers are a way to map from input text to a discrete label. The text is turned into a feature vector, and the classifier then predicts one of two possible labels.
- This classification problem is one of the applications enabled by text analysis tools, which we have previously discussed.


### **Sentiment Analysis and Feature Extraction:**
- ![[Attachments/seg-2.pdf]]
- **Goal**: To build a pipeline that goes from raw text to feature vectors, which can then be used in a classifier to train a sentiment analysis model. The goal is to classify text as having positive or negative sentiment.
- **Sentiment Analysis**: This involves determining whether a piece of text expresses a positive or negative sentiment.
- **Binary Classification**: Sentiment analysis is framed as a binary classification problem where the labels are either positive or negative sentiment.
- **Positive sentiment** indicates that the author likes something.
- **Negative sentiment** indicates that the author does not like something.
- **Challenges in Sentiment Analysis**: Sentiment analysis is complicated by factors like negation and higher-level structure within the text. For instance, "will never watch again" indicates a negative sentiment, even though "watch again" alone might indicate positive sentiment.
- **Two Main Steps**: The process involves two main steps:
	1. **Feature Extraction**: Mapping text to feature vectors.
	2. **Training a Classifier**: Using labeled data to train a classification model.

**Feature Extraction: Bag of Words**

- **Basic Feature Type**: The most basic type of feature used is the **bag-of-words**.
- **Vocabulary**: A vocabulary is constructed of the _n_ most common words (e.g., 10,000 words in English). This vocabulary is used to create a feature vector.
- **Feature Vector**: Each word in the vocabulary is assigned a position in a large vector. The vector has a dimension equal to the size of the vocabulary (e.g., 10,000).
- **Binary Representation**: For each input text, 1 is placed in the vector position if the corresponding word is present in the text, and a 0 if the word is absent. This results in a sparse vector with mostly zeros and some ones. For example, the sentence “the movie was great” will have 1s in the vector positions corresponding to "the", "movie", "was", and "great".
- **Count Representation:** Instead of using a binary presence/absence representation, the feature vector can also use word counts, indicating how many times a word appears in the input text.

**Extensions of Bag of Words**

- **Bag of n-grams**: Instead of single words, sequences of _n_ consecutive words (n-grams) are used as features. **Example**: The bi-grams (2-grams) in the sentence "the movie was great" are "the movie", "movie was", and "was great".
- Using n-grams allows the model to capture more context and meaning. For instance, the bi-gram "not great" can capture the negative sentiment in "the movie was not great,".

**Term Frequency-Inverse Document Frequency ([[TF-IDF]])** 

- This method adjusts word counts based on how common they are across a document collection.
- **Term Frequency (TF)**: The count of a term in a given document.
- **Inverse Document Frequency (IDF)**: A measure of how rare a term is across a collection of documents, calculated as the log of the total number of documents divided by the number of documents containing the word. This helps to give more weight to words that are more unique to particular documents.
- **TF-IDF Score**: The TF-IDF score of a word is the product of its term frequency and inverse document frequency. Common words like "the" receive low scores while rarer, more specific words receive higher scores.

**Pre-processing**

- **Tokenization**: The process of converting a raw string into a sequence of tokens (words or sub-words).
- **Whitespace Tokenization**: A simple approach that splits words based on spaces. This works well for English but not for many other languages.
- **Tokenizers**: Tools that can handle punctuation, contractions, and other complexities. For example, tokenizers can split "wasn't" into "was" and "n't" and separate punctuation from words.
- **Stop Word Removal**: Removing common function words (e.g., "the," "is") that often don't contribute much to sentiment analysis. This is not always necessary but can be beneficial for some tasks, particularly sentiment analysis.
- Stop words are removed because they are very common, skew bag-of-words vectors and don't contribute much to sentiment analysis.
- **Casing**: Converting text to lowercase (or sometimes to true case). Lowercasing can be beneficial for sentiment analysis.
- **Handling Unknown Words**: Replacing rare or unknown words (words not in the vocabulary) with a special "UNK" token. For some tasks, the words can also just be dropped.
- **Indexing**: Assigning a unique numerical index to each word or n-gram to map them to positions in the feature vector. A mapping is maintained that keeps track of the position of each word in the feature space.

**Summary of the Pipeline**

- The pipeline goes from raw strings to count-based or presence-based feature vectors using bag-of-words or TF-IDF representations [5].
- The raw text undergoes several pre-processing steps, including tokenization, stop word removal, casing, unknown word replacement, and indexing [5].
- These steps are critical for turning raw text into a format that can be used in machine learning models for sentiment analysis [5].

These notes provide a detailed overview of the feature extraction process for sentiment analysis and build on earlier concepts about machine learning in NLP. This lecture focuses on the initial steps of transforming text into a usable feature vector for model training. This will help you understand the process involved in transforming text into a numerical representation for machine learning.


### **Machine Learning Framework:**

- ![[Attachments/Pasted image 20250113034647.png]]
- The goal is to **optimize a set of parameters**, denoted as _w_.
- These parameters could be weights in a linear classifier or parameters in a neural network.
- The approach is based on **supervised learning**, where the model has access to labeled data.
- Labeled data consists of pairs of inputs _x_ (e.g., a sentence) and corresponding labels _y_.
- The data is in the form of (_x_i, _y_i) where _i_ represents the _i_th training example.
- The task is framed as an **optimization problem**, where the goal is to find an optimal _w_ that allows the model to perform well on the classification task.

**Optimization Objective**

- The optimization involves a **training objective**, which is a linear sum over the training examples.
- This is an important property for stochastic gradient descent. The objective is a sum of **loss functions** calculated for each training example. The loss function measures how well the current weight vector _w_ fits a particular training example. 
- The overall objective is to find a _w_ that minimizes the sum of losses across all the training data. The overall objective can be represented as the sum from i=1 to d, of the loss calculated using the ith data point and the weights.

**Stochastic Gradient Descent (SGD)**

- **SGD** is an algorithm used to find the optimal parameters _w_.
- The algorithm involves iterating over the training data for a certain number of epochs.
- For each epoch, a data point is sampled from the training data.
- **Weight Update:** For each sampled data point, the weight vector _w_ is updated. The update rule is:
	- _w_ := _w_ - α ∇ _L_(_w_)
	- **∇ _L_(_w_)** represents the **gradient of the loss function** with respect to the weights _w_. This gradient indicates the direction that would increase the loss.
- **α** is the **step size**, a parameter that controls how far to move the weight vector in each update.
- The weights are adjusted in the opposite direction of the gradient to reduce the loss.
- The **step size (α)** is a crucial parameter, particularly in deep neural networks.
- This process is repeated for a number of iterations.

**Key Takeaways**

- **General Framework:** The framework is applicable to various algorithms used in the course, including perceptron and logistic regression [1].
- Even training complex deep neural networks will fundamentally use the same optimization approach [1].
- **Iterative Approach**: Stochastic gradient descent is an iterative approach that adjusts the weights step by step until the loss is minimized.


### **Perceptron Algorithm:**
- ![[Attachments/Pasted image 20250113040556.png]]
- The perceptron is an algorithm for training a **linear binary classifier**.
- It is used for binary classification tasks, where the goal is to classify data points into one of two categories (e.g., positive or negative sentiment).

**Decision Rule**

- The decision rule for a binary classifier is based on the dot product of the weights (w) and the feature vector (f(x)).
- If **w ⋅ f(x) > 0**, the decision is **+1** (positive).
- If **w ⋅ f(x) ≤ 0**, the decision is **-1** (negative).
- The algorithm learns the set of weights _w_ from a training set.
- The weights and features exist in the same vector space; for example, a 10,000-dimensional feature space will have a 10,000-dimensional weight vector.

**Algorithm Steps**

- The perceptron algorithm is run for a certain number of **epochs**.
- For each epoch, the algorithm iterates through the training data.
- **Prediction:** For each example, a prediction (y_pred) is made using the current weights.
- The prediction is either +1 or -1, based on the decision rule.
- **Weight Update:** The weight vector _w_ is updated based on the prediction.
- **If the prediction is correct (y_pred = y_i)**, the weight vector remains unchanged.
- **If the prediction is incorrect**:
	- **If the true label (y_i) is +1**, the weight vector is updated as: **w := w + α * f(x_i)**.
	- **If the true label (y_i) is -1**, the weight vector is updated as: **w := w - α * f(x_i)**.
- **α** (alpha) is a constant representing the learning rate, which controls the magnitude of the update.
- The update rule essentially encourages the dot product of the weights and the features to be more positive when the true label is positive and more negative when the true label is negative.

**Example Walkthrough**

- Simplified sentiment analysis example with the following sentences: "movie good," "movie bad," and "not good". The corresponding labels (y) are +1, -1, and -1 respectively.
- The feature vectors (f(x)) are based on the count of the words: 'movie', 'good', 'bad', and 'not'. For example, "movie good" has a feature vector of [1, 1] [2].
- The weight vector (w) is initialized to all 0s.
- The algorithm proceeds through the examples, updating the weight vector each time a prediction is incorrect.
- For instance, when the algorithm encounters "movie good" in the first epoch, it makes a wrong prediction (-1) because the initial weight vector is all zeros. Therefore, the algorithm updates the weight vector to [1, 1].
- The process is repeated for multiple epochs.
- After the first epoch, the weight vector reflects that "bad" and "not" have negative weights.
- In the second epoch, the algorithm may still make some incorrect predictions, and update the weight vector. After a few iterations the algorithm will converge to the point where it correctly classifies all the examples.

**Convergence and Separability**

- In a machine learning course, it can be proven that the perceptron algorithm will converge if the data are **linearly separable**.
- **Linear Separability:** The data points from different classes can be separated by a straight line (or hyperplane in higher dimensions).
- If the data is not linearly separable, the perceptron algorithm may loop infinitely.
- The lecture illustrates this with another example: "good," "bad," "not good," "not bad".
- In this case, the data points are not separable in the 3-dimensional space, and the perceptron fails to converge.
- The solution is to change the underlying feature set (e.g. add bi-grams) to create a feature space where the data becomes separable.
- By adding bi-grams such as “not good” and “not bad” to the feature space the data become separable.


### **Perceptron as Minimizing a Loss Function**
- ![[Attachments/Pasted image 20250113132238.png]]
- We can reframe the perceptron algorithm not as an iterative, error-driven update procedure but as an instance of **stochastic gradient descent (SGD) minimizing a specific loss function**.
- The loss function has a slightly complex structure, which can be unpacked by focusing on two cases, depending on the term _w_T_ . f(xi)_  . _yi_.
- The loss function is 0 when _w_T_ . f(xi)_ is >=0 and _yi_ =1, or when _w_T_f(xi)_<0 and _yi_ =-1.
- To simplify the explanation, the focus is on the case where _yi_=1.
- The x-axis represents _w_T_f(xi)_, and the y-axis represents the loss.
- The loss function has two parts:
	- 0 when _w_T_f(xi)_>=0. This is represented by a flat line on the right side of the graph.
- It is the negative of _w_T_f(xi)_ when it is less than 0. This is represented by a slanted line on the left side of the graph.
- This loss function, when used with stochastic gradient descent, results in the perceptron algorithm we previously discussed.

**Sanity Check**

- When the loss == 0, no update is made. This aligns with the perceptron algorithm's behavior when an example is classified correctly.
- The gradient of the loss with respect to _w_ is zero when _w_T_f(xi)_ > 0.
- The gradient of the loss with respect to _w_ is -_f(xi)_ otherwise.
- The gradient can be thought of as a linear function of _w_ times _f(xi)_.
- When the gradient is computed, taking the derivative with respect to a specific weight _w_ gives the corresponding feature value.
- The gradient with respect to the vector _w_ is -_f(x)_ if _w_T_f(x)_ < 0.
- When the gradient is multiplied by -α (where alpha is the step size), if α=1, the weight vector is updated by adding _f(x)_ to _w_, which is consistent with the perceptron update rule when the true label is +1. This demonstrates an alignment between the perceptron's update rule and stochastic gradient descent on this loss function.

**Visualization**
- ![[Attachments/Pasted image 20250113132317.png]]
- A small example with two coordinates and four data points (two positive and two negative) is used to visualize the loss function with respect to the weight vector.
- The loss is visualized using a heat map, where different colors represent varying amounts of loss.
- There is a wedge-shaped region where the loss is zero. A weight vector at the edge of this wedge results in a decision boundary that correctly classifies the data.
- Different weight vectors within this region also provide correct classifications.
- The gradient descent algorithm will move down the loss surface until the flat, zero-loss region is reached, which represents convergence of the algorithm.

**Impact of Data Points on Loss**

- An example can be shown where +ve datapoint is removed, resulting in a different loss surface. The wedge-shaped region, however, remains the same, as it is determined by the remaining two points that define the decision boundary.
- This visual intuition connects the loss formulation of the perceptron to the error-driven procedure previously discussed.

### **Logistic Regression as a Discriminative Probabilistic Model:**
- ![[Attachments/seg-6.pdf]]

**What is Logistic Regression?**

- Logistic regression is a way to **classify things** by figuring out the **probability** of each class. Instead of just assigning a label, it tells you how likely something belongs to a certain category.
- It's a **discriminative model**, meaning it directly calculates the probability of a label (_y_) given some input data (_x_). This is different from a **generative model** that tries to model how the data was generated.
- It is a key concept and the **foundation of many techniques**, including feed-forward neural networks.

**How Does it Work?**

- Imagine you have a bunch of data points, and you want to classify each one into one of two classes (positive or negative). Logistic regression figures out the probability of a data point belonging to the positive class [1].
- It uses a formula that looks like this to calculate the probability of a data point being in the positive class: _p(y = +1 | x) = ew⋅f(x) / (1 + ew⋅f(x))_ [1].
- _w_ is the **weight vector**, which determines the importance of each feature [1].
- _f(x)_ is the **feature vector**, which is a set of numbers representing the data point [1].
- The dot product of _w_ and _f(x)_ is similar to what's done in the perceptron algorithm, but in logistic regression, this dot product is used in the logistic function [1].
- The **logistic function** (_ex / (1 + ex)_) is a special function that takes any number and turns it into a probability between 0 and 1 [1]. This is useful because probabilities must be between 0 and 1 [1].
- The probability of a data point belonging to the negative class is calculated as _p(y = -1 | x) = 1 - p(y = +1 | x)_, since there are only two classes, and their probabilities must sum to one [2].

**Making Decisions**

- To classify a data point, logistic regression checks if the probability of it being in the positive class is greater than 0.5 [2].
- If the probability is greater than 0.5, it's classified as positive, otherwise, it is classified as negative [2].
- The decision boundary for logistic regression turns out to be the same as for the perceptron: _w⋅f(x) > 0_ [2]. The key difference, though, is the probabilistic interpretation in logistic regression [2].

**How Does Logistic Regression Learn?**

- The goal is to **maximize the likelihood** of observing the correct labels in the training data, given the data itself [2]. This means we want to find the weight vector _w_ that makes the observed labels as likely as possible [2].
- The **likelihood** is the product of the probabilities of observing each label, given its corresponding data point [2].
- Because products are hard to deal with, we can maximize the **log-likelihood** instead, which is the sum of the log probabilities. This is because log is a monotonic function [3].
- To turn this into a minimization problem (which is easier for computers to solve), we can minimize the **negative log-likelihood (NLL)** [3]. Think of NLL as a **loss** function [3].
- The **stochastic gradient descent (SGD)** algorithm is used to minimize the loss function [3].
- SGD involves computing the **gradient** of the loss function with respect to the weights [3]. The gradient shows the direction in which to adjust the weights to reduce the loss [3].

**Updating the Weights**

- The update rule is derived using a derivative for a positively labeled example (_yi_ = +1) [4].
- After some math, the update rule for weights becomes: _w := w + α * f(x) * (1 - p(y = +1 | x))_ [5].
- _α_ is a small number called the learning rate [5].
- This looks similar to the perceptron update: _w := w + α * f(x)_, where if we misclassify a positive example as negative, we update the weight vector by adding the feature vector [5].
- However, in logistic regression, the update is scaled by _(1 - p(y = +1 | x))_ [5].
- If the model is very confident about the prediction ( _p(y = +1 | x)_ is close to 1), there will be a small update [5].
- If the model is not confident ( _p(y = +1 | x)_ is close to 0) or misclassifies the example, the update is larger and resembles the perceptron update [5].

**Loss Curves**

- If we plot the loss as a function of _z = w⋅f(x)_, the loss curve for logistic regression looks like a smoothed version of the perceptron loss [6].
- This means that the two algorithms will generally perform similarly, even though their logic is different [6].

**Key Takeaways**

- Logistic regression is a method to classify by predicting probabilities, using a logistic function [1].
- It learns by minimizing the negative log-likelihood loss [3].
- The weight update rule is similar to the perceptron's, but it's modulated by a probability term [5].
- Logistic regression and perceptron have similar loss curves and often perform similarly in practice [6].
- The choice between perceptron and logistic regression might not be significant in many classification problems [6].

These notes should give you a good understanding of logistic regression. Remember to go back to the equations if you want to solidify your understanding.