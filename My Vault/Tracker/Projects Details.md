

## Santander(Nov23-Oct24):


- #### Retrieval
	- opensearch used for vector database.
	- Type of search in retrieval:
		- HNSV
		- KNN
	- Retrieval using semantic search(vector embedding similarity)
	- Hybrid search experimentation done(semantic + keyword). Various combination(arithematic/geometric/harmonic) and normalisation(l2/min-max) techniques used and diff weights tried.
	- 
- Uses AWS secret manager, AWS codebuild

- Done using Sagemaker processing job(sands run step etc is the sagemaker processing job)

- #### Ranking 
	- Initially retrieval ranking and view count used. Too much dependance on view count so used view count binned/normalized etc to reduce dependance. 
	- Then ranking was changed to do weighted arithematic/ geometric mean of the 2.
	- Cross-encoder- Colbert and msmarco models used too for reranking. compared to title and chunked index and scored. Ranking generated using this score, opensearch scores, and view count binned.
- #### Embeddings
	- ada_2_embedding model used.
	- Experimented with all-MiniLM-L6-v2 sentence transformer embedding model as well.
	- Used cross-encoder to integrate
	- Created Sagemaker endpoints for these embeddings models. 
## Aegon(May23-Oct23):

- Lots of different table normalised and kept in s3 buckets.
- Used Data Wrangler for Feature Engg. running processing jobs, to join all the diff table that were normalised and kept in s3 buckets.
- Pyspark used in some case during Data Wrangling.
- Test cases in Codebuild, like data validation
- Did univariate/bivariate analysis
- VIF for multi collinearity(1.4 threshold)
- 
## GenAI LAB(Feb23-April23):


## ING Australia(Aug22-Jan22):

- Understand ROC-AUC, AUC relation with F1 and skewed data
- performed Blending/stacking
- LightGBM/XgBoost
- Did univariate/bivariate analysis
- VIF for multi collinearity(1.4 threshold)
- Blending/Stacking

## UWME(Nov21-Feb22):

- 4 types of images: 
	- Gen page containing 1 or more table
	- insurancebinder_form- checkboxes(ticked/crossed) & Tables
	- invoices-  (Grids, Just boxes with text within)
	- SOV Forms(1 big table and some text boxes)
	- QA
- #### Tablenet model used. 
	- Opensource model to detect tables in images. 
	- Finetuned to detect diff kinds of table in diff usecases.
	- The model used was OriginalTableNet().
		- ![[Attachments/Pasted image 20240829134940.png]]
		- The base model is a [[DenseNet]] encoder(had tried [[VGG19]], [[ResNet]], effcientNet)![[Attachments/Pasted image 20240829135125.png]]
		- In forward pass, the input was passed through the dense layer giving 3 outputs, pool_3, pool_4, pool_5. 
		- pool_5 output is passed through a conv layer.
		- This conv output along with pool_3, pool_4, was used as input for Table_decoder and column_decoder. This gave us the Table and column output.![[Attachments/Pasted image 20240829135045.png]]![[Attachments/Pasted image 20240829135103.png]]
		- Row_decoder was also tried in similar manner, but no good results so later removed.![[Attachments/Pasted image 20240829135014.png]]
		- This model was opensource and pretrained. We had taken the weights(best checkpoint) along with the architecture from Github.
	- The model was finetuned on diff usecases, Gen_table, Insurance_binder forms, sov etc.
		- <Finetuning.>
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
	- Now this image taken, 



















## CAR Damage(Mar22- July22):


## Synth Data:
