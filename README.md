# Emergency Department Visits 
ER wait times in Canada are known to be very high - so high that in fact people avoid going to the ER or sometimes don't get treated. What are the main issues that are associated with ER wait times in Canada?\
To explore this, we first look into the data - why are people going to the ER in the first place? What are their main problems and can these be addressed elsewhere? Are ER resources being used efficiently?

## Main Findings for Fiscal Year 2021-2022
- ### Less urgent and non-urgent cases make up about 35% of all emergency room visits.
 ```sql
SELECT triage_level, number_of_ed_visits,
    ROUND(100.0 * number_of_ed_visits / 
        SUM(CASE WHEN triage_level = 'Total' 
                 THEN number_of_ed_visits 
                 ELSE 0 
            END) OVER (), 2) AS triage_percent_of_total
FROM ed_visits
WHERE fiscal_year = '2021–2022' AND main_problem = 'Any' AND visit_disposition = 'Total'
ORDER BY triage_percent_of_total DESC, triage_level;
```
<img width="879" alt="Screenshot 2025-01-24 at 2 57 44 PM" src="https://github.com/user-attachments/assets/f79752bc-dde1-4bf4-9ffd-b3b1a44930a0" />

- ### Of less urgent and non-urgent cases, about 85% get discharged home
 ```sql
 SELECT triage_level, visit_disposition, number_of_ed_visits,
    ROUND(100.0 * number_of_ed_visits / 
        SUM(CASE WHEN visit_disposition = 'Total' 
                 THEN number_of_ed_visits 
                 ELSE 0 
            END) OVER (PARTITION BY triage_level), 2) AS percent_of_total
  FROM ed_visits
  WHERE fiscal_year = '2021–2022' AND main_problem = 'Any'
  ORDER BY triage_level, percent_of_total DESC;
```
<img width="869" alt="Screenshot 2025-01-24 at 3 01 40 PM" src="https://github.com/user-attachments/assets/aa7e26f9-01c8-441a-b4b0-faca2e3e76d4" />
<img width="870" alt="Screenshot 2025-01-24 at 3 01 14 PM" src="https://github.com/user-attachments/assets/f7673899-027e-427b-8000-7641481b477c" />

- ### 37% of urgent cases in the emergency department were left or not seen
```sql
SELECT triage_level, visit_disposition, number_of_ed_visits,
    ROUND(100.0 * number_of_ed_visits / 
        SUM(CASE WHEN triage_level = 'Total' 
                 THEN number_of_ed_visits 
                 ELSE 0 
            END) OVER (PARTITION BY visit_disposition), 2) AS disposition_percent_of_total
FROM ed_visits
WHERE fiscal_year = '2021–2022' AND main_problem = 'Any' AND visit_disposition = 'Not seen or left'
ORDER BY disposition_percent_of_total DESC, triage_level DESC;
```
<img width="872" alt="Screenshot 2025-01-24 at 3 06 08 PM" src="https://github.com/user-attachments/assets/b9a377ff-ada3-4dd4-9ce6-5a47aa3b96cc" />

## What does this mean? 
The question needs further narrowing down. But learning that less urgent and non-urgent cases take up such a significant amount of ER resources mean we could explore how the healthcare system could provide support for these cases in other ways. Do we need more urgent care centers? Better walk-in clinic hours?\
We also want to make sure ER resources are left for actual emergencies and urgent cases. Urgent cases have the potential of becoming dangerous later on - and leaving urgent cases in the waiting room long enough to leave means possibly dealing with something bigger down the road. How can we expand our resources in order to make sure at least the highest three levels of triage are always taken care of?

