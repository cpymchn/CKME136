# NOTES

**Inciting question:** what are book reviewers on Amazon doing -- what are their intentions or trying to achieve-- when they write/leave reviews?

## HIVE
### The Data
Name Database: 	Reviews  
Name Table: 	ProductReviewsDataSet

Name             | Type
-----------------| ----
marketplace 		 | (string)
customer_id 		 | (bigint) ==> (string)
review_id 		   | (string)
product_id 		   | (string)
product_parent 	 | (bigint)
product_title 	 | (string)
product_category | (string)
star_rating 		 | (string) ==> (int)
helpful_votes 	 | (bigint) ==> (int)
total_votes 		 | (bigint) ==> (int)
vine 			       | (string)
verified_purchase| (string)
review_headline  | (string)
review_body 		 | (string)
review_date 	   | (string) ==> (date)

* product_id appears to correspond with ISBN-10 thus author and BISAC1 are (theoretically) retrievable from other sources.
* product_parent doesn't seem to correspond to anything external to Amazon, making ascertaining original publication date a challenge.

### Basic Statistics
#### Products & Works

How many individual products?  
4,487,493
```
SELECT COUNT (DISTINCT(product_id)) FROM productreviewsdataset;
```

How many unique works of intellectual property?  
3,297,274
```
SELECT COUNT (DISTINCT(product_parent)) FROM productreviewsdataset;
```

Works with the Most Reviews  

ID          | Work                                | Note
------------|-------------------------------------|--------
"600633062" | E.L. James' Fifty Shades Trilogy 		|Erotica
"64509529"  | Donna Foley Mabry's Maude			      |KDP 
"999720718" | John Steinbeck's The Grapes of Wrath|
"965953628" | Todd Burpo's Heaven is for Real		  |"Jesus riding a rainbow-colored horse"
"889744191" | Bill O'Reilly's Killing Patton		  |Fox News
"119148606" | Sylvia Day's Bared to You			      |Erotica
"825104451" | Kathryn Stockett's The Help		      |Oprah
"758174477" | Sylvia Day's Reflected in You		    |Erotica
"178002349" | J.K Rowling's The Cuckoo's Calling  |
"695693467" | Laila Ibrahim's Yellow Crocus		    |KDP
"676714541" | Ken Follett's Fall of Giants		    |Oprah
"401439625" | Orson Scott Card's Enders Game      |
"66877953"  | David Baldacci's The Escape         |
"994527755" | Harper Lee's To Kill a Mockingbird  |
"920432311" | Jonathan Cahn's The Harbinger		    |Prophecy Fiction

Works by Number of Reviews

No of Reviews | No of Works
--------------|------------
01|	1361007
02|	 504872
03|	 285004
04|	 187915
05|	 134942
06|	 102452
07|	  80146
08|	  65304
09|	  53165
10|	  44846
11|	  38012
12|	  32748
13|	  27835
14|	  25061
15|	  22047
20|	  12996
25|	   8555
50|	   2187
100|	  526
More than 1000| 1294
More than 5000|	 85

```
SELECT COUNT(product_parent)
FROM (
    SELECT product_parent
    FROM productreviewsdataset
    GROUP BY product_parent
    HAVING COUNT(*) > 4500
)
```
```
SELECT product_parent, COUNT(product_parent)
FROM (
    SELECT product_parent
    FROM productreviewsdataset
    GROUP BY product_parent
    HAVING COUNT(*) > 4500
)
```
#### Reviews
How many reviews?  
30,540,879
```
SELECT COUNT (review_id) FROM productreviewsdataset;
```

How many reviews of print products?  
19,531,329
```
SELECT COUNT (product_category) FROM productreviewsdataset WHERE product_category='Books';
```

How many reviews of ebooks?  
11,009,548
```
SELECT COUNT (product_category) FROM productreviewsdataset WHERE product_category='Digital_Ebook_Purchase';
```
Reviews by Year  

Year| Reviews
----|--------
1995|	0000199
1996|	0004704
1997|	0036750
1998|	0121235
1999|	0245388
2000|	0541500
2001|	0445923
2002|	0427983
2003|	0441073
2004|	0441350
2005|	0518094
2006|	0564884
2007|	0755865
2008|	0821344
2009|	1001522
2010|	1103834
2011|	1281771
2012|	2407811
2013|	6021286
2014|	6106183
2015|	7342270

