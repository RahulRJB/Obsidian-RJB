
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

- Imagine you have a bunch of data points, and you want to classify each one into one of two classes (positive or negative). Logistic regression figures out the probability of a data point belonging to the positive class.
- It uses a formula that looks like this to calculate the probability of a data point being in the positive class: _p(y = +1 | x) = e^w⋅f(x) / (1 + e^w⋅f(x))_.
- _w_ is the **weight vector**, which determines the importance of each feature.
- _f(x)_ is the **feature vector**, which is a set of numbers representing the data point.
- The dot product of _w_ and _f(x)_ is similar to what's done in the perceptron algorithm, but in logistic regression, this dot product is used in the logistic function.
- The **logistic function** (_e^x / (1 + e^x)_) is a special function that takes any number and turns it into a probability between 0 and 1. This is useful because probabilities must be between 0 and 1.
- The probability of a data point belonging to the negative class is calculated as _p(y = -1 | x) = 1 - p(y = +1 | x)_ = 1 / (1 + e^w⋅f(x))_, since there are only two classes, and their probabilities must sum to 1.

**Making Decisions**

- To classify a data point, logistic regression checks if the probability of it being in the positive class is greater than 0.5.
- If the probability is greater than 0.5, it's classified as positive, otherwise, it is classified as negative.
- The decision boundary for logistic regression turns out to be the same as for the perceptron: _w⋅f(x) > 0_ . The key difference, though, is the probabilistic interpretation in logistic regression.

**How Does Logistic Regression Learn?**

- The goal is to **maximize the likelihood** of observing the correct labels in the training data, given the data itself. This means we want to find the weight vector _w_ that makes the observed labels as likely as possible.
- The **likelihood** is the product of the probabilities of observing each label, given its corresponding data point.
- Because products are hard to deal with, we can maximize the **log-likelihood** instead, which is the sum of the log probabilities. This is because log is a monotonic function.
- To turn this into a minimization problem (which is easier for computers to solve), we can minimize the **negative log-likelihood (NLL)**. Think of NLL as a **loss** function.
- The **stochastic gradient descent (SGD)** algorithm is used to minimize the loss function.
- SGD involves computing the **gradient** of the loss function with respect to the weights. The gradient shows the direction in which to adjust the weights to reduce the loss.

**Updating the Weights**

- The update rule is derived using a derivative for a positively labeled example (_yi_ = +1).
- After some math, the update rule for weights becomes: _w := w + α * f(x) * (1 - p(y = +1 | x))_.
- _α_ is a small number called the learning rate.
- This looks similar to the perceptron update: _w := w + α * f(x)_, where if we misclassify a positive example as negative, we update the weight vector by adding the feature vector.
- However, in logistic regression, the update is scaled by _(1 - p(y = +1 | x))_.
- If the model is very confident about the prediction ( _p(y = +1 | x)_ is close to 1), there will be a small update.
- If the model is not confident ( _p(y = +1 | x)_ is close to 0) or misclassifies the example, the update is larger and resembles the perceptron update.

**Loss Curves**

- If we plot the loss as a function of _z = w⋅f(x)_, the loss curve for logistic regression looks like a smoothed version of the perceptron loss.
- This means that the two algorithms will generally perform similarly, even though their logic is different.

**Key Takeaways**

- Logistic regression is a method to classify by predicting probabilities, using a logistic function.
- It learns by minimizing the negative log-likelihood loss.
- The weight update rule is similar to the perceptron's, but it's modulated by a probability term.
- Logistic regression and perceptron have similar loss curves and often perform similarly in practice.
- The choice between perceptron and logistic regression might not be significant in many classification problems.

### **Sentiment Analysis**
- ![[Attachments/seg-7.pdf]]
- Sentiment analysis is a type of **classification task** where the goal is to determine the **sentiment** (positive or negative) expressed in a piece of text.
- It involves understanding whether a given text expresses a positive opinion (e.g., "the movie was great") or a negative one (e.g., "the film was awful").
- Sentiment analysis can be thought of as a **binary classification problem**.

