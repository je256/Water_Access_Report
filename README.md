 # Power_BI_Water_Access_Report

<img width="862" height="494" alt="powerbi_report" src="https://github.com/user-attachments/assets/46f2f7ed-824c-43f2-8f85-5d7836dcfaef" />


## KEY INSIGHTS 
- After extensive data analysis and modeling, here are the main findings:

### 1. **Large Population Lacks Basic Water Access**
- Only 22% of Maji Ndogo’s population has access to basic water services, especially in rural areas.
- This was determined by creating a new column, Basic_water_access with the following measure :
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
- **Contaminated wells**, **broken infrastructure**, and **long queues at shared taps** are key limiting factors.
- Urban areas are better served, but still show gaps due to aging infrastructure or inadequate coverage.

### 2. **Improving Water Access Requires Targeted Interventions**
- The data reveals **which specific source types** need improvement.
- **Shared taps with >30 min queue times** need upgrades or expansion.
- **Contaminated wells** must be cleaned or replaced.
- **Public taps** should be prioritized in high-density rural areas.

### 3. **Rural Areas Incur Higher Costs**
- I estimated that improving rural sources cost 50% more due to remoteness and logistics.
- Therefore, a new column is added to the infastructure_cost table with the following measure : Rural_adjusted_Cost = Infrastructure_cost[unit_cost_USD] *1.5 
Now we have our rural costs, we can create a budget to accomodate these costs fro each improvement. So I made a new column called Budget_improvement_cost on the project_ progress table with a measure : 
````
dax
Budget_improvement_cost = 
IF(
    'project_progress'[improvement] = "Rural",
    LOOKUPVALUE('infrastructure_cost'[Rural_adjusted_cost], 'infrastructure_cost'[improvement], 'project_progress'[improvement]),
    LOOKUPVALUE('infrastructure_cost'[unit_cost_USD], 'infrastructure_cost'[improvement], 'project_progress'[improvement])
)
````
- Budget adjustments account for this, ensuring fair resource allocation.

### 4. **Impact Can Be Quantified**
- We calculated the **potential improvement in basic water access** if all planned upgrades are completed.
- Upgrading X number of sources can increase basic access by 78% increase directly impacting tens of thousands of lives.

### 5. **Provincial Reports Enable Localized Decision-Making**
- Each province has unique water challenges.
- The report supports **drill-through to provincial pages**, allowing local leaders to:
- See the **total population affected**.
- Understand **budget needs by town**.
- View **source type distribution** and **gender-specific safety issues**.
- Track progress against planned improvements.

---