```
SELECT COUNT(review_id) FROM "reviews"."productreviewsdataset" WHERE review_date BETWEEN date '' AND date '';
```
Stars                   | Reviews
------------------------|---------
How many 1-Star reviews?| 1,476,928
How many 2-Star reviews?| 1,225,508
How many 3-Star reviews?| 2,433,535
How many 4-Star reviews?| 5,846,144
How many 5-Star reviews?| 19,558,762
```
SELECT COUNT(review_id) FROM "reviews"."productreviewsdataset" WHERE star_rating = '';
```
* obviously more inclined to review something if you liked it
* better to extract avg review length, min/max, etc with python... rather than hive.

#### Reviewers

How many reviewers?  
10,795,137
```
SELECT COUNT (DISTINCT(customer_id)) FROM productreviewsdataset;
```

How many books per reviewer?  

Number of Books| Number of Reviewers
---------------|--------------------
01|	6,760,778
02|	1,709,433
03|	  759,004
04|	  418,523
05|	  261,352
06|	  176,992
07|	  124,537
08|	   92,287
09|	   70,407
10|	   55,512
11|	   44,575
12|	   36,421
13|	   29,966
14|	   24,991
15|	   21,522
16|	   18,204
17|	   15,749
18|	   13,721
19|	   11,915
20|	   10,371
21|	    9,436
22| 	  8,353
23|	    7,549
24|	    6,760
25|	    6,168
30|	    3,824
35|	    2,591
40|	    1,847
45|	    1,438
50|	    1,066
60|	      717
70|	      494
80|	      363
90|	      280
100|      222
125|      142
150|       73
200|       37
300|       11
500|        5

* another powerlaw
* 225 people have reviewed over 1000 books over the ten years
* reviewing one and only one book, by itself, is not an indication of aberrant behaviour

```
SELECT COUNT(customer_id)
FROM (
    SELECT customer_id
    FROM productreviewsdataset
    GROUP BY customer_id
    HAVING COUNT(*) = 1
)
```
##### MISC
* How many unstarred reviews? Zero
* How many untitled reviews? 69
* How many title-only reviews? 250

```
SELECT * FROM "reviews"."productreviewsdataset" WHERE review_body = '';
```
* the thumbs-up/thumbs down drive-by review (like on Netflix) is either prohibited by Amazon or has been scrubbed from this dataset. Must remember to try to leave a review with stars only to see if it is permitted in the website's user interface.

### Rudimentary Anomaly Detection

Are any of the poor reviews because of delivery/damage/customer service issues?  

Compliant         | No.
------------------|-----
damaged           | 6432
shipping          | 3930 
delivery          | 3150
customer service  | 1894

* out of 1,476,928 1 star reviews that's approximately 1%...
```
SELECT count(review_body)
FROM (
SELECT (review_body) 
FROM "reviews"."productreviewsdataset" 
WHERE star_rating = 1 
)
WHERE review_body LIKE '%%'
   OR review_body LIKE '%word2%'
   OR review_body LIKE '%word3%'
```
```
SELECT regexp_extract_all('1a 2b 14m', '\d+'); -- [1, 2, 14]
```
### Review Clusters - Timing...Looking for Baseline
* Malcolm Gladwell's Blink (968642731)
* Thomas Friedman's The World is Flat (377846531)
* Freakonomics (150560620)
```
SELECT review_body, review_id, customer_id
    FROM productreviewsdataset 
    WHERE review_body in
    (SELECT review_body FROM productreviewsdataset
    GROUP BY review_body  
    HAVING COUNT(*)>1)
```
### Review Clusters - Similarity
* 2,063,392
* how many where length is greater than 25?
* 3grams with same star rating
* [todo]reviewing blocks...same customers, same works

### Training Sets - Coarse Cuts
* Recappers first pass 1,721,982
* Recappers second pass 493,412
```
SELECT review_id, review_body, star_rating 
FROM "reviews"."productreviewsdataset" 
WHERE review_body LIKE '%%' OR 
review_body LIKE '%%' OR 
review_body LIKE '%%' OR 
review_body LIKE '%%' OR 
review_body LIKE '%%' OR 
review_body LIKE '%%' OR 
review_body LIKE '%%';
```
### Other