**Feature Extraction**

- Feature extraction is the process of converting text data into **numerical feature vectors**, which can then be used by a machine learning classifier.
- The basic steps to go from text to feature vectors are:
- A labeled dataset of examples are used to train a classifier.
- Text is converted to a feature vector via feature extraction.
- This involves mapping words or sequences of words into a vector space.

**Bag of Words**

- The most basic type of features are called **bag of words (BOW)** features.
- In this approach, we create a vocabulary of the most common words in the language. For example, the 10,000 most common words might be used as the vocabulary.
- Each word in the vocabulary is assigned a position in a large vector.
- For each input example, the corresponding position in the vector is marked with a "1" if the word is present in the example, and a "0" if it's not.
- This leads to a vector that consists of mostly zeros and a few ones.
- The values in this vector can represent the presence or absence of a word, or they can be counts of how many times each word appears in the input.
- The bag of words approach can be effective for sentiment analysis.

**Bag of N-grams**

- **N-grams** are sequences of _n_ consecutive words.
- **Bag of n-grams** is an extension of the bag of words approach where instead of individual words, we consider sequences of words.
- For example, bigrams are sequences of two words (e.g., "movie was", "was great").
- This can capture some context, such as "not great", which would be missed by the simple bag of words approach.
- A feature vector is created in a similar way to bag-of-words, but with n-grams as the units instead of words.

**Term Frequency-Inverse Document Frequency (TF-IDF)**

- TF-IDF is a weighting scheme that combines two concepts:
	- **Term Frequency (TF):** The count of a term in a given document.
	- **Inverse Document Frequency (IDF):** A measure of how rare a term is across a collection of documents. The IDF is calculated as the log of the total number of documents divided by the number of documents containing the term.
- **TF-IDF score** is calculated by multiplying the term frequency by the inverse document frequency.
- Common words like "the" will have a low TF-IDF score as they appear in most documents. And rare words will have a higher TF-IDF score, as they may be more characteristic of the document.
- TF-IDF can help to emphasize important words while downplaying common ones.

**Pre-processing**

- Pre-processing is the step of preparing the raw text before feature extraction.
- This involves a few steps, including:
	- **Tokenization:** The process of splitting raw text into words or tokens. Simple whitespace tokenization works well for English, but other languages may require different approaches.
	- Tokenizers can also handle punctuation, contractions and hyphenated words. For example, a tokenizer can separate "wasn't" into "was" and "n't" or "great!" into "great" and "!".
- **Stop Word Removal:** Removing common function words (e.g., "the", "is") that do not contribute much to the sentiment analysis task.
- **Casing:** Converting text to a specific case (e.g., lowercasing) which can be helpful for sentiment analysis and other applications.
- **Handling Unknown Words:** Replacing rare words that are not in our vocabulary with a special token (e.g., "/<unk/>")
- **Indexing:** Mapping each word or n-gram into the feature space, effectively assigning each item a position in the vector.

**Summary of Pipeline**

- Raw string input is transformed by applying tokenization, stop word removal, casing, unknown word replacement, and indexing.
- The processed string is converted into a feature vector, which could be a bag of words or TF-IDF vector.
- This feature vector is then used to train a sentiment classifier.


### **Optimization Basics**
- ![[Attachments/seg-8.pdf]]
- The core idea of optimization is to find the best set of **weights** (denoted as _w_) that **minimizes a loss function**.
- The **loss function** is a way of quantifying how well our model is performing, and it's defined with respect to the training dataset. The goal is to find the _w_ that minimizes this function.
- The loss function is considered as a **linear sum** over training examples. The training data is treated as fixed, and _w_ is the variable that we are changing to find a good value for the loss function.

**Stochastic Gradient Descent (SGD)**

- **Stochastic Gradient Descent (SGD)** is an optimization algorithm where we repeatedly pick a training example (_i_) and then apply an update to the weights _w_ .
- The update rule is: _w := w - α * ∇Li(w)_
- _α_ is the **step size** (also known as the learning rate) which determines how big of a step to take in the direction of the gradient.
- ∇_Li(w)_ is the **gradient** of the loss on the _i_th example with respect to the weights.
- Because we are minimizing the loss function, we subtract the gradient in the update rule. This moves _w_ in the direction that reduces the loss.

**Step Size**

- The **step size** is a critical parameter in the optimization process. It makes the difference between convergence and non-convergence.
- If the step size is too large, the optimization process can **oscillate** around the minimum and never converge.
- If the step size is too small, the optimization process can take a very long time to converge to the minimum
- Choosing the right step size is not a straightforward task and is an active area of research.

**How to Choose Step Size**

- **Trial and Error:** One approach is to try a range of different orders of magnitude for the step size.
- **Start Large and Decay:** Another strategy is to start with a larger step size and gradually reduce it as the optimization process progresses. This can be done using a fixed schedule like _1/t_ or _1/√t_, where _t_ is the epoch number.
- **Decrease on Stagnation:** Another common technique is to monitor performance on a held-out validation set. If performance stagnates, the step size can be decreased.

**Limitations of Basic SGD**

- Basic SGD treats all positions of the weight vector equally. This can be problematic because different positions might reflect very different things. For example, in neural networks, they may reflect different layers, and in feature based models, they may represent common versus rare features.
- It may not be efficient to apply the same update to all of the parameters.

**Newton's Method**

