
## Santander(Nov23-Oct24):

- #### Problem Statement:
	- Customer care executives have to answer variety of questions/work upon various user requests. Based on each query, they may have specific Identification&verification(ID&V) requirements and specific procedures to follow. For this the company maintains a set of documents (~6000) to refer from. Using RAG solution to solve the problem and reduce the training time of the agents. Index the entire doc base in a vectorDB, and based on the query asked, retrieve the Topk docs and use them for the generation using a LLM.
- #### Primary Objective:
	- We have specific metrics to optimize on:
		- Recall i.e % of times correct docs retrieved in TopK.
		- Helpfulness(correctness)(0/1/2/3)- How correct is the response
		- Honesty(yes/no)- Is the model hallucinating/inferring out of context
- #### VectorDB:
	- Using Amazon Opensearch as vectorDB. 
	- The i-exchange documents, we get the HTML, Title, view_count, audience_tags etc from a diff team.
	- We process the documents, create raw, markdown, normalised text from the HTML, get nested URLs, lowercase the audience_tags and create a list.
	- We create 2 separate indexes, 1 for the title and other for the body(contents) of the documents, chunked(200 words/chunk, no overlap).
	- We use ada_002 embedding model from OpenAI to embed the title/chunks and then ingest all of these into the 2 diff indexes.
	- We experimented with all-MiniLM-L6-v2 sentence transformer embedding model.
		- Got the model from Sagemaker Jumpstart/HuggingFace.
		- Create the model. Create Sagemaker endpoint configuration. Using the configuration, create Sagemaker endpoint and deploy the model.
		- We also finetuned this model before deploying it. Used all the queries/response from the pilot available and synthetic data for the finetuning.
		- For fine-tuning used the Sagemaker hyper-parameter tuner and HyperOpt.
		- Saved the fine-tuned model in Model Registry.
		- Recall improved, cost effective and lower latency.
	- For the index we used [[HNSW]] from nmslib engine to get the approx. nearest-neighbour. Other Options for ANN were ANNOY, ivf. Other engine options: faiss, lucene. We used cosine_similarity for the distance metrics. Other distance metrics: l2, l1, hamming, inner_product etc.
	- Delta logic:
		- The i-exchange documents get updated very frequently(weekly/daily). We can create a new/fresh index everytime, but that would involve downtime in Production, so instead we employ the delta logic.
		- The updates i.e 
			- Creation of new documents
			- Update to existing documents
			- Deletion of existing document, we receive in the form of JSONS.
		- We take the document ids to be updated/deleted and delete those documents from the index.
		- Then we create a list using the new docs to be created and docs to be updated, and then use it to create these documents in the 2 indices.
		- This delta logic is run on a schedule every couple of hours, so the index always remain updated with the latest version of the documents .
- #### Retrieval:
	- Retrieval done using HNSW, an approx nearest-neighbour algorithm.
	- We take the query, embed it and use the embedding to get the Top 10/20 similar titles from the Title index and chunks of documents from the chunk/body index.
	- Have used Semantic search primarily, for the retrieval.
	- Have also experimented with Hybrid search(semantic + keyword match). 
		- In the hybrid search, we employ both the semantic + keyword match search. The results for both of them is normalized using l2/min-max techniques to bring them down to the same scale. and then given diff weightage to each of the searches and combined using arithematic/geometric/harmonic means.
		- Did not fetch good results, just semantic search proved better. This maybe due to the fact the queries were mostly 2-3 words long.
		- We also used a split technique, i.e. using just semantic search for queries within 3 words and hybrid search for queries more than 3 words. Was not worth the effort, increased complexity and latency.
	- Colbert was thought for usage for retrieval, but not useful when the index size is in the range 5k-10k. 
	- ##### Ranking: 
		- After retrieval of Top 10/20 similar titles from the Title index and chunks of documents from the chunk/body index based on the given query, we use reranking technique to combine and get the final ranks of the different documents retrieved using the 2 indices. 
		- Initially used count(from chunk index, the number of times diff chunks of the same documents retrieved), retrieval scores and view count to get the final rank. 
		- Too much dependance on view count, so used view count binned/normalized etc to reduce dependance on it.
		- Then ranking was changed to do weighted arithematic/ geometric mean of the 2 ranking from the 2 indices.
		- [[Cross-Encoder]]:
			- Experimented with cross-encoder models like msmarco for re-ranking. The final ranking generated using this cross-encoder score, opensearch scores, and view count binned. 