Plot Mentions 1401146
```
SELECT count(review_body)
FROM "reviews"."productreviewsdataset" 
WHERE review_body LIKE '%plot%';
```

Format Mentions
```
SELECT COUNT (*)
FROM "reviews"."productreviewsdataset" 
WHERE review_body LIKE '%audio%'
LIMIT 100;
```
Duplicates
3170

## MODEL RESULTS

## The Spolier Finder

#### Model 1 - Robust
* Number of records for evaluation data 	42774
* Number of records for training data 	334128
* Score: 0.500
* Baseline: 0.330 

#### Model 2 - Lean
* Number of records for evaluation data 	13971
* Number of records for training data 	28803
* Score: 0.750
* Baseline: 0.487 

#### Model 3 - Binary Clean, Lean, and Dialed-In
* Number of records for evaluation data 	18000
* Number of records for training data 	42000
* Score: 0.991
* Baseline: 0.500 
* 97% are correct
    * 11,726 true positive
    * 5,726 true negative
 * 3% are errors
    * 268 false positive
    * 280 false negative
* 67% of the records are predicted as true
* 33% of the records are predicted as false
  * False positive rate 0.0447
  * Precision 0.9777
  * Recall 0.9767
  * Accuracy 0.9696 

## The Insincerity Finder
* possible avenue to explore... hypothesis --> the more an author solicits reviews, the higher instance of 5-star reviews being mixed/negative sentiment?
* i.e., if there is a higher than normal discrepancy between star rating and sentiment, then the review was likely solicited
* see below for comparison of two titles... one where the author solicited reviews and one where the author did not

Profile	| Sentiment |	1 Star  |	2 Star  |	3 Star  |	4 Star  |	5 Star  |	Total |	% = Error | Percent of Total
--------|-----------|---------|---------|---------|---------|---------|-------|---------|------------------
Organic	|Positive	  |20	      |46	      |132      |365      |650      |1213	  |1.65     |63.31%
Organic	|Neutral	  |27	      |31	      |54	      |79	      |150	    |341	  |84.16    |17.80%
Organic	|Mixed	    |9	      |25	      |56	      |31	      |5	      |126	  |11.11    |6.58%
Organic	|Negative	  |95	      |64	      |56	      |15	      |6	      |236	  |2.54     |12.32%
Solicited|	Positive|	16|	16|	69|	441|	3680|	4222|	0.38|	65.64%
Solicited|	Neutral|	77|	51|	119|	242|	1225|	1714|	93.06|	26.65%
Solicited|	Mixed|	4|	13|	42|	33|	24|	116|	24.14|	1.80%
Solicited|	Negative|	124|	88|	87|	32|	49|	380|	12.89|	5.91%

* not sure how to build an application to do this at scale

## Date Anomaly Finder

```
# Linear Regression in R
book <- read.delim("~/Documents/blinkReviewsMonth.csv")
month <- as.numeric(book$review_month)
plot(review_count~month, data=book)
meanLine = mean(book$review_count); abline(h=meanLine)
regressionModel=lm(review_count~month, data=book); regressionModel; abline(regressionModel)
#For the Residuals
plot(regressionModel)
termplot(regressionModel)
summary(regressionModel)
```
### Results Book One 

Coefficients              

(Intercept) |    date.num  
------------|-------------
35.198390   |   -0.001357  

Residuals

Min|1Q|Median|3Q|Max
-------|--------|-------|-------|-------  
-17.015|  -6.959| -2.860|  6.411| 35.193 

Coefficients

_           | Estimate |Std. Error|t value | Pr(>|t|)   
----------- |----------|----------|--------|-------------  
(Intercept) |35.1983899|10.8871128|   3.233|  0.00156  
date.num    |-0.0013571| 0.0007387|  -1.837|  0.06856 

Signif. codes:  0 ``***`` 0.001 ``**`` 0.01 ``*`` 0.05 ``.`` 0.1 `` `` 1

Residual standard error: 9.502 on 126 degrees of freedom  
Multiple R-squared:  0.02609,	Adjusted R-squared:  0.01836   
F-statistic: 3.375 on 1 and 126 DF,  p-value: 0.06856
 
### Results Book Two 

Coefficients  

(Intercept) |       month     
------------|-------------  
36.5454 |     -0.2471   
    