- **Newton's method** uses the **curvature** of the objective function to figure out the correct step size.
- It uses the [[Inverse Hessian]] (second derivative) to make the optimization process more efficient.
- For a quadratic function, Newton's method can jump directly to the optimum in a single step.
- However, the **computation of the Hessian is very expensive** in terms of computing power and time, particularly for large models with many features (it's scales quadratically to the number of features) and is thus not generally feasible to use.

**Adaptive Methods**

- **Adaptive methods** like AdaGrad, AdaDelta, and Adam, are designed to address the limitations of SGD and Newton's method (Hessian problem). They are clever because they:
1. Approximate the inverse Hessian's behavior without actually computing it
2. Scale linearly with the number of parameters (much more efficient)
3. Adjust learning rates per-parameter based on past gradients

Think of it like this: instead of computing the full curvature information (inverse Hessian), these methods keep running estimates of how quickly each parameter should be updated based on their historical gradient behavior. This gives many of the benefits of using curvature information but at a much lower computational cost.

**Regularization**

- In classical statistics, fully optimizing the loss function is considered bad due to the **bias-variance trade-off**.
- **Regularization** is used to prevent overfitting by not fully optimizing the loss function. It is done to reduce the variance of the estimator and to improve results on new data.
- Instead of explicitly adding regularization to the objective function, it is common to use techniques like early stopping (not running as many iterations).
- Other ad hoc tricks used in deep learning such as dropout can also provide similar benefits of regularization without explicitly incorporating it into the loss function.


### **Multi-class Classification**

- ![[Attachments/seg-9.pdf]]

- Multi-class classification is a generalization of binary classification where data points are categorized into **more than two classes**.
- The set of classes is denoted by _y_, which is sometimes referred to as the output space.

**Approaches to Multi-class Classification**

- One approach is to use **one-vs-all** which involves creating a boundary that separates each class from all of the others, resulting in _n_ binary classifiers. However, this method may not work well in cases where classes cannot be easily separated using linear classification.
- Instead, multi-class classification can be formulated using two main techniques:
	- **Different Weights (DW):** Different weight vector for each class.
	- **Different Features (DF):** Same weight vector but different features for each class.
- These two methods are generally equivalent in basic settings, but DW is more suited to neural networks and DF is useful for structured classification.

**Different Weights (DW) Approach**

- In the different weights approach, each class has its own weight vector. The diff weight vectors are all dotted with the feature. The classification decision is made by taking the **arg max** over all possible classes. This means selecting the class that results in the highest magnitude.
- The expression used to determine the class is the dot product of a class-specific weight vector (_wy_) and the feature vector (_f(x)_) (same for all classes). The class with the highest dot product is the prediction.
- This approach is similar to one-vs-all, but it's not trained as a series of binary classifiers.

**Different Features (DF) Approach**

- In the different features approach, there is a single weight vector, but the features are dependent on the class.
- The feature vector is denoted as _f(x,y)_ and is created by combining the input features _f(x)_ with a hypothesized class _y_.
- The features are defined as indicators for the input and the class; if _y_ is hypothesized as the correct class, the features are "turned on" for the hypothesized class.
- A single weight vector _w_ is then used to calculate a score for the combined features, by dot product of _w_ and _f(x,y)_.
- The features are conjunctive as they look at both _x_ and _y_.
- The reason that DF is not used as much in neural networks is that it would require rerunning the network _n_ times for n-way classification.

**Example: Topic Classification**

- A topic classification example can be used to illustrate the DW and DF approaches.
- The sentence "too many drug trials too few patients" can be classified into one of three classes: health, sports, or science.
- The feature vector _f(x)_ is a bag of words, specifically unigrams with vocab: drug, patients, and baseball.
- For the sentence provided, the feature vector is [1, 1, 0] since it contains "drug" and "patients" but not "baseball".

**DW Approach in the Example**

- Each topic (health, sports, science) has its own weight vector.
- For example, the weight vector for health might assign high weights to "drug" and "patients," and a low weight to "baseball" like [4, 5, -1]. This vector is denoted as _w_health_.
- The dot product of this weight vector and the feature vector, _(w_health • f(x))_ gives a score for health == 4x1+5x1+0x-1 = 9.
- Similarly, other weight vectors give scores for sports and science eg.  _(w_sports • f(x))_ == [2, 1, 6] • [1, 1, 0] = 3.
- The topic with the highest score is chosen as the prediction.

**DF Approach in the Example**

- The feature vector _f(x,y)_ is created by replicating the feature vector _f(x)_ for each class.
- When _y=health_, _f(x,y)_ has the _f(x)_ vector at the beginning, followed by 0's i.e _[110   000,  000]_. When _y=sports_, 0s are followed by _f(x)_ in the second position i.e. _[000, 110, 000]_.
- The weight vector, _w_, can now be a single weight vector and each class can be determined by the values in the dot product.
- When you toggle the hypothesized value of _y_, different parts of the feature vector become active, resulting in different dot products.

**Structured Classification**

- The different features approach is particularly useful in **structured classification**, where the output _y_ is a more complicated object.
- For example, this could involve assigning part-of-speech tags to each word in a sentence.
- In these cases, it is not practical to have one set of weights per output class, making DF a more viable choice. The DF approach allows us to combine properties of the input with properties of the output.


### **Multi-class Perceptron**
- ![[Attachments/seg-10.pdf]]

- Multi-class perceptron is a generalization of the perceptron algorithm to handle more than two classes. The algorithm iterates through epochs and data, updating weights when the prediction is incorrect.
- **Prediction:** The prediction uses different weights, indexed by the class label _y_ (Using **DW Approach**). The predicted class is the one that maximizes the dot product of its weight vector with the feature vector _f(x)_.
- _W_y_ • _f(x)_
- **Update:** An update is made only if the predicted class (_y_pred_) does not match the correct class (_y_i_). The update involves two parts:
	- **Demoting the incorrect class:** The dot product of the weight vector for the incorrect class (_W_ypred_) with the feature vector _f(x)_ is decreased by subtracting α_f(x)_.
	- **Promoting the correct class:** The dot product of the weight vector for the correct class (_W_yi_) with the feature vector _f(x)_ is increased by adding α_f(x)_.
- The step size _α_ is also part of the update.
- The general idea is to move the model's score for the correct class higher and the score for the incorrect class lower.

**Example of Multi-class Perceptron**

- Two example sentences: 
	- "too many drug trials too few patients"; label y=1, 
	- "baseball players taking drugs";  label y=2. 
- Vocab = [drugs, patients, patients]
- The output space _y_={1, 2, 3}, and the default prediction is y=3.
- Initially, all weight vectors (_wy1_, _wy2_, _wy3_) are initialized to 0.
- **First example:** "too many drug trials too few patients" (_y_=1) => [1, 1, 0], the model predicts _y_pred_=3. An update is made: αf(x) is added to _w_y1_ and subtracted from _w_y3_.
- **Second example:** "baseball players taking drugs" (_y_=2)=> [1, 0, 1],, the model predicts _y_pred_=1 (because _w_y1_ ⋅ _f(x)_ is the highest). An update is made: _αf(x)_ is added to _w_y2_ and subtracted from _w_y1_.
- If the prediction is correct, no update is made.

**Multi-class Perceptron with Different Features(DF)**

- In the different features formulation, there is only one weight vector _W_.
- The update rule is similar: _α_ _f(x, yi)_ is added to _W_. This means that the features are associated with the correct class.
- The other updates are modified such that instead of having _y_pred_ associated with the weights, it now lives inside the feature function.

**Multi-class Logistic Regression**

- Multi-class logistic regression is a generalization of logistic regression to handle more than two classes. The probability that _y_ equals a particular class _ŷ_ is calculated as the exponential of the dot product of _w_ and the feature vector, divided by the sum of these exponentials over all possible output values.

P(y=_ŷ_|x) = exp(_w_⋅_f(x,ŷ_)) / Σy' exp(_w_⋅_f(x,y'_)

- This is different from binary logistic regression where we had 1 + exp(_w_ ⋅ _f(x)_) in the denominator. The denominator normalizes the probabilities so they sum to 1.
- **Gradient:** The gradient of the log-likelihood of the data has the form of -_f(x, yi_) + Σy P(y|x) _f(x,y)_.
- The gradient shows that the weights associated with the correct class are promoted and the weights associated with the incorrect classes are demoted.

**Gradient Analysis**

- When the probability of _y_i_ given _x_ is close to 1, the gradient is approximately zero.
- When the probability of the wrong class _y_bad_ is higher, the gradient promotes the correct class _yi_ and demotes the incorrect class _y_bad_.
- The update resembles that of multi-class perceptron, with the exception that the update is “softer” in that it takes into account the distribution over all the classes rather than just the correct and the incorrect class.
- When the probabilities are in the middle, you get a softer update where you are kicking all the other classes down a little bit and boosting the correct one.

**Key Takeaways**

- Multi-class perceptron and logistic regression are extensions of their binary counterparts for problems with more than two classes.
- Both algorithms involve updating weights based on whether the prediction is correct or not.
- The multi-class perceptron update directly adjusts the weights of the predicted and true classes.
- Multi-class logistic regression uses a probability distribution over all classes, and the update is based on the gradient of the log-likelihood.
- Both algorithms have similar update rules with slightly different approaches and they use the same idea of promoting correct classes and demoting incorrect ones.
- These algorithms will be revisited in the context of neural networks and structured prediction.




### **Multi-class Classification Examples in NLP**
- ![[Attachments/seg-11.pdf]]

- Multi-class classification is used to categorize text into more than two classes. eg:
	- Text classification
	- Textual entailment
	- Entity disambiguation or entity linking
	- Authorship attribution

**1. Text Classification**

- This involves assigning a piece of text to a topic or category.
- **Datasets** for text classification range from news articles to online forum postings.

**2. Textual Entailment**

- Textual entailment is a **three-class classification task** dealing with sentence pairs.
- The relationships between sentence pairs are:
	- **Entailment**: The first sentence implies the second sentence. For example, "A soccer game with multiple males playing" entails "Some men are playing a sport".
	- **Contradiction**: The two sentences cannot be true at the same time. For example, "A black car starts up in front of a crowd of people" contradicts "A man is driving down a lonely road".
	- **Unrelated**: The relationship between the two sentences is neither entailment nor contradiction.
- This task is challenging, especially when using basic methods like bag-of-words features.
- **Neural networks** have significantly improved performance on this task.

