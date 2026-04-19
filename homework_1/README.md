HOW TO RUN / SEQUENCE OF FUNCTIONS
- load raw data
	- inventory = pd.read_csv('T_F41SCHEDULE_B43_with_missing.zip')
	
- task 1: missing data imputation
	- inventory = fix_na_carrier(inventory)
	- inventory = impute_carrier_name_and_airline_id(inventory)
	- inventory = impute_manufacture_year(inventory)
	- inventory = impute_seats_and_capacity(inventory)
	
- task 2: standardization
	- inventory = standardize_manufacturer(inventory)
	- inventory = standardize_model(inventory)
	- inventory = standardize_aircraft_status(inventory)
	- inventory = standardize_operating_status(inventory)

- task 3: drop remaining missing rows
	- inventory = inventory.dropna().reset_index(drop=True)

- task 4: Transformation and derivative variables
	- inventory = apply_boxcox_transformations(inventory)

- task 5: feature engineering
	- inventory = create_size_column(inventory)

- task 6: modeling refresher
	- results_df = build_models(inventory)
	- display(results_df)
	- print("Final dataset shape:", inventory.shape)
	- print(inventory.head())



EXPECTED OUTPUTS
Task 1: Missing Data Imputation
Missing counts and percentages for the 6 investigated columns, followed by printed confirmation that each column reaches 0 missing values after its respective imputation step.

Task 2: Standardization
Value counts before and after cleaning for MANUFACTURER, MODEL, AIRCRAFT_STATUS, and OPERATING_STATUS. MANUFACTURER unique count drops from 183 to ~121.

Task 3: Drop Missing Rows
Printed row counts: ~132313 rows before, ~101287 rows after, ~31026 rows removed. Followed by a zero-sum missing values check.

Task 4: Box-Cox Transformation
Skewness values for NUMBER_OF_SEATS (~-0.27) and CAPACITY_IN_POUNDS (~4.31), four histograms (before and after transformation for each column), and printed lambda values.

Task 5: Feature Engineering
Proportion tables and two stacked bar charts: one for OPERATING_STATUS by SIZE group, one for AIRCRAFT_STATUS by SIZE group.

Task 6: Modeling
A results table with train and test RMSE for all four models (linear regression and random forest for each target).