Residuals  

Min    |  1Q    | Median|    3Q |    Max   
---------|--------|-------|-------|--------- 
-20.672    |-12.740 | -6.942|  9.605|  60.690 

Coefficients

_|Estimate |Std. Error |t value |Pr(>|t|)   
----------- |----------|----------|--------|---------------  
(Intercept) |36.54542  |  3.18389 | 11.478 | > 2e-16    
month       |-0.24713  |  0.04385 | -5.635 | 1.13e-07

Signif. codes:  0 ``***`` 0.001 ``**`` 0.01 ``*`` 0.05 ``.`` 0.1 `` `` 1

Residual standard error: 17.69 on 123 degrees of freedom  
Multiple R-squared:  0.2052,	Adjusted R-squared:  0.1987   
F-statistic: 31.76 on 1 and 123 DF,  p-value: 0.000000113 

### Results Book Three

Coefficients  

(Intercept) |       month  
------------|-------------  
31.5872     | -0.3927  

Residuals 

Min    |  1Q    | Median|    3Q |    Max   
---------|--------|-------|-------|--------- 
-17.629  |-8.810  |-1.617 |  7.075|  33.125 

Coefficients  

_|Estimate |Std. Error |t value |Pr(>|t|)   
----------- |----------|----------|--------|---------------  
(Intercept) |31.58719  |  2.21526 |  14.26 |  <2e-16 ``***``  
month       |-0.39269  |  0.03663 | -10.72 |  <2e-16 ``***``  

Signif. codes:  0 ``***`` 0.001 ``**`` 0.01 ``*`` 0.05 ``.`` 0.1 `` `` 1

Residual standard error: 11.21 on 102 degrees of freedom  
Multiple R-squared:  0.5298,	Adjusted R-squared:  0.5252   
F-statistic: 114.9 on 1 and 102 DF,  p-value: < 2.2e-16 

### Results Book Four

####  Coefficients

(Intercept) |       month  
------------|------------- 
-5.5018 |      0.2488
    
####  Residuals

Min    |  1Q    | Median|    3Q |    Max   
---------|--------|-------|-------|---------  
-36.232 |-22.450  |-3.882 |  7.917| 297.007 

####  Coefficients

-|Estimate |Std. Error |t value |Pr(>|t|)   
----------- |----------|----------|--------|---------------   
(Intercept)| -5.50183 |   5.01379 | -1.097 |   0.274    
month       | 0.24877  |  0.03747  | 6.639 | 2.27e-10 ``***``

Signif. codes:  0 ``***`` 0.001 ``**`` 0.01 ``*`` 0.05 ``.`` 0.1 `` `` 1

Residual standard error: 37.98 on 229 degrees of freedom  
Multiple R-squared:  0.1614,	Adjusted R-squared:  0.1577     
F-statistic: 44.07 on 1 and 229 DF,  p-value: 0.0000000002268

### Results Book Five

#### Coefficients

(Intercept) |       month    
------------|-------------  
   -27.6818 |      0.5161    
   
####  Residuals

Min    |  1Q    | Median|    3Q |    Max   
---------|--------|-------|-------|---------  
-64.32 |-39.82 |-15.19  |17.86 |562.13 

#### Coefficients

-|Estimate |Std. Error |t value |Pr(>|t|)   
----------- |----------|----------|--------|-----------------         
(Intercept) |-27.6818  |  10.3485 | -2.675 | 0.00804 ``** ``    
month       |  0.5161  |   0.0812 |  6.357 |1.19e-09  ``***``

Signif. codes:  0 ``***`` 0.001 ``**`` 0.01 ``*`` 0.05 ``.`` 0.1 `` `` 1

Residual standard error: 76.48 on 218 degrees of freedom   
Multiple R-squared:  0.1564,	Adjusted R-squared:  0.1525   
F-statistic: 40.41 on 1 and 218 DF,  p-value: 1.191e-09
____________________________________________________________________________________

* Like titles help predict like titles... I am not sure I have the data to group titles together inorder to harness the predictive power. At the very least I need date of publication.
* Gut tells me most titles behave like books 1-3 above. Four and five are likely anonmalies... how to check?
* Ask Kanchana what to do now... also how to scale this???
* Find notes on poisson regression
* What about Loess?