**3. Entity Disambiguation (Entity Linking)**

- This involves linking a mention of an entity in text to its corresponding real-world entity, often represented by a **Wikipedia article**.
- For example, in the text "… they had disqualified Armstrong from his seven consecutive Tour de France wins", "Armstrong" refers to Lance Armstrong.
- This is a difficult task as the same mention could refer to multiple entities, so it can be thought of as a **multi-class classification problem** with a very large number of classes (e.g., any article on Wikipedia).
- The number of potential classes can be reduced using techniques such as **pruning** down to articles with the mention in their titles.
- This requires a more complex feature structure than just combining indicators of the input with class label.

**4. Authorship Attribution**

- This is the task of identifying the author of a text based on their writing style.
- It is an old problem that has been studied using statistical methods for over a century.
- Stylistic information, such as **stop word frequencies and function word usage**, which are often ignored in text classification, can be very useful for this task.
- Famous historical examples include:
	- Disputes over who wrote Shakespeare’s plays.
	- Attributing the Federalist Papers to either Alexander Hamilton or James Madison.
- An example of authorship attribution in the lecture is **Twitter authorship attribution**, where the goal is to identify who wrote a given tweet from a set of potential authors.
	- A study was done using 500 million tweets from 1000 users with at least 1000 tweets each.
	- The model they used was a support vector machine with **character four-grams and word two-through-five-grams**.
	- They found that accuracy was surprisingly high, even with limited training data.
	- The key to success was finding **k-signatures**, which are n-grams that appear in a certain percentage of an author's tweets but not in others.
	- These signatures can be at the character level, or at the word level.
	- These signatures reveal distinctive patterns that basic linear classifiers can pick up, despite the challenges.

**Key Takeaways**

- Multi-class classification is a versatile technique with various applications in NLP.
- The choice of feature representation can vary depending on the task and complexity.
- These examples show the different ways we can use multi-class classification.
- Some problems require more sophisticated methods, such as neural networks, due to complex relationships.

### **Fairness in Classification**
- ![[Attachments/seg-11a.pdf]]

- When classifiers are used to make real-world decisions, they can have significant impacts on people's lives.
- It is crucial to consider the ethical implications of using classifiers, as these systems can be used in ways that discriminate unfairly.
- Classifiers should adhere to the same standards as humans, including anti-discrimination laws.

**Why Accuracy Isn't Enough**

- Focusing solely on accuracy is not sufficient to determine if a model is fair.
- It is important to analyze how classifiers treat different population subgroups.
- **Bias** is defined as when a classifier makes consistent non-zero prediction errors on a subgroup compared to the overall population.

**Illustrative Example of Bias**

- Consider two populations, pi1 and pi2, where performance on a test is plotted against ground truth performance.
- If individuals from pi1 consistently score higher on a test than individuals from pi2, despite having the same ground truth performance, the test is considered biased.
- This means the test **penalizes pi2**, as they receive lower scores despite having the same underlying ability.

**Fairness in Classification: Predicted Positives vs. Ground Truth Positives**

- Fairness in classification is also based on the ratio of predicted positives to ground truth positives.
- The ratio of predicted positives to ground truth positives must be approximately the same for each group to ensure fairness.
- For instance, if group 1 has 50% positive reviews and group 2 has 60% positive reviews, a fair classifier should predict 50% positive in group 1 and 60% positive in group 2.
- A classifier that predicts 50% positive in both groups is unfair to group 2, even if its accuracy is high for both groups, because it under-predicts the positive rate for that group.

**Adjusting Classification Thresholds**

- It is possible to use different criteria across different groups to achieve fairer results.
- This may involve adjusting the classification threshold for certain groups. For example, if a classifier under-predicts positives for group 2, one could lower the threshold for positive predictions for that group. This counter-intuitive approach could result in a fairer outcome.

**The Problem of Sensitive Features**

