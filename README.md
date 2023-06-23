<h1> Institutional-Level Monitoring of Immune Checkpoint Inhibitor IrAEs Using a Novel Natural Language Processing Algorithm</h1>



<h4>Authors: Michael Shapiro, Herut Dor, Anna Gurevich-Shapiro, Tal Etan, Ido Wolf  </h4>

<br>

<h2>Introduction</h2>

Immune checkpoint inhibitors (ICIs) have revolutionized cancer treatment but can result in severe immune-related adverse events (IrAEs). Monitoring IrAEs on a large scale is essential for personalized risk profiling and assisting in treatment decisions. In this study, we conducted an analysis of clinical notes from patients who received ICIs at the Tel Aviv Sourasky Medical Center using an ensemble of two Natural Language Processing algorithms to systematically identified seven common or severe IrAEs chosen as event of interest by the oncology department.Our analysis encompassed 108,280 clinical notes associated with 1,635 patients who had undergone ICI therapy. Additionally, we examined the utilization of corticosteroids and treatment discontinuation rates following IrAEs as an additional confirmation and adverse event severity indication. Our algorithm demonstrated high accuracy in identifying IrAEs, as indicated by an area under the curve (AUC) of 0.89 for each suspected note and F1 scores of 0.87 or higher for five out of the seven IrAEs examined at the patient level. <br>

This study presents a novel approach of utilizing NLP methods for monitoring IrAEs and we  believe it holds potential for monitoring other medications, enabling comprehensive post-marketing surveillance to identify susceptible populations and establish personalized drug safety profiles.<br>
<br>

<h2> Privacy concerns </h2>

The data for this study was prepeaired from the hospital database by detecting all patients in the medical center that have recieved one of the following immunotherapies during the last 5 years using the administration records. 
The date of the first and last administration of the immunotherapy was recorded. Then, any text that was recorded for the patient in the EHR by a physician (whether during admission , discharge or follow-up note) was extracted between the date of the first admission and up to three months after the last admission. 

**To minimize access to private medical data the following steps were taken:**
* Personal data deindentification 
* Extraction of only the immidiate context of a potential IrAE for use in labeling 
* Labeling was done by a physician from the medical center.
* All analysis done on the data was conducted on a dedicated computer on the hospital network.  

<br>

<h2> Data Files </h2>

**Due to privacy concerns we are unable to provide the data files used in the process of this work. We provide here the information regarding the data files that were extracted by the hospital and used in the rest of the work:**

1. Id_to_drug.csv-  Contains the immunotherapy received by each patient. Each patient received only one immunotherapy regime in our study. The file contain the following rows: 
    * PatNum - coded patient ids
    * Immunotherapy - The ICI drug the patient received
    * min_date - first administration of the immunotherapy. 
    * max_added_date -  3 months after the last administration of immunotherapy for the patient. 

2. all_immunotherapy.csv - contains any note that was recorded by a physician for the patients in out study between the initial administration of imunotherapy and 3 months after the last dose. 
    * PatNum - coded patient ids
    * Description_text- Free text for the note
    * Date -  The date the note was recorded
    * Note_id- a unique identifier of the each note

3. additional_text.csv - contains aditional large cohort of deindentified notes that were recorded for other patients in the medical center and extracted randomly. They were used only for FastText and AlephBert unsupervised training on a dedicated hospital computer without human access to the note content.  

4. after_treatment_data.csv - This file that contains all notes for the patients in our study starting from the max_added date, which was 3 months after the last dose of imunotherapy or the the original cutoff date(August 2021), and up to the new cutoff point(October 2022). 
The notes containing in this file were not encountered by the algorithm before and were used here to evaluate the scalability of the algorithm. 

5. steroids_immune_patients.csv - This file contains prescription data of medications containing the following generic substances: Prednisone, Prednisolone, Dexamethasone, Methylprednisolone, Hydrocortisone through the Oral or IV routes only, with a dose of at least 20mg of prednisone or equivalent. These include prescription for both inpatient care and the hospital outpatient clinics care. The file itself contains only the Execution_Date and the PatNum for each prescription\administration of the medication.

<br>

<h2> Code Files </h2>

**The following code files represent the development and testing process of the algorithm:**
1. get_initial_data.ipynb - This code extracts all potential mentions of the IrAEs of interests and prepairs the data for labeling. Then we use the labeled data to prepiar the train and test cohorts used for the rest of the experiment. 

2. fasttext_model.ipynb - This code trains a fastText encoding using the additional unstructured medical notes. Then we use the encoding as a vector representation and use them to train an LSTM model to distinguish which of IrAEs mentions are true and positive mentions due to ICI treatment. 

3. alphabertmodel.ipynb - This code is used to provide domain adaptation of the AlephBert pretrained model using the additional data. Then this adapted model is finetuned on the task of distinguishing which of IrAEs mentions are true and positive mentions due to ICI treatment. 

4. Test_the_joined_model.ipynb - this code uses the trained models from both previously mentioned files and tests thier performance on the test cohort including the use of clustering stage - where at least 2 relevant positive mentions are required for confirmation. 

5. Steroid_use_and_ICI_treatment_cessation.ipynb - This file examines the use of corticosteroid treatment after the diagnosis of IrAE or the cessation of ICI treatment as an addiitonal confirmation signal and a sign of IrAE severity.  

6. Scalability_testing.ipynb - This code was used to evalute the runtime of the algorithm on additional notes from our patients starting from the previous cut-point and up to October 2022 as provided in after_treatment_data.csv file. 