- #### Generation:
	- We take the queries, the docs retrieved and a prompt and model parameters to create a payload and do API call for generation.
	- The Prompt:
		- Help the agent out, answering user query.
		- Be careful with ID&V for any steps undertaken(diff security levels present, Standard, low, enhanced etc.)
		- Answer in bulleted points.
		- Answer with 80/120/160 words
		- Just give an outline, not going into to much details pr specifics, as the context may get missed.
		- If query not clear/tooo vague/missing keywords, ask the agent to rephrase and provide more details.
		- Don't assume anything, only answer based on the content of the documents given below.
		- Few-shot prompting.
		- Tried many diff wording especially with gpt3.5
	- User vs System msg (gpt3.5):
		- gpt3.5 turbo was not good in following instructions when the context got too long.
		- Tried shuffling the instructions between the system and user prompts to check what worked.
		- Also increased the num of documents in the context, one-by-one, to check for threshold, as to after which point, the model stops following instructions.
		- All these rectified with gpt 4o/4o-mini
	- ##### GPT4o-mini vs GPT3.5 Turbo:
		- longer context window(128k vs 64k)
		- lower cost input/output tokens(almost 1/2) 
		- Lower Latency(5-10secs to 3-4secs)
		- Reduced hallucination
		- Better performing
- #### Post processing(Augmenting response):
	- Being a stochastic process, at times, the response may not be as instructed, thus we need a check in place.
	- If placeholder doc ID, 'KB00XXXXX', from few-shot prompting example of the prompt is used by the model by mistake, so not return the gpt response. Instead, respond, "Sorry, could you try again with more details"
	- If the response has any nested doc id mentioned, which is not among the 5 retrieved docs, replace it with the parent docID.
	- Order of the doc IDs mentioned in the response should be the same as order of the retrieved docs.
	- If any particular ID&V process mentioned in the response, replace with "appropriate ID&V".
	- Replace Stop words.
- #### Evaluation:
	- We save the queries, docs retrieved, model response, audience, rating/feedbacks by the agents on a daily basis to build up a dataset.
	- The feedbacks are bucketed into, 'No issues', 'Steps missing', 'Wrong process', 'i-exchange wrong', 'Others'.
	- Any docID, URL, Doc name mentioned in the feedback is extracted out. (We do a semantic search on the title index to check if any doc name is mentioned in the feedback or not)
	- We use GPT model to auto-evaluate the model response for honesty and helpfulness. For this we provide the query, docs retrieved, model response and evaluation parameters as context to the model for it to output a relevant score for helepfulness(0/1/2/3) and Honesty(yes/no).
	- For the responses rated 5 by the agents, we save them separately as ground truth. We extract the docIDs from these responses. We thus now know that the answer the given query is present in the corresponding extracted documents.
	- This feedback evaluation run on a schedule using Sagemaker processing job(sands run step etc is the sagemaker processing job). This is then submitted to the Stakeholder for further evaluation.
- #### Query Cache:
	- The inference/feedback by the agents are again retrieved and joined on a daily basis and ingested in a query index.
	- The query index maintains 1 month worth of queries
	- Now for any new query asked, first the similar queries above a particular similarity threshold(cosine_sim, 0.97) from the index in retrieved and proportion of the 5 rated queries is calculated.
	- If this proportion is above a particular threshold then the docs retrieved version numbers are matched with the most recent indexed queries' version number. If they all match then we can just use the indexed response and not have to generate again.
	- If the proportion is below a given threshold/the docs versions do not match, a confidence level is generated based on the proportion again and appended to the generated response.
- #### Contextual Retrieval:
	- Many a times the chunks of a document lack context which may lead to incorrect responses. The solution could be to do logical chunking i.e chunking based on paragraphs etc. or we can create contextual chunks. We feed the LLM with the entire document and the chunk in question. The LLM locates the chunk within the document and modifies the chunk giving it appropriate context for better retrieval.
	- Frequent updates to documents may be an issue. May not scale because of needing a LLM call for each chunk.
- #### Tricks used:
	- 
- #### Challenges:
	- gpt3.5, did not follow instructions, switched between User vs System prompt
	- Out of context responses
	- Improper ID&V
	- Index update failure ans: Context Manager
	- Reranking





## Aegon(May23-Oct23):

- #### Problem Statement:
	- In UK, people above 55 can start drawing down their pension money(decumulate) until their retirement age(66) when they receive all of the pension money.
- #### Primary Objective:
	- The objective was to predict customers most likely to decumulate based on their transactional history, the interest rates received on the pension pot etc, to develop an early prevention system. 
	- Was primarily a data analysis/data science project