- Classifiers can discriminate even when explicitly avoiding sensitive features like gender or race.
- This occurs when other features correlate with membership in a minority group.
- For example, in authorship attribution, bag-of-words features can detect different dialects of English or code-switching, thus revealing the group a person belongs to.
- Similarly, zip code information, when used in loan applications, correlates heavily with race, thereby introducing bias.
- A real-world example is Amazon's resume sorting tool, which learned negative weights for women's organizations and women's colleges, revealing an unintended gender bias.


### **Introduction to Neural Networks**
- ![[Attachments/seg-12.pdf]]

- Neural networks are introduced as a method to address the limitations of linear classifiers in certain classification problems.
- A key issue with basic linear classifiers is that they cannot separate data that is not linearly separable. The example given is the sentences "good", "bad", "not good", and "not bad", which, when represented as bag-of-unigrams features, cannot be separated by a linear classifier.
- Methods like adding bigram features or using kernel methods can solve this problem, but these can be computationally expensive and have undesirable properties for NLP.
- Neural networks aim to transform the data into a **latent feature space**, where the data can be more easily separated.

**How Neural Networks Transform Data**

- Neural networks transform the input data using a **non-linear function**.
- The process involves a linear transformation followed by a non-linear activation function.
- **f(x)**: Represents the original input features vector.
- **v**: A matrix that maps the n-dimensional input feature space to a d-dimensional space.
- **z**: The latent feature vector, calculated as z = g(v * f(x)), where g is a non-linear function.
- **g**: A non-linear activation function. Common choices include hyperbolic tangent (tanh), rectified linear units (ReLU), and sigmoid functions. The choice of non-linearity is important, but the specific function used often doesn't significantly affect the outcome.

**Learning Useful Latent Features**

- The goal of the transformation by v and g is to make the classification problem easier in the transformed (latent) space.
- The classifier is then applied in this transformed space using **wT . z**, where w is the weight vector and z is the transformed input vector.
- Simplified example with features x1 and x2 to demonstrate how this works.
	- The transformation of these features to a new space using tanh and the matrix v results in new coordinates z1, z2, and z3, which are tanh(x1), tanh(x2), and tanh(x1 + x2), respectively.
	- This transformation moves data points into a space where they can be separated by a hyperplane, as opposed to the original, inseparable space.

**Conjunctive Features and Learning Feature Interactions**

- The transformations in neural networks allow the learning of **conjunctive features**, which are combinations of input features. For example, the z3 coordinate (tanh(x1 + x2)) acts as an "or gate" between x1 and x2, thus modeling how the features interact with each other.
- Neural networks can learn these feature combinations in an **end-to-end** way, without needing manual specification of the interactions.
- By learning the parameters in matrix v, as well as the best parameters for the linear classifier that operates on the output of the non-linear activation functions, the whole system can be optimized for the classification task.


### **Visualizing Feed-Forward Neural Networks**
- ![[Attachments/seg-13.pdf]]

- The lecture focuses on visualizing how feed-forward neural networks transform data through a series of operations. These transformations create a **latent feature representation** (z) that facilitates classification.
- The process involves three main stages:
	- **Warping Space:** Input features (f(x)) are multiplied by a matrix (v), which warps the feature space. This can be visualized as turning the feature space into parallelograms.
	- **Shifting:** A bias or shift is applied to the warped space, though this is often ignored in visualizations because it can be incorporated into the feature space. This step can be visualized as a simple translation or shift of the warped space.
	- **Non-linear Transformation:** The warped and shifted space is then passed through a non-linear transformation (g), such as tanh or ReLU, which squashes the space into a specific range. For example, hyperbolic tangent (tanh) maps the entire real line into the range of -1 to 1.

**Understanding the Transformations Graphically**

- These transformations can be visualized graphically, especially in low-dimensional spaces. The goal of these transformations is to map data points into a space where they are **linearly separable**, even if they were not in the original space.
- For example, if you have two sets of points (e.g., red and blue curves) that are not separable by a linear classifier, these transformations can map them into a new space where they become linearly separable.

**The Power of Deep Networks**

