

## Santander(Nov23-Oct24):

Type of search in retrieval:
- HNSV
- KNN
Hybrid search experimentation
Uses AWS secret manager, AWS codebuild
experiments with cross encoder, sentence transformer
Done using Sagemaker processing job(sands run step etc is the sagemaker processing job)
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
	- ib_from- checkboxes(ticked/crossed)
	- Grids(Just boxes with text within)
	- Forms(1 big table)
	- QA
- Tablenet model used. Opensource model to detect tables in images. 
	- Finetuned to detect diff kinds of table in diff usecases.
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
- #### ib_from- checkboxes(ticked/crossed):
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
			- Using a superres library, the text within is enhanced and resized.
			- pytesseract used to extract the text.
			- Finally a dataframe formed with col, checked_or_not and text
	- ##### Extracting table if any from ib form.
		- Separately `Table_handler()` function used to extract tables if any from this ib image.
		- 



















## CAR Damage(Mar22- July22):


## Synth Data:
