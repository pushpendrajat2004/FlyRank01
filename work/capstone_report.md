                                                                    Capstone Report
Author: Pushpendra Singh Jat
Lane: Predicting Organic Success 
Repo: pushpendrajat2004/FlyRank01 
Date: September 8, 2026 

0. Abstract
This study addresses the challenge of predicting organic search success for long-tail content using pre-publication signals. Utilizing a dataset of 5.8 million records from FlyRank, we developed a Random Forest classifier to identify content likely to generate at least one organic session. By employing a grouped-validation strategy by client, we mitigated data leakage and identified that content word count is the primary driver of initial organic discovery. This single feature accounted for over 50% of the model's predictive importance. Ultimately, this output acts as a decision-support tool to help content teams prioritize existing articles for optimization and target ideal keyword profiles for new content.

1. Problem Framing
The core decision this analysis supports is how content teams should allocate resources for content optimization and new keyword targeting to maximize ROI. The unit of analysis is the individual content/article. The output is a classification score indicating the likelihood of achieving top-quartile organic performance (i.e., generating at least one organic session). Machine learning is highly valuable here because identifying the pre-publication signals (like word count and keyword length) that actually drive discovery allows human editors to formulate data-driven content architectures rather than just chasing search volume. 

2. Data Safety
This model was trained using the FlyRank Internship Warehouse (Full Release), specifically joining the dim_content and fact_content_daily_performance_sample tables. 
•	Sample Size: 5,845,433 rows utilizing a 50% Bernoulli sample. 
•	Date Window: 2026-06 to 2026-06. 
•	Leakage Prevention: client_hash_id was exclusively used as a grouping ID for validation splits to prevent the model from memorizing client-specific baseline traffic, and it was deliberately excluded from the feature set. 

3. Baseline
The transparent baseline rule used for comparison was a majority-class prediction. Because the dataset represents long-tail content where a large portion sees zero traffic, predicting the majority class (0 sessions) yields a highly skewed baseline accuracy of 95.48%. This is a fair comparison because it highlights the difficulty of the task: a high accuracy score can simply be a high base rate. 

4. Model / Analysis
A Random Forest Classifier (n_estimators=10, max_depth=10) was selected to handle the non-linear relationships between content structure and organic success. 
•	Target Definition: A binary classification (is_successful) where success is defined as generating strictly greater than 0 organic sessions (sessions_organic > 0). 
•	Feature List: keyword_char_count, keyword_token_count, url_char_count, search_volume, competition, backlinks, and word_count. 

5. Evaluation
The validation design utilized a Grouped Shuffle Split (GroupShuffleSplit, test_size=0.2), grouped by client_hash_id to ensure the model generalizes to unseen clients rather than learning historical domain authority. 
•	Metrics vs. Baseline: While the baseline achieved an accuracy of 95.48%, the Random Forest achieved an accuracy of 94.86%. However, the model achieved a Precision of 17.39% and a Recall of 3.63% (F1-Score: 0.0601) in identifying the minority success class, whereas the baseline yields 0.0 for these metrics. 
•	Error Analysis: The errors heavily stem from data sparsity. A massive portion of long-tail content has zero organic sessions, meaning the binary reduction ignores the nuances of moderate growth and struggles against the overwhelming 97% majority class. 

6. Interpretation
The Random Forest feature importances revealed that structural content signals matter significantly more than keyword metrics pre-publication. 
•	Word Count was the dominant signal, holding a 52.7% importance score. 
•	URL Character Count (16.2%) and Keyword Character Count (11.2%) were the next most critical features. 
•	Surprises: Traditional SEO metrics like search_volume (4.5%) and backlinks (2.5%) showed remarkably low predictive power for initial long-tail discovery. 

7. Recommendation
Based on the directional findings, a FlyRank editor should utilize the following ranked playbook tomorrow:
1.	Prioritize Long-Form Content: Allocate resources to expanding word count on existing thin articles, as it is the top predictor of breaking out of zero-traffic. 
2.	Refine URL Architecture: Optimize URL character length to improve visibility. 
3.	Target Keyword Length: Focus targeting efforts on keyword token/character count over raw search volume. 
4.	Limits: The model assumes independent pre-publication features, though variables like word count and keyword character count are often inherently linked. 

8. Reproducibility
To rebuild this analysis from a fresh clone:
1.	Environment: pip install duckdb huggingface_hub pandas scikit-learn matplotlib seaborn. 
2.	Data Retrieval: Use Hugging Face Hub to pull dim_content.parquet and fact_content_daily_performance_sample.parquet from the FlyRank/internship-warehouse repository. 
3.	Execution: Run the Jupyter notebook from top-to-bottom. The evaluation is sealed using random_state=42 in both the GroupShuffleSplit and the RandomForestClassifier. 

9. Acknowledgments & Data Credit
Built on the FlyRank ML Internship dataset. Data provided by FlyRank. 