- By stacking multiple layers of these transformations, deep networks can learn very complex mappings. Each layer applies warping, shifting, and non-linear transformation, allowing the network to "pull apart" entangled data.
- The lecture illustrates this with two entangled spirals which, through the described series of operations, can be separated in the transformed space.
- The ability to learn these transformations automatically through backpropagation is a key advantage of neural networks.


**Key Concepts**

- **Latent Feature Space (z):** The transformed space where data is represented after passing through the neural network layers. This space is created by applying the transformations to the input feature space.
- **Linear Separability:** The goal of the transformations is to make the data linearly separable, which means that a linear classifier can effectively separate different classes in the latent space.
- **Backpropagation:** These transformations are learned through backpropagation, without needing any hand-design of these transformations.
- **End-to-End Learning:** The entire network, including the transformations, can be learned end-to-end, which makes deep networks so powerful.

**Visual Example: Transforming Entangled Spirals**

- Example of two entangled spirals to demonstrate the power of these transformations.
- Initially, the spirals are deeply entangled and not linearly separable.
- Through a series of warping, shifting, and non-linear transformations, the neural network is able to pull these spirals apart, creating a highly warped space, making them linearly separable.


### **Feedforward Neural Networks: Mathematical Notation**
- ![[Attachments/seg-14.pdf]]


- The starting point is **multi-class logistic regression**, where the probability of a class _y_ is calculated using the formula: P(y) = exp(w_y ⋅ f(x)) / Σ exp(w_y' ⋅ f(x)), where _w_y_ is the weight vector associated with class _y_.
	- _f(x)_ is the feature vector.
	- The sum in the denominator is over all possible classes _y'_.
	- This equation calculates a **scalar probability** for a single class.
- To move towards a vectorized computation, the weight vectors for each class (_w1, w2, w3..._) are stacked into a single **weight matrix (W)**.
- This allows the entire operation to be expressed in a more compact form: **softmax(W * f(x))**.
- _W_ is a matrix of size (number of classes x number of features). The output being the **vector of probabilities** for each class.

**Introducing Hidden Layers**

- A **hidden layer** is introduced to compute a latent feature representation _z_.
- This is achieved by multiplying the input feature vector _f(x)_ by a matrix _V_ (to change dimensionality) and then applying a non-linear function _g_: **z = g(V * f(x))**.
- _V_ is a matrix of size (d x n), where _n_ is the number of input features and _d_ is the dimension of the hidden layer.
- _z_ is the latent feature vector.
- The output probability vector is then computed using the same softmax operation: **softmax(W * z)**.
- The sizes of the matrices (_V_ and _W_) are critical for the operations to work correctly, and must match the sizes of the layers.

**Training Neural Networks**

- The training process aims to **[[Notes/Maximum Likelihood|maximize the log-likelihood]]** of the training data, or equivalently, **minimize the negative log-likelihood (loss)**.
- The log-likelihood of a training example is expressed as a dot product of a **selector vector _e_** with the log probabilities. _e_ is a vector with 1 in the position of the correct class and 0s everywhere else.
- This formulation ensures that the network learns to assign a high probability to the correct class.
- The loss function for a single example is given by the weights of the correct class dotted with _z_, minus the log of the sum of the exponentiated values, which is similar to multi-class logistic regression.

**Backpropagation**

- Backpropagation is used to compute the gradients of the loss with respect to the parameters (matrix _V_) in the earlier layers of the network.
- The high level idea of backpropagation involves computing an **error signal** based on the output layer and passing it back through the network.
- This error signal is used to update the parameters in the earlier layers of the network by applying the **chain rule**.
- The gradient of the loss with respect to _V_ is computed as the product of the gradient of the loss with respect to _z_ and the gradient of _z_ with respect to _V_.
- The gradient of _z_ with respect to _V_ involves the derivative of the non-linear activation function _g_, and the gradient of the linear transformation.
- For each parameter matrix (_V_ and _W_), a gradient term from the loss is combined with information from the input to that layer to update the parameters.
- Backpropagation works by using saved values from the forward pass and computing the gradient backwards.

