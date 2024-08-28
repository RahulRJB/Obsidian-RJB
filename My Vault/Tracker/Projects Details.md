

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

- 4 types of data: 
	- Gen page containing 1 or more table
	- checkboxes(ticked/crossed)
	- Grids(Just boxes with text within)
	- Forms(1 big table)
	- QA
- #### QA:
	- Using a form, upload the pdf/docx, Question and then submit it.
	- Redirected to a new page. If its .docx, conberted to .pdf using `docx2pdf()`.
	- pdf converted to images using `pdf2image.convert_from_path()` and poppler. 
	- Using `pytesseract()` 



## CAR Damage(Mar22- July22):


## Synth Data:
