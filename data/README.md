# data

```{r dimensions-and-glimpse-all-students}
#GOLD assessment results for all EHS students
dim(EHS_all_students)
glimpse(EHS_all_students)
```

```{r dimension-and-glimpse-race}
#GOLD assessment results for white vs. non-white EHS students
dim(EHS_with_race)
glimpse(EHS_with_race)
```

```{r dimensions-and-glimpse-dll}
#GOLD assessment results for DLL vs non-DLL EHS students
dim(EHS_Dual_Language)
glimpse(EHS_Dual_Language)
```

```{r dimensions-and-glimpse-multi-year}
#GOLD assessment results for multi-year vs first-year participating EHS students
dim(EHS_multi_year)
glimpse(EHS_multi_year)
```

## name of data file

Because of the complex structure of the original dataset, we chose to clean and organize the data in Google Sheets rather than R to simplify the reformatting process. Specifically, we created new columns indicating “yes” or “no” for Dual Language Learners, race, and multi-year students. We also cleaned by consolidating data from all Early Head Start (EHS) programs into unified columns, because the original dataset had some categories arranged in columns and others in rows.


`Category` = `which aspect is being tested` 
`WHE Bottom` = `Bottom widely held expectation scores`
`WHE Top` = `Top widely held expectation scores` 
`Average` = `Average scores` 
`# Children` = `Number of children being tested` 
`# Below` = `Number of students below average`
`% Below` = `Percent of students below average`
`# Meeting` = `Number of students meeting expectations` 
`% Meeting` = `Percentage of students meeting expectations` 
` # exceeding` = `Number of students exceeding expectations` 
`% exceeding` = `The percentage of students exceeding expectations`
`Time period` = `Fall, winter, or spring trimesters` 
`Age range` = `The age range of the students` 
`White` = `If the student is white or not`
`Dual Language` = `If the student is a dual language learner or not` 
`Multi-Year` = `If the student is a Multi-Year student or not` 

EHS_all_students: 
Rows:45 Columns: 13 
[1] "Category"    "WHE Bottom"  "WHE Top"     "# Children"  "Average"     "# Below"    
[7] "% Below"     "# Meeting"   "% Meeting"   "# exceeding" "% exceeding" "Time Period"
[13] "age range" 

EHS_with_race:
Rows:108 Columns: 14
[1] "Category"          "WHE Bottom"        "WHE Top"           "# Children"       
[5] "Average"           "# Below"           "% Below"           "# Meeting"        
[9] "% Meeting"         "# exceeding"       "% exceeding"       "Time Period"      
[13] "White (Yes or No)" "Age Range" 

EHS_Dual_Language
Rows: 108 Columns: 14
[1] "Category"     "WHE Bottom"   "WHE Top"      "# Children"   "Average"      "# Below"     
[7] "% Below"      "# Meeting"    "% Meeting"    "# exceeding"  "% exceeding"  "Time Period" 
[13] "Age Range"    "Dual Language"

EHS_multi_year
Rows: 72 Columns: 14
[1] "Category"    "WHE Bottom"  "WHE Top"     "# Children"  "Average"     "# Below"    
[7] "% Below"     "# Meeting"   "% Meeting"   "# exceeding" "% exceeding" "Time Period"
[13] "Age Range"   "Multi-Year" 

