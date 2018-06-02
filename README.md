# Innoplexus-challenge

I am sharing my approach to the Innoplexus-Challenge hosted by Analytics Vidhya https://datahack.analyticsvidhya.com/contest/innoplexus-hiring-hackathon/ Glad that I made it up to the top of the leaderboard. Thank you so much for this opportunity guys to test our knowledge on machine learning. 

# Problem

This is just a 2 day challenge, the objective of the competition is to predict the list of other articles which will be referenced by an article. The train dataset contains information about the article, authors, title, abstract, full text, set, published date and the list of all the articles it has as references. Test dataset as expected contains everything except the reference list. Please visit the competition link to know more about it.

# Approach
In this competition I mainly focussed on feature extraction. There were no direct features which we can use in our algorithm. With very less features available I thought identifying correct features which will be the key in this competition. Used most of the available time in identifying features and finding out the best probability to identify the reference articles. I have just the basic knowledge in NLP and just tried to identify features using that. Xgboost has always been my first go-to model as it is fast and gets good comparable results with anyother model.  

# Feature Extraction
1. Used title, abstract, author, published date to identify the features. Since full text had data for very less number of articles I chose to ignore that field.
2. Split all the string columns to get all the words, removed the stop words and got the stem of each word. The author field was split by comma to extract the complete name of the author.
3. For each article found all the articles which belonged to the same set whose published date on or before the published date and created one row for each of them. Based on the reference list marked them if they were referenced or not.
4. Found the distance between the corresponding string columns by matching the words between original pmid and related pmid. Also in the same way calculated the distance between title and abstract and vice versa of the 2 articles.
5. Difference between the published dates of the 2 articles.

# Algorithm and prediction
1. Test set is also created the same way as above.
2. Used Xgboost algorithm to predict the probability if the pmid's are related are not. Used AUC as the loss function and a learning rate of 0.05.
3. Selected a base probability of 0.12 through trial and error based on the scores I got from the public leaderboard
4. Those articles which didnt have even one record with that probability will be retried with (probability/1.5). This is made recursive until I got all the predictions or reach a very low probability. If there were still some articles left the reference list is made empty. Not sure if this is the best approach, tried this to avoid too much noise getting into prediction and also to have the necessary articles in the list.

# Things that worked/didn't
## Worked
1. Distance calculation is not actually intersection of both the strings, distance will be just based on if words in the main article is present in the related article. Words that are present in the related article and not in main article doesn't make any impact to distance.
2. Stemming of words and stop words helped.
3. Cross distance of strings between title and abstract and vice versa

## Did not work
1. Lemmatization of words did not help much.
2. Using only noun and verbs for distance calculation.
3. Somehow five fold Xgboost gave a lesser score, so went with just 1 fold

## Things that I wanted to try but couldn't
1. Semantic distance of the string columns.
2. Have a proper logic for identifying the correct base probability by calculation the F1 score against a validation set.
3. More features and some ensembles.
