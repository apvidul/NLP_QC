# NLP_QC
QC NILE PIPELINE


# PROD5

## Prepare

### Check the range of **patient_num**
Using script `src/check_patient_num.sql` to check the number of records, the min and max for **patient_num**, and how many unique patient exist

|   |  num |
|---|---|
|unique_patient_count|74682|
|min_patient_num|1|
|max_patient_num|76739|


```
Include these patient info:


Database QC:

vidul adding sample number for example bdlwo -
in `fact` table: contain all patients - 100

number of patients in fact table that also have notes in the notes table.
(80% of raprod5 patienst has notes in the note tables) - 80



patient in the notes table but  they only have empty notes - 10


effective number of patients with note with text data = 70

```

```
Nile QC:
in inle output, get all unique patientid and check if they match 70.

let say only 40 matches, 20 patietn notes have no cui output in nile results - random sample notes withing this 20 patietn cohort and do string search.

show result in patient level not note level

total number of unique patient: will be the same number in fact notes tables
total number of notes: same as encounter num
```

There are total: 17596333 **encounter_num**. every **encounter_num** is unique.
### Check START_DATE range

Using scrpit `src/check_startdate.sql`to check to earlest and latest start_date

```
1841-01-01 to 7201-11-09
```
```
Cut off date of PROD 5 is `2023-08-29`
```
|Date_Group	|Count|
|-----------|------|
|BEFORE 1990|	1315
|1990-1999|	456925
|2000|	137760
|2001|	158005
|2002|	193669
|2003|	240058
|2004|	322922
|2005|	397004
|2006|	461861
|2007|	533036
|2008|	617376
|2009|	682992
|2010|	732265
|2011|	795639
|2012|	856816
|2013|	908976
|2014|	975320
|2015|	1029318
|2016|	1065073
|2017|	1086751
|2018|	1115013
|2019|	1121401
|2020|	914444
|2021|	1053019
|2022|	1057588
|2023|	681746
|After 2023-08-29|	41


## RUN NILE

1. Dictionary

NILE Global Dictionary: `/data/vbio-methodsdev/charlesytq/dict/nile/test_dict.txt`

2. NLP properties
3. Raw Output folder: `/data/vbio-methodsdev/charlesytq/projects/prod5/output`

## QC

###  Compare with log file
Checking the total processed note and missing note and compare with the log file of NILE.
   ```
    1 to 5001 total encounter numbers in database: 1667953
    1 to 5001 missing count: 7006

    5001 to 10001 total encounter numbers in database: 1696884
    5001 to 10001 missing count: 6879

    10001 to 15001 total encounter numbers in database: 1597894
    10001 to 15001 missing count: 6565

    15001 to 20001 total encounter numbers in database: 1247896
    15001 to 20001 missing count: 5797

    20001 to 25001 total encounter numbers in database: 1057922
    20001 to 25001 missing count: 5680

    25001 to 30001 total encounter numbers in database: 1030238
    25001 to 30001 missing count: 5203

    30001 to 35001 total encounter numbers in database: 971716
    30001 to 35001 missing count: 5688

    35001 to 40001 total encounter numbers in database: 1013325
    35001 to 40001 missing count: 6398

    40001 to 45001 total encounter numbers in database: 1048085
    40001 to 45001 missing count: 6323

    45001 to 50001 total encounter numbers in database: 1104183
    45001 to 50001 missing count: 6541

    50001 to 55001 total encounter numbers in database: 1137292
    50001 to 55001 missing count: 6589

    55001 to 60001 total encounter numbers in database: 1099627
    55001 to 60001 missing count: 6009

    60001 to 65001 total encounter numbers in database: 1000332
    60001 to 65001 missing count: 5505

    65001 to 70001 total encounter numbers in database: 799968
    65001 to 70001 missing count: 3547

    70001 to 75001 total encounter numbers in database: 776837
    70001 to 75001 missing count: 3492

    75001 to 76739 total encounter numbers in database: 345036
    75001 to 76739 missing count: 1947
   ```
   The number of total encounter numbers is matching the number total notes in the log file
   
   The number of missing note is matching the number of skipping notes in the log file.




### Random Sample  
   Random sample 1000 patients, take all their encounter_num and note.
   Find the CUI `C0003873` for those notes in NILE output. 

|    |  count |
|-----|------|
| Sample Notes| 226256 |
|Sample Notes contain RA CUI| 19518|

-  Sample Notes: Randomly select 1000 patient, take all the notes for those patient
-  Sample Notes contain RA CUI: For the NILE output of theses notes, that contain CUI C0003873


    For the `19518` NILE output result that contain `C0003873`, select there raw notes and do string matching with the `17` terms that represent `C0003873` in  Global dictionary. 

    ```
    SEARCH_TERMS = [
    "rheumatoid arthritis, unspecified, site unspecified",
    "ra - rheumatoid arthritis",
    "atrophic arthritis",
    "arthritis, rheumatic",
    "chronic rheumatic arthritis",
    "arthritis rheumatoid",
    "arthritis or polyarthritis, rheumatic",
    "proliferative arthritis",
    "rheumatoid arthritis",
    "arthritis, rheumatoid",
    "rha - rheumatoid arthritis",
    "arthritis or polyarthritis, atrophic",
    "systemic rheumatoid arthritis",
    "rheumatic gout",
    "rheumatoid arthritis, unspecified",
    "rheumatoid disease",
    "ra"
    ]
    ```
    As the result: all 19518 notes found terms that represent CUI `C0003873`


`Extra QC: don't have RA but got c0003873`



## TODO: Monthly Count

Example NILE output
```
1269|4164|20150316|C0871470Y,AB00MMHGY,C0428883Y,AB00MMHGY,C2189285Y,AB000BPMY,C0018792Y,AB00RATEY,AB000BPMY,C0429025Y,C0488377Y,AB0000MSY,AB0000QTY,AB0000MSY,AB000QTCY,AB0000MSY,AB00AXISY,C0449286Y,C0542560Y,AB00AXISY,C0449286Y,C0542560Y,C0004238Y,C0344434Y,C2926591Y,C0151636Y,C3714652Y,C3900135Y,C0232297Y,C0340293U,AB000JUNY,AB0000STY,C1839341Y,C0750591Y,C0205093Y,C5203038Y,C0022116Y,C4321499Y,C0522055Y,C1707455Y,AB000ECGY,AB000MARY,C0237881N,C0750502N,C1546944N,AB0000MDY,AB0000PMY
```
For each patient, each CUI is counted only once per day.

Except two verison of Monthly count:


### Calendar Monthly Count

example format:

| Patient | Date | CUI | Count|
|---------|------|-----|------|
|Patient_Num|calender month|C000|count|
|1269| 200808 | C0003873 | 3 |

 Month format: yyyy-mm

 ` show there are same amount of patients`


### Relative Monthly Count

Select the earilest date of each CUI as month 1, every 30 days count as 1 month


| Patient | Month | CUI | Count |
|---------|-------|-----|-------|
|Patient_Num| Months from the first date| CUI | count|
| 1269| 3 | C0003873 | 4 |

```
 # Calculate month number based on 30-day periods from earliest date

 def calculate_month(date_str, earliest_date):
   
    date = datetime.strptime(date_str, '%Y%m%d')
    days_diff = (date - earliest_date).days
    return (days_diff // 30) + 1  # +1 to make first
```