- #### Data Engg:
	- The data was not at all ready for the development process. Historical data was normalised and kept across different s3 buckets.
	- Not given access to proper metadata, data dictionary, data definitions and how to join the data back into usable form. This was a TCS sponsored POC, if successful, it would be sponsored by client for prod. So no proper support from client.
	- Used Data Wrangler to visualise the data sample for all the tables. and to join them back for Feature Engg. Running processing jobs, to join all the diff table.
	- Pyspark used in some case during Data Wrangling  and transformations.
	- Univariate/bivariate analysis was done as a part of EDA.
	- VIF for multi collinearity(1-4 threshold)
- #### Data:
	- The money was invested by the customers in either SIP form or one time. This money was put into various funds(portfolio) and managed by the fund managers. This constituted the AUM(asset under management) for that person. So the fund managers, depending upon the risk accepted by the customers, used to buy units of various funds with the money thus managing the assets/AUM for the customers. These funds units were bought using the money at fund unit price of the time. As the unit price of the funds increased overtime, the portfolio valuation/AUM of the customer rose as well.
	- We had transaction details of the funds purchased/sold, along with the unit prices of these funds at the time of purchase, of customers of the bank dating back 20 years(2002-2003). These transactions was made overtime by the fund managers. So when customers used to put in money, funds were bought, if they decumulated, funds were sold, and if a particular fund was not performing at par with the market, the fund was sold and money from it was used to buy other good-performing funds.
	- This was the key data table for us. We could use this to get the diff customers over the years. Their portfolio valuation overtime and the AUMs i.e the funds they were invested in/switched to/switched from. We could also get the frequency in which diff customers put in money in the pension pot.
	- So we used this data to get the diff funds, unit prices overtime, and the interest they returned over the months/years. So we could compare their interests to get the good/bad performing funds. 
	- This data also was used to get the decumulating customers. A customer was said to decumulate if they took out >=500pounds from the pension pot, which could also only happen after the age of 55.
	- So we analysed the portfolio performance overtime and in the months leading to decumulation of the customers, as they were key indicators.
	- We also checked the frequency of adding money to the pension pot and its relation with decumulation.
	- We checked the average fund/AUM performance and tried to get a correlation decumulation.
	- On the basis of all this, we engineered diff features to predict decumulation. If a person decumulated then their features were calculated uptil the time when the first money was withdrawn. 
	- The training/validation data was from 2002 till 2021-2022. Some data of this time duration was kept aside for in-time testing. Using the model build we also tested on out-of-time data from 2023 i.e predicted people who would decumulate in 2023 based on historic data.
- #### Modelling:
	- Optuna was used to tune the model.
	- This was a binary classification problem (decumulate vs. not decumulate) with class imbalance (fewer people decumulating early), here are key metrics and tests we considered:
		1. Classification Metrics:
			- [[ROC-AUC]] Score: Crucial for binary classification, especially with imbalanced classes. 
			- Precision-Recall curve
			- Recall: This was out crucial metric, for us FP was ok but not FN. missing a decumulation case is more costly than false alerts.
			- ROC_AUC, precision-recall curves, Recall and business cost was used together to decide on the threshold value.
			- Cohen's Kappa: Shows improvement over random chance
		2. Business-Specific Metrics:
			- Cost Matrix: Different costs for false positives vs false negatives (e.g., opportunity cost of incorrect predictions) 
	-  Model Validation Approaches:
		- Cross-validation: 5-fold to ensure robust performance
		- Time-based validation: Important since pension decisions are time-dependent. We considered both in-time and out-of-time data for our test set.
		- Out-of-time validation: Testing on newer data to check for concept drift
	- Model Interpretability Tests:
		- SHAP values: To explain feature importance
		- Partial Dependence Plots: Show relationship between features and decumulation probability
		- Feature Importance Rankings: From XGBoost
	- We also performed segmented Performance Metrics:
		- Model accuracy across different age bands (e.g., 55-60, 60-65, 65+)
		- Performance for different pension pot sizes
		- Prediction accuracy based on contribution frequency patterns.
		- Model reliability for customers with varying investment risk profiles.
	- Financial Impacts:
		- Average pension value preserved through accurate predictions
		- Cost savings from early identification of potential decumulation





## GenAI LAB(Dec22-April23)(POC):

- #### Problem Statement:
	- Create a e2e solution in which there would be multiple vector databases for various related topics(features of diff products from diff competing companies, like Paisa Bazaar) and a chat UI to interact with it.
