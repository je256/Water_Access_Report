 # Power_BI_Water_Access_Report

<img width="862" height="494" alt="powerbi_report" src="https://github.com/user-attachments/assets/46f2f7ed-824c-43f2-8f85-5d7836dcfaef" />


## KEY INSIGHTS 
- After extensive data analysis and modeling, here are the main findings:

### 1. **Large Population Lacks Basic Water Access**
- Only 22% of Maji Ndogo’s population has access to basic water services, especially in rural areas.
- This was determined by creating a new column, 'Basic_water_access' with the following formula:
````
dax
Basic_water_access = 
IF(
    AND(
        'water_source'[type_of_water_source] = "well",
        RELATED('well_pollution'[description]) = "Clean"
    ),
    "Basic Access",
    IF(
        'water_source'[type_of_water_source] = "tap_in_home",
        "Basic Access",
        IF(
            AND(
                'water_source'[type_of_water_source] = "shared_taps",
                'water_source'[Average_queue_time] < 30
            ),
            "Basic Access",
            "Below Basic Access"
        )
    )
)
````
- This categorised the rows into Basic_water_access and Below_basic_water_access based on the following conditions, if the description is 'Clean' and water source is a well; if the water source in a tap in the home; if the water source is a shared tap whose queue time is under 30 minutes.
- Then a measure was created to find the percentage of the population with basic access to water. 

````
dax
Basic_water_access_% = 
DIVIDE(
    CALCULATE( 
        SUM('water_source'[number_of_people_served]),
        FILTER(
            'water_source',
            'water_source'[Basic_water_access] = "Basic Access"
        )
    ),
    SUM('water_source'[number_of_people_served])
)
````
- To improve basic water access in Maji ndogo :
- More wells need to be clean
- The infrastructure needs repair 
- Queue times at shared taps need to be under 30 minutes.
- Urban areas are better served, but still show gaps due to aging infrastructure or inadequate coverage.

### 2. **Improving Water Access Requires Targeted Interventions**
- From the data provided, we are able to target which water sources to improve.
- Most of our population in urban areas use wells and taps in their homes while most people in rural areas use wells and shared taps.

### 3. **Rural Areas Incur Higher Costs**
- It was estimated that improving rural sources cost 50% more due to remoteness and logistics.
- Therefore, a new column is added to the infastructure_cost table with the following formula : Rural_adjusted_Cost = Infrastructure_cost[unit_cost_USD] *1.5 
- Now the rural costs have been calculated, we can create a budget to accomodate these costs for each improvement. 
- A new column called Budget_improvement_cost from the project_ progress table is made with the following formula : 
````
dax
Budget_improvement_cost = 
IF(
    'project_progress'[town] = "Rural",
    LOOKUPVALUE('infrastructure_cost'[Rural_adjusted_cost], 'infrastructure_cost'[improvement], 'project_progress'[improvement]),
    LOOKUPVALUE('infrastructure_cost'[unit_cost_USD], 'infrastructure_cost'[improvement], 'project_progress'[improvement])
)
````
- Budget adjustments account for this, ensuring fair resource allocation.
- Rural sources incur more costs but serve a larger number of the population , therefore increasing the percentage of people with access to basic water 

### 4. **Impact Can Be Quantified**
- If all the planned upgrades are completed, a 78% improvement to basic access is expected nationally.
- This directly impacts tens of thousands of lives.
- The wells serve the highest number of people both in the urban (6467) and rural (10,916) areas and repairing just the wells has a 72% improvement on the access to basic water and costs about half of our budget, 46.99M.
- From our report, it is also possible to view these metrics based on province.

### 5. **Provincial Reports Enable Localized Decision-Making**
- Each province has unique water challenges.
- The report supports drill-through to provincial pages, allowing local leaders to:
- See the total population affected.
- Understand budget needs by town.
- View source type distribution and gender-specific safety issues.
- Track progress against planned improvements.

---
