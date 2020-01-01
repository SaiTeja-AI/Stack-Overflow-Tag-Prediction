# Stack-Overflow:Tag-Prediction
<h1> 1.BusinessProblem </h1>
<h2> 1.1 Description </h2>
<p>
Stack Overflow is the largest, most trusted online community for developers to learn, share their programming knowledge, and build their careers.<br />
<br />
Stack Overflow is something which every programmer use one way or another. Each month, over 50 million developers come to Stack Overflow to learn, share their knowledge, and build their careers. It features questions and answers on a wide range of topics in computer programming. The website serves as a platform for users to ask and answer questions, and, through membership and active participation, to vote questions and answers up or down and edit questions and answers in a fashion similar to a wiki or Digg. As of April 2014 Stack Overflow has over 4,000,000 registered users, and it exceeded 10,000,000 questions in late August 2015. Based on the type of tags assigned to questions, the top eight most discussed topics on the site are: Java, JavaScript, C#, PHP, Android, jQuery, Python and HTML.<br />
<br />
</p>
<h2> 1.2 Real World / Business Objectives and Constraints </h2>
<ol type = "1">
  <li> Predict as many tags as possible with high precision and recall.</li>
  <li> Incorrect tags could impact customer experience on StackOverflow.</li>
  <li> No strict latency constraints.</li>
</ol>
<h2> 1.3 Data Field Explanantion </h2>
Dataset contains 6,034,195 rows. The columns in the table are:<br />
<pre>
<b>Id</b> - Unique identifier for each question<br />
<b>Title</b> - The question's title<br />
<b>Body</b> - The body of the question<br />
<b>Tags</b> - The tags associated with the question in a space-seperated format (all lowercase, should not contain tabs '\t' or ampersands '&')<br />
</pre>

<br />

<h2>2. Mapping the real-world problem to a Machine Learning Problem </h2>
<h3> 2.1 Type of Machine Learning Problem </h3>
<p> It is a multi-label classification problem  <br>
<b>Multi-label Classification</b>: Multilabel classification assigns to each sample a set of target labels. This can be thought as predicting properties of a data-point that are not mutually exclusive, such as topics that are relevant for a document. A question on Stackoverflow might be about any of C, Pointers, FileIO and/or memory-management at the same time or none of these. <br>
__Credit__: http://scikit-learn.org/stable/modules/multiclass.html
</p>
<h3>2.2 Performance metric </h3>
<b>'Micro-Averaged F1-Score (Mean F Score)': </b><br> 
<p>The F1 score can be interpreted as a weighted average of the precision and recall, where an F1 score reaches its best value at 1 and worst score at 0. The relative contribution of precision and recall to the F1 score are equal.</p>
<p> The formula for the F1 score is:

<p> F1 = 2 * (precision * recall) / (precision + recall)</p>



In the multi-class and multi-label case, this is the weighted average of the F1 score of each class. <br>

<b>'Micro f1 score': </b><br>
Calculate metrics globally by counting the total true positives, false negatives and false positives. This is a better metric when we have class imbalance.
<br>

<b>'Macro f1 score': </b><br>
Calculate metrics for each label, and find their unweighted mean. This does not take label imbalance into account.
<br>

https://www.kaggle.com/wiki/MeanFScore <br>
http://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html <br>
<br>
<b> Hamming loss </b>: The Hamming loss is the fraction of labels that are incorrectly predicted. <br>
https://www.kaggle.com/wiki/HammingLoss <br>
<h2> 3.Exploratory Data Analysis </h2>
<h3> 3.1 Analysis of Tags </h3>
<h3> 3.1.2 Total Number of Unique Tags </h3>
<p> there are total 42048 tags are present </p>
<p> Distribution of Number of times each tag appered </p>
<img src = "images\dist_tags.png">
<p> top most 500 tags and their distribution </p>
<img src = "images\dist_tags_500.png">
<h3> Observations </h3>
<ol type = "1">
  <li> There are total 153 tags which are used more than 10000 times. </li>
  <li> 14 tags are used more than 100000 times.</li>
  <li> Most frequent tag (i.e. c#) is used 331505 times.</li>
</ol>
<h3> 3.1.3 Distribution of number of tags in question </h3>
<img src = "images\dist_tags_3.png">
<h3> obseravtions </h3>
<ol type = "1">
  <li> Maximum number of tags per question: 5. </li>
  <li> Minimum number of tags per question: 1.</li>
  <li> Avg. number of tags per question: 2.899.</li>
</ol>
<h3> Freequency of top 20 Tags </h3>
<img src = "images\dist_tags_4.png">
<h3> 4. Preprocessing of questions </h3>
<ol> 
    <li> Separate Code from Body </li>
    <li> Remove Spcial characters from Question title and description (not in code)</li>
    <li> Give more weightage to title : Add title three times to the question  </li>
    <li> Remove stop words (Except 'C') </li>
    <li> Remove HTML Tags </li>
    <li> Convert all the characters into small letters </li>
    <li> Use SnowballStemmer to stem the words </li>
</ol>
<p> here we are using only top 500 tags because we pose it as multi label problem and to solve this we have to run 500 models separetly
  so it is very time consuming if we take all 40000 tags then we have to 40000 models so it is computationally expensive task.</p>
<p> below is the coverage of number of questions if we take top most 500 questions </p>
<img src = "images\dist_tags_5.png">
<h3> Models </h3>
<ol>
  <li> i used bag of words representation upto 4 grams for text data and applied logistic regression one vs rest model. </li>
  <li> did hyperparameter tuning also to find best C </li>
  <li> finally applied Linear SVC One vs Rest </li>
</ol>
<p> below table descibes the results </p>
<table>
  <tr>
    <th> Model </th>
    <th> C value </th>
    <th> precision </th>
    <th> recall   </th>
    <th> f1-score </th>
  </tr>
  <tr>
    <td> Logistic Regression </td>
    <td> 0.01 </td>
    <td> 0.7 </td>
    <td> 0.24 </td>
    <td> 0.36 </td>
  </tr>
  <tr>
    <td> Logistic Regression </td>
    <td> 100 </td>
    <td> 0.57 </td>
    <td> 0.34 </td>
    <td> 0.43 </td>
  </tr>
  <tr>
    <td> SGD with hinge loss </td>
    <td> 0.001 </td>
    <td> 0.72 </td>
    <td> 0.3 </td>
    <td> 0.42 </td>
  </tr>
  <tr>
    <td> SGD with hinge loss </td>
    <td> 0.01 </td>
    <td> 0.78 </td>
    <td> 0.18 </td>
    <td> 0.29 </td>
  </tr>
</table>
<h3> Credits </h3>
<a href="www.appliedaicourse.com">Applied Ai</a>
  
  