- #### Approach:
	- There were a different set of documents for different company's products. Indices were created for each of them.
		- Given a prompt, we used Logical [[Notes/RAG#^ce78b9|routing]] to route the question to the appropriate index of a particular company and retrieve from it. If the question was related to comparing products of diff companies or required info from different companies, using the routing automatically the relevant indices were chosen leveraging a LLM. Could compare only 2 indices at a time, only developed that. SO if there were 4 in total only 2 could be compared. But this can be extended beyond that
	- The chat history was to be saved for a given session for a later use.
		- A session could also have multiple topics selected at the same time or at different points of time, in which case independent retrieval was done across the diff indices and then the retrieved documents from the different indices were reranked using [[Notes/RAG#^4d4660|RAG Fusion]].
- #### Details:

	- This is an advanced RAG implementation with sophisticated features like logical routing and RAG fusion.

	Architecture & Technical Implementation: A multi-index architecture with ChromaDB. A few key technical aspects worth highlighting:
	
	1. Vector Database Design:
	- Implemented separate ChromaDB collections for different product domains
	- Used OpenAI's ada-002 embeddings.
	- Designed an efficient document chunking strategy (~500 words, with overlap), also experimented with chunks based on paragraphs/sentences, i.e logical chunking
	- Implemented metadata filtering to improve retrieval precision
	- Hybrid Search implemented(Sematic + Keyword search) for better retrieval accuracy, as the response was a lot data oriented.
	
	2. Advanced RAG Features:
	- Tried query enrichment/expansion
	- Logical Routing: Used LLM to analyze query intent and route to appropriate indices. Used Entity recognition as well.
	- 
	![[Attachments/Recording 20250117024020.webm]]
	- RAG Fusion: Implemented reciprocal rank fusion for multi-index queries
	- Session Management: Maintained conversational context across multiple topics
	
	Key Metrics & Optimization:
	
	1. Retrieval Quality Metrics:
	
	- Mean Reciprocal Rank (MRR) to evaluate routing accuracy(0.75 was the target)
	- [[ROUGE]] scores for comparing generated responses against ground truth-  Could be used because our responses were very data centric. Was used majorly during the initial part of the POC for evaluation
	- **BERTScore** for semantic similarity evaluation- Used later alongside ROUGE to check for actual semantic similarity.  **BERTScore** is based on contextual embeddings from models like BERT and focuses on semantic similarity.
	- Human evaluation scores for response relevance and accuracy(1/0 depending if the response was correct or not on a ground truth dataset of 100 queries)
	
	2. Performance Metrics:
	- Average query latency (likely around 2-3 seconds for multi-index queries)
	
	Technical Challenges & Solutions:
	
	1. Query Understanding:
	- Challenge: GPT-3.5's inconsistency in following routing instructions
	- Solution: Implemented structured output parsing and validation layers
	- Added fallback mechanisms for ambiguous queries
	
	2. Result Consolidation:
	- Challenge: Merging results from multiple indices while maintaining context
	- Solution: Implemented custom ranking algorithms
	- Used weighted scoring based on query relevance
	
	3. Session Management:
	- Challenge: Maintaining context without performance degradation
	- Solution: Implemented efficient context windowing
	- Used selective history pruning based on relevance
	
	4. System Optimization:
	- Challenge: Managing response latency with multiple index queries
	- Solution: Implemented parallel retrieval strategies
	- Added caching layers for frequently accessed embeddings
	
	Advanced Features: 
	1. Result Post-processing:
	- Source attribution and confidence scoring
	
	2. Monitoring & Logging:
	- Detailed query tracking for optimization
	- Usage analytics for system improvement
	
	Infrastructure Considerations:
	
	1. Scalability:
	- Designed for horizontal scaling of vector collections
	- Implemented efficient index updates
	- Memory-optimized retrieval patterns
	
	2. Production Readiness:
	- Error handling and recovery mechanisms eg. fallback to old state if index update failed using context window.

## ING Australia(July22-Nov22):

- #### Primary Objective:
	- Development of models as part of the early warning system (EWS) in 3 lines of business in ING- Personal Loans, Retail Mortgages, credit card
	- Needed to calculate the probability to default. 
	- Total 4 EWS models to be developed, LR model(baseline) and ML model(LGBM) for each of the 2 products.
	- The models would be developed to predict the probability of the borrower triggering bad definition (delinquency, hardship, default) in the next 3 months, further to be grouped in the risk segments high/medium/low
- #### Modelling:
	- Required huge effort in Data Engineering, preprocessing, dimensionality reduction to transform the provided data into a usable form.
	- ##### Data available:
		- Internal customer history data
		- Personal deposit data
		- Credit bureau Data (Equifax)
		- Transactional data
		- ~ 1400 features were there to begin with.
		- The data was a snapshot of 2019
	- ##### EDA:
		- Distribution analysis
		- Trend analysis
		- Factor/PC analysis
		- Univariate/Bivariate analysis
		- [[Notes/Partial Dependency Plot (pdp)|pdp]]
	- ##### Baseline model (LR model)  Feature Screening:
		- [[VIF]]
		- [[Notes/Information Value(IV)|Information Value]] (Rejected <3% data)
		- Missing Value(Rejected more than 58% data) 
		- Population Stability Index
		- Domain considerations (we reach ~700 features)
		- Bivariate(Pairwise) Correlation (reject >0.7)(we reach ~240 features) for cont variables
		- [[Notes/Cramer's V|Cramer's association]] (80% threshold) (145 features) for cat variables
		- AUC score filter(55% threshold) (68 feats.)
		- Backward/Forward stepwise LR selection modelling (28 features)
		- After dimensionality reduction we end up with 28 features gives us ROC-AUC of ~78%
	- ##### ML model Feature Screening:
		- Missing Values
		- Domain considerations (1164 feats)
		- Pairwise correlation (~95%) (930 feats.)
		- Cramer's V
		- [[Boruta]] (~400 feats.)
		- [[SHAPRFECV]] (16 feats)
	- ##### Modelling:
		- All the cont variables were binned using optbinning to make a all cat variable dataset
		- We tried out both LightGBM and RandomForest, and also tried to use stacking on these 2 models. Optuna was used to finetune the models
		- The data was from 2019, was divided in train, test and validation sets. 
		- 5 fold Cross-validation was done to prevent overfitting
		- We also had data from Sept-Nov 2021 as out-of-time test set


## UWME(Feb21-Aug21):

- #### Problem Statement:
	- There are historical documents of similar type, printed that need to be digitized.
	- Pytesseract used can nowadays be replaced by phi-3.5, Qwen2 OCR vision models. When prompted can extract tables directly 
- ####  Evaluation:
	- Accuracy of text with a row/column for pytesseract
	- And Any row/column missed out for tablenet also IoU for loss during training of TableNet() model
- #### Data: 
	- Gen page containing 1 or more table
	- insurancebinder_form- checkboxes(ticked/crossed) & Tables
	- invoices-  (Grids, Just boxes with text within)
	- SOV Forms(1 big table and some text boxes)
	- QA
- #### Tablenet Model 
	- Opensource model to detect tables in images. 
	- The model used was OriginalTableNet().
		- ![[Attachments/Pasted image 20240829134940.png]]
		- The base model is a [[DenseNet]] encoder(had tried [[VGG19]], [[ResNet]], effcientNet)![[Attachments/Pasted image 20240829135125.png]]
		- In forward pass, the input was passed through the dense layer giving 3 outputs, pool_3, pool_4, pool_5. 
		- pool_5 output is passed through a conv layer.
		- This conv output along with pool_3, pool_4, was used as input for Table_decoder and column_decoder. This gave us the Table and column output.![[Attachments/Pasted image 20240829135045.png]]![[Attachments/Pasted image 20240829135103.png]]
		- Row_decoder was also tried in similar manner, but no good results so later removed.![[Attachments/Pasted image 20240829135014.png]]
		- This model was opensource and pretrained. We had taken the weights(best checkpoint) along with the architecture from Github.
	- The model was finetuned on diff usecases, Gen_table, Insurance_binder forms, sov etc to detect diff kinds of table.
- #### Finetuning
	- class ImageFolder(nn.Module)- To start with, we manually had hand labelled the images for tables and columns and saved the paths into a dataframe. This df contains info about the various images like img_path, table_mask_path, col_mask_path. Using this class, the df is iterated through, image, table and col and row dimension arrays are loaded, transformed(Normalised, etc) and returned.![[Attachments/Pasted image 20240904030242.png]]
	- Now using this class, train_data df is loaded and train dataset created and then loaded using Pytorch Dataloader of batch_size=128. These images after loading to dataloader is used to calculate the mean and std of the normalised image tensor.   -- NOT NEEDED![[Attachments/Pasted image 20240904030329.png]]
	- The model is trained using BCE loss. The predicted table/col area and ground truth table/col area are compared, The comparison is done on every pixel, So More the intersection lesser the loss, better the model performs.![[Attachments/Pasted image 20240904030347.png]]
	- SO using the model, the BCEloss, the model is finetuned on each of the diff table types.![[Attachments/Pasted image 20240904030432.png]]![[Attachments/Pasted image 20240904030449.png]]![[Attachments/Pasted image 20240904030538.png]]![[Attachments/Pasted image 20240904030601.png]]![[Attachments/Pasted image 20240904030733.png]]![[Attachments/Pasted image 20240904030847.png]]

- #### QA:
	- Using a form, upload the pdf/docx, Question and then submit it.
	- Redirected to a new page. If its .docx, conberted to .pdf using `docx2pdf()`.
	- pdf converted to images using `pdf2image.convert_from_path()` and poppler. 
	- Using `pytesseract.image_to_string(img, conf='--psm 6 oem 3 tessedit_char_whitelist "<whitelisted char>" ')` extracting the text from these pdf images.
	- Have trained a tiny roberta model and saved the best checkpoint. Using it we get the `tokenizer=Transformers.AutoTokenizer.from_pretrained(checkpoint)`
	     `model=Transformers.AutoModelForQuestionAnswering.from_pretrained(checkpoint)`
	   `nlp = Transformers.pipeline('QA', model=model, tokenizer=tokenizer)`
	   `res = nlp({'Q': question, 'context': context})`
	   res, the answer to the question asked.
- #### ib_form- checkboxes(ticked/crossed) & Tables:
	- Can do multiple files/images at a time. Once images uploaded, saved in a local directory for further use.
	- ##### Extracting Checkboxes and text:
		- Then the image processing starts. `joblib` used to parallelize the process.
		- `remove_lines` -- All the big lines are removed.
			- Image is greyscaled, threshold and binned using opencv(cv2)
			- Using the horizontal/vertical kernels created using cv2, the lines are detected in the images and removed
		- `detect_checks` -- Detectingcheckboxes.
			- Image is greyscaled, threshold and binned using opencv(cv2)
			- Using the horizontal/vertical kernels created using cv2, small lines(approx the size of checkboxes) are detected.
			- Then checked if the horizontal and vertical line are connected. If so the the area within the boxes are checked.
			- If area is within a range(approx area range of checkboxes), these lines are marked in the image using `cv2.rectangle()` and the coordinated of these boxes saved.
		- `pytesseract` -- to extract bounding boxes of text in the image.
			- Using function `.image_to_data(output=dict)`, the coordinates of the bounding box enclosing texts in the image is generated.
			- If 2 text boxes are super close, would mean that they are part of the same text, so the coordinates are merged.
			- These text boxes are marked as well in the image using `cv2.rectangle()` and also coordinates saved.
		- `checked_box` -- Detecting which checkboxes are checked.
			- Uisng the coordinates of the check boxes, the image is cropped to get just the checkbox. 
			- Then Using kernels the checkbox H/V lines are removed.
			- Then we check if the box is empty pixels, if it is, then it means box is unchecked, otherwise checked.
		- `get_label` -- Get the text associated with the checkboxes.
			- Iterate through all the checkbox coordinates
			- Check which text box coordinates is closest to that checkbox and append it to a list.
			- So we can now associate the checkbox with its accompanying text. and also if checkbox is marked.
			- The checkboxes marked are highlighted in a separate image as well as its text.
		- `get_dataframe` -- Get a df of the text that are checked.
			- Iteratively, the bounding boxes of texts are taken and using it, image cropped one by one, 
			- Using a superres library((EDSRx3/x4, ESPCNx2/x3/x4), the text within is enhanced and resized.
			- pytesseract used to extract the text.
			- Finally a dataframe formed with col, checked_or_not and text
	- ##### Extracting table if any from ib form.
		- Separately `Table_handler()` function used to extract tables if any from this ib image.
		- for ib_table, we load the checkpoint of trained weights for better detection of the ib table.
		- We load the image, resize it, do some conversions using cv2(greyscale, LA etc)
		- After this converted to tensors, and passed through the trained OriginalTableNet() model.
		- Model outputs, table_out and column_out. It is thresholded to get the prominent table/column boundaries.
		- Using cv2.findContours(), contours for found and area of the contour checked. If area > threshold, it is a table and contour saved. From it we get the coordinates of the diff table boundaries.
		- Same done for the columns as well.
		- Using the table/column boundaries/coordinates, the original image is taken and highlighted and returned along with the coordinates, etc
		
		- Now a random column coordinate is taken and the original image is cropped with it to get just that column, then we check if that column has row lines or not.
		- To do that, the cropped image is thresholded, a horizontal kernel created, using the 2 erode_horizontal created and then this and kernel is dilated to get the horizontal line offerings/candidates.
		- Using these offerings, iterate and find the rows that are more than 4pixel thick, only choose that for the final rows. Now we check if the total no of row lines>4? i.e total 3 rows, if there are, this is table with row lines or else its a table without row lines.

		- If table has row lines:
			- The table is taken and again, the diff column coordinates are iterated through. It is now again resized, cropped to get just the col, greyscaled, and again we try to find row lines. Then we upscale the column image and using the row lines of the col we extract the data out of the rows using pytesseract. So we now have all the row data for a col. Now we check if the coordinates of the col is within which table coordinates, i.e col part of which table, sfter that we create a dict of tables and append that col as a member of this table. In the end we have dict with keys= diff tables and values=diff col with the table, data within each row of a col, confidence, bb of the rows etc. Now we have all the info about all the data in al the table of the ib page, we create dfs using this. For a table we allign its rows, after which we can transfer the data to dfs.
		- Table without row lines:
			- Again we align the table with the cols by comparing the coordinates. So we get the cols associated with each table. Now we table each table, iterate through its cols, crop the image to get the col, resize, sharpen, upscale, convert to greyscale, remove lines if any. Then we extract the bb of the text in the rows of this col using pytesseract. We check the closeness of the bbs, if 2 bbs very close or intersect, then the text within must be part of the same row and are mergerd. The  the row text is taken and added to a dictionary, same done for all the cols. Thus we get a df. Depending on the avg distance bet the bbs, we also calculate if any row is empty or not. We also check if all the cols of a table has the same no of rows.
	- So we have images of checkboxes and tables boundaries marked. We take the images and merge them and save them for display. We display all the images and the checkbox table extracted and gen table if any extracted.
- #### sov - checkboxes(ticked/crossed) & Tables:
	- Almost same as the ib_form!
- #### Invoices (Key-value pairs):
	- Here as well, multiple images can be processed at a time.
	- Image, taken, threshold found, horizontal kernel created using which diff V lines detected. The most extreme and longest vertical lines selected out of it. They must be the vertical borders. They are marked and image returned.
	- Now this marked image taken, and we try to derive boxes from it. To do that we convert to greyscale, thresholded. H and V kernels created, and used to get the H and V lines in the images, Then the lines intersecting extrated out, as they must be the boxes. The area checked,if >than a threshold area, then the coordinates of the boxes saved and the image highlighted with the boxes.
	- Now the boxes iterated through, for each box, coordinates taken and image cropped to get just the box. The box again, greyscaled, thresholded, horizontal/vertical kernels of a min line length/ width created, using which those lines are detected, contours found and marked and removed. Then pytesseract used to extract text from that box and appended to a list. These text within the box are key-value pairs.
	- Now these kv pairs of each box iterated through, ***Spacy*** used to decipher the kv pairs, extract entities using entity modelling. If the entity label== 'KEY', then saved in the key list  else if == 'Value', saved in the value list and saved. Hence we retrieve the Key-value pairs present in all the boxes of the image. We make a df out of this.
	- So the highlighted image and the KV df returned.
- #### Generic Tables form:
	- Again exactly same as the ib_form!




## CAR Damage(Feb22- June22):

- #### Problem statement:
	- We have an UK based car insurance company Aviva. They wanted to automate the customer claim initiation process and decrease the processing time. For this they wanted to build a platform in which, after the customer uploaded the photo of their damaged car, a trained object detection model(Mask-RCNN) would detect the location of the damage, and estimate the severity of the damage and potential repair cost that may be associated. 
- #### Data:
	- We were given some sample photos as training data by the clients, however it wasn't enough to train the model.
	- We looked into the public domain and got 2 datasets of damaged vehicle photos from Kaggle and UCL.
	- We had to manually hand label the entire dataset i.e create a metadata for the bounding boxes around the damaged area to be used for training.
- #### Plan:
	- Hand label the entire dataset
	- Finetune an object detection model like Mask-RCNN, to detect 2 types of classes, the entire vehicle and the damaged area of the vehicle, 3rd class was to also detect the number plate.
	- The ROI returned with bounding boxes were classified into different classes wrt damage severity such as minor/substantial/severe and in terms of the area damaged i.e windshield/bonnet/front body/side panels/hood. We modified the architecture of the model such that any damage car area returned would have 2 classes everytime, area, damage severity.
	- Given we know the details of the customer and the make of the car, estimate the cost of repair associated by combining the severity of damage and area of damage and the average cost associated with to repair that area with that severity of damage.
- #### Modelling:
	- We downloaded the pre-trained Mask-RCNN from Detectron2 by Facebook.
	- Finetuned it with the labelled images.
	- Use mAP and AV (average recall) as a metric to test the accuracy of the model
## Synth Data:












## Application:

- Permanent Add:  29/8, Purbachal North, Kalitala Main Road, Preyashi Building, 1st floor

- Address:  007, Soumya Sankalp Apartment, Kodigehalli, Kadugodi
  Whitefield, Bengaluru - 560067

- Institute:  Heritage Institute Of Technology, Kolkata

101330659295
## Queries:

- Salary?
- Location?
- Hybrid? How many times a week?
- Kolkata?
- HR name
- Save name and number


## Summary:

- ##### Skillset:
	- Generative AI, Deep Learning, NLP, Computer Vision, Classification, Regression, Ensembling, Feature Engineering, Dimensionality Reduction, Object Detection, Hyperparameter Tuning, Model Optimization, Evaluation & Validation
- ##### Short Summary:
	- As a Data Scientist, I have a strong track record of developing and deploying end-to-end machine learning solutions across a variety of business domains, including financial services, insurance, and customer care. My experience spans the full project lifecycle, from initial data exploration and feature engineering to model development, validation, and implementation. I am proficient in applying advanced techniques like deep learning, natural language processing (NLP), and traditional machine learning algorithms to solve complex business problems and deliver actionable insights. I have hands-on experience working with structured and unstructured data, and demonstrated success in both classification and regression problems. A key focus of my work involves rigorous model evaluation, ensuring robustness, and quantifying impact on business outcomes. I possess the ability to communicate model insights and their practical implications to diverse stakeholders. My projects consistently emphasize optimization for performance and efficiency, and I’m experienced in using a variety of tools and frameworks in the cloud environment.

- ##### Short Summary with Focus on NLP:    
	- As a Data Scientist, I have a proven track record of designing, developing, and deploying innovative data-driven solutions, with a particular expertise in Natural Language Processing (NLP) and machine learning. I've led projects encompassing Retrieval Augmented Generation (RAG) systems, predictive analytics, computer vision, and automated data extraction, demonstrating a comprehensive skillset. My NLP experience includes developing RAG systems using advanced techniques like semantic embeddings, hybrid search, query enrichment, and cross-encoder reranking to optimize retrieval accuracy and response quality. I have also implemented custom ranking algorithms and query caching mechanisms to achieve sub-second response times for frequent queries. I possess strong skills in document processing, data extraction from unstructured data sources (text and images), and model evaluation. I am comfortable implementing a variety of machine learning models, including classification and object detection models and possess significant experience with feature engineering, hyperparameter tuning, and rigorous model validation. In all my roles I've emphasized performance, efficiency, and practical impact. I am adept at using tools such as Langchain, OpenSearch, ChromaDB, Sagemaker, AWS Data Wrangler, PySpark, Optuna and Detectron2.
- ##### Short Summary with Focus on CV:





## Process:

| Applied           | Role(Work) | Pay | Location | <center>Status</center>       | HR, num |
| ----------------- | ---------- | --- | -------- | ----------------------------- | ------- |
| John Deere        | GenAI      | 26  | MG Road  | <center>**Selected**</center> | Priti   |
| Rocket Softwares  |            |     |          | Finish mail questions         |         |
| Hurmonics Global  |            | 30  |          | 1st round reject! <sob  sob>  |         |
| Maaze(Chandigarh) |            |     |          | 2nd round await               |         |
| HCLTech           |            | 32  |          | 2nd round                     |         |
| Deloitte          |            |     |          | 2nd round reject!             | Venu    |
| Tiger Analytics   |            |     |          | Mail again                    |         |
| EPAM              |            |     |          | 1st round select              |         |
| Quantiphi         |            |     |          | 1st round reject              |         |
| Lowe's            |            |     |          |                               |         |
| Public Sapient    |            |     |          |                               |         |
| IT Covenence      |            |     |          |                               |         |
| Jaeggar           |            |     |          |                               |         |
| Airbus            |            |     |          |                               |         |
| Tredence          |            |     |          |                               |         |




