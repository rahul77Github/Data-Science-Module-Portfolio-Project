# Data Science Module - Portfolio Project 

You are a Controller of Examination (COE) in an Examination body that regularly organizes an online examination. As a part of your reasonability, you need to generate various reports from the database. Your task is to automate the entire process using pandas. Details of the database are as follows: 

           -> Application_ID: ID of the applicant
           -> D_O_B: Date of Birth
           -> Sex: Gender of Candidate
           -> H_Qual: The highest qualification of the candidate
           -> TH_CENT_CH: First choice of examination city
           -> SEC_TH_CEN: Second Choice of Examination City
           -> CATEGORY: Category of student
           -> Rollno: Roll Number of the candidate (To be filled by CEO)
           -> cent_allot: Examination center district (To be filled by CEO)
           -> cent_add: Examination Centre Address (To be filled by CEO)
           -> exam date : Examination Date (To be filled by CEO)
           -> batch: Allotted batch on examination Date (To be filled by CEO)
           -> rep_time: Reporting time for examination (To be filled by CEO)

**Stage One: Finalization of Candidate Count**
1. Calculate the Candidate count in each district based on the first choice of examination city.
2. Assume that as per policy districts having more than 100 students should have an examination center. District in which students are less than 100, adjust those students to other districts
based on their second choices and find the final candidate count of each district.

**Stage Two: Preparation of Database**

In each examination center, the exam is to be organized in two shifts batch I & batch II(reporting time 9:00 AM & 2 PM). The exam can be conducted any number of days in a city from
September 1-30, 2023 depending upon the number of candidates in a city. Note that only one examination centre is possible in each city and a maximum of 20 students can appear in one shift.

**Based on the information mentioned above complete the examination database by allocating:**

          -> Rollno: Roll number of the candidate will start from NL2000001 onwards(eg:NL2000001,NL2000002,NL2000003……).
          -> cent_allot: allocate center by putting examination city code.
          -> cent_add: put NIELIT <District Name> as the center address in each location (for eg if the district name is ADI then the center add is NIELIT ADI).
          -> examDate: Allocate any exam date between 1st September 2023 to 30th September 2023 keeping a minimum no of examination days and not violating any conditions mentioned above.
          -> batch: Allocate batch I or II ensuring all the conditions mentioned above.
          -> rep_time: For batch I reporting time is 9 AM and for batch II reporting time is 2 PM.

Write an appropriate program to complete the aforementioned task. Sample filled database is shown below:
Rollno| cent_allot| cent_add| exam date| batch| rep_time
------|-----------|---------|----------|------|---------|
NL2000001| CHN| NIELIT CHN| 01-12-2020| 1| 9:00 AM

# Solution starts here

           import pandas as pd
           from datetime import datetime, timedelta
           import random

           # Load the dataset (assuming the dataset is named 'data.csv')
           data = pd.read_csv('examdatabase.csv')
           data.head(10)
           
           # Calculate candidate count in each district based on first choice of examination city
           district_candidate_count = data['TH_CENT_CH'].value_counts()
           district_candidate_count

           # Adjust candidates to other districts based on second choices
           def adjust_candidates(row):
               if row['TH_CENT_CH'] in district_candidate_count:
                   return row['TH_CENT_CH']
               elif row['SEC_TH_CEN'] in district_candidate_count:
                   return row['SEC_TH_CEN']
               else:
                   return None
               data['Final_Cent'] = data.apply(adjust_candidates, axis=1)
            data.head()

            # Create a mapping of examination city codes to district names
           city_district_mapping = {
               'WGL': 'WGL',
               'MAHB': 'MAHB',
               'KUN': 'KUN',
               'GUN' : 'GUN',
               'KARN' : 'KARN',
               'KRS' : 'KRS',
               'CTT' : 'CTT',
               'VIZ' : 'VIZ',
               'PRA' : 'PRA',
               'NALG' : 'NALG',
               'MED' : 'MED',
               'ADI' : 'ADI',
               'KPM' : 'KPM',
               'ANA' : 'ANA',
               'TRI': 'TRI',
               'KHAM' : 'KHAM',
               'NEL' : 'NEL',
               'SOA' : 'SOA',
               'VIZI' : 'VIZI',
               'EGOD' : 'EGOD',
               'SIR' : 'SIR',
               'NIZA' : 'NIZA',
               'PUD' : 'PUD',
               'KRK' : 'KRK',
               'WGOD' : 'WGOD',
               'VIJ' : 'VIJ',
               'PNI' : 'PNI'
               # Adding more mappings as needed
           }

           # Allocate candidates to examination centers and shifts
           def allocate_centers(row):
               district = city_district_mapping[row['Final_Cent']]
               row['cent_allot'] = row['Final_Cent']
               row['cent_add'] = f'NIELIT {district}'
               return row

           data = data.apply(allocate_centers, axis=1)

           # Allocate examDate, batch, and rep_time
           start_date = datetime(2023, 9, 1)
           end_date = datetime(2023, 9, 30)
           date_range = [start_date + timedelta(days=i) for i in range((end_date - start_date).days + 1)]

           def allocate_exam_info(row):
               row['Rollno'] = f'NL{2000000 + row.name + 1}'
               row['examDate'] = random.choice(date_range).strftime('%d-%m-%Y')
               row['batch'] = random.choice([1, 2])
               row['rep_time'] = '9:00 AM' if row['batch'] == 1 else '2:00 PM'
               return row
           data = data.apply(allocate_exam_info, axis=1)

           # Save the final examination database to a CSV file
           data[['Rollno', 'cent_allot', 'cent_add', 'examDate', 'batch', 'rep_time']].to_csv('exam_database.csv', index=False)
           
           
