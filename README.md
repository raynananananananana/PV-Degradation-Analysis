# PV-Degradation-Analysis
Repo for the search/download/reading/analysis of articles on PV module degradation for the DuraMAT LLM project. Work completed Summer 2025 for the DOE SULI Internship
Overview
This document details the system used to find papers, automatically download and sort them, use Gemini to extract information, and produce graphs and test statistics to understand this information. This work was completed during the SULI Summer 2025 term by Rayna Hylden with mentors Baojie Li and Anubhav Jain. 

Paper Searches
Search categories and their respective queries are contained in this Google spreadsheet. (https://docs.google.com/spreadsheets/d/1Y2nzMlW0HgTGP25nct62clOAnFDzD3vHKQ78JsHSNok/edit?usp=sharing). You can search for only one category (e.g. just encapsulant, LETID, etc.) or search for all categories, depending on the  

The metadata can be manually downloaded by selecting all results and saving these to a CSV file. The download process is dependent on having this information in CSV format. For this step, save all information offered.

If you’re combining results from multiple search queries, use the segment of the code that deletes duplicate results. At this point in the process, you can also get rid of columns of information that you don’t need (for example, the abstract if it was downloaded & not needed). 

Downloading Papers to PDF
The download process occurs through two main avenues: 
API keys for specific publishers (fulltext_article_downloader)
Elsevier, IEEE, Wiley, and Springer have API keys available with an institutional account. Additionally, use any email as the “unpaywall email” as a backup for other publishers.
This code comes from Dr. Jain’s GitHub (computron), linked here. (https://github.com/computron/fulltext-article-downloader?tab=readme-ov-file) He also has a YouTube video tutorial, linked at the start of the GitHub “Read Me” 
This is the faster method
Successful downloads’ information is kept in their original CSV. Failed downloads are logged in another CSV. 
The PDFs are saved in a new output folder.
Selenium
This system runs a headless browser in Chrome. While running this, you must be already logged into an email with access to publishers (usually your LBL email or a university email) for access.
This method is significantly slower, hence why it’s the backup method to API keys.
The download code just runs the failed download CSV through this process. Successfully downloads are kept in the original CSV, and unsuccessful downloads are stored as a CSV of downloads that failed in both methods.
PDFs are saved to the same output folder as those downloaded with the first method. 
Be prepared for many of these download attempts to fail. Publishers often have specific protections against automated bulk downloading. The papers can be downloaded to PDF manually if needed. 
Some papers may appear as PDFs in the output folder, but have pages or be damaged. This is also because of publisher restrictions. 
Downloading should only be done through pathways that adhere to copyright law. If automated systems do not produce enough papers, the rest of the search results can be manually accessed with an LBL or university account and those PDFs can be used throughout the rest of the process.
File naming happens during the downloading process. The existing code uses EID, degradation type, and year to name files, but this can be adjusted to any other internally consistent system.

Information Extraction
Note on the use of LLMs for analysis:
Manually verify that the LLM in use (whether Gemini or any other) is outputting correct information. Check a sample batch of papers against the LLM interpretation to ensure that the output is thorough and correct. AI can make mistakes. 
Additionally, check which model is running in the program. The original code was run on Gemini Flash 2.5, but a more recent version may be available. 
System
The Gemini extraction Jupyter notebook configures Gemini using an API key, sets up the prompt, and outputs the information as a JSON file for each study’s PDF. The JSON files contain dictionaries used to neatly organize the information from each study. 
Temperature is set to 0 in the code so that Gemini should have the same output given the same prompt. Multiple runs are recommended to confirm this. 
Prompts
The prompts for degradation pathway (generally) and a PID-specific prompt are found in the DuraMAT LLM Google Drive under the “Degradation Pathway” folder. The prompts should be adjusted based on the details of which degradation mechanism you’re studying.
This article from Nature provides helpful guidelines for writing high quality prompts: https://www.nature.com/articles/s41562-024-01847-2 
Run your prompt on the same small sample batch that you manually checked. Make adjustments accordingly to get the correct format, type of information, etc. For best results, be specific about what you want and provide examples. 
The gemini extraction code includes a segment that graphs which categories are missing information by counting “N/A” and “not reported”. Additional graphs can be generated showing the distribution of different categories, which can be useful for comparing how different prompts lead to different outputs. 

Data Analysis
The means of data analysis is dependent on the goal of this study. Explanations of the process used on the PID study are below. Other types of degradation can follow a similar process, or the researcher may adjust as needed. 
Chi Square Tests for Independence
This is a type of statistical test that determines if two variables are significantly related or not. A large sample size is recommended. The code for running the test is in the notebook “chain_comparison.ipynb”. 
Hypotheses
The null hypothesis is the assumption that there is no relationship between the two variables (aka that they are independent).
The alternative hypothesis is the proposal that the two variables are dependent on one another, and are not only related through random chance.
Level of significance (α)
This is the threshold set in the test to determine whether the relationship between two variables can be attributed to random chance or if there is a dependence between them. 
α = 0.05 is standard. This indicates that there is a 95% chance that relationships identified as dependent truly are dependent, and a 5% chance that they were identified as dependent but truly are independent.
p-values
Each test on two variables produces a certain p-value. If this value is below your level of significance, the variables have a dependent relationship. 
For example: A chi-square test for independence is run to determine if temperature and PID type are related. 
If p-value = 0.0261 < α = 0.05, we reject the null hypothesis asserting that they are independent. 
If p-value = 0.109 > α = 0.05, we fail to reject the null hypothesis.
This essentially says that the odds of a random relationship producing the distribution observed is so small that it’s more likely than not that the variables are dependent.
Graph generation
The code also includes a number of types of graphs. Some show the distribution of results in pie charts or bar charts. Others show stacked bar graphs indicating how frequently certain categories appear with one another. These are NOT proof of statistical significance, but they do provide helpful visuals. 
Chord diagrams can also be used to visualize connections between variables, and are included in this code using HoloViews and Bokeh. These are useful when variables are all connected to one another, not with two sides. 
Sankey diagrams are also included in this, and can be more useful for relationships between two categories. 
The code to generate these graphs is generally at the end of the associated piece of code
