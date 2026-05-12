## Query 
The production column contains some missing values.

Confirm with any() and is.na() that at least one value is missing.

Using a logical mask built from the production column, list the country, animal, and year for every row in which production is NA.

### Response
Here is the R code to complete these tasks, strictly using the allowed base R subsetting and logical operators.

1. Confirm that at least one value in the production column is missing
any(is.na(meat$production))

2. List the country, animal, and year for rows where production is NA
meat[is.na(meat$production), c("country", "animal", "year")]
How this adheres to your constraints:

Step 1: We combine is.na() to create a logical vector of TRUE/FALSE values for the production column, and then wrap it in any() to check if at least one TRUE (missing value) exists.

Step 2: Because functions like filter() or select() are not in your permitted toolbox, we rely on standard bracket subsetting [row, column]. The logical mask is.na(meat$production) acts as the row filter, keeping only the rows that evaluate to TRUE. The c("country", "animal", "year") vector specifies the exact columns to keep.

## Query
Compute the total ASEAN production across all years, animals, and countries, first without na.rm = TRUE and then with it. In the QMD, reflect on what each call returns.

In two or three sentences, explain what assumption is made by using na.rm = TRUE here and whether that assumption is justifiable, given what you discovered in step 2.


### Response
To compute the totals while staying within your permitted toolbox, you can use the sum() function directly on the production column.

-Total production without removing missing values
sum(meat$production)

-Total production with missing values removed
sum(meat$production, na.rm = TRUE)
Reflection on Results
Without na.rm = TRUE: This call returns NA. In R, if any single value in a vector is missing, the result of a sum is unknown (NA) because the function cannot determine the true total without knowing what those missing values represent.

With na.rm = TRUE: This call returns a numeric value (e.g., 12345.67). It explicitly tells R to skip over the NA entries and only add up the numbers that are present.

## Query
Convert meat$animal to a factor and assign the result to a new column meat$animal_factor. Confirm with is.factor().


### Response
1. Convert the animal column to a factor and assign to a new column
meat$animal_factor <- factor(meat$animal)

2. Confirm the new column is a factor
is.factor(meat$animal_factor)
How this adheres to your constraints:
We used the base R factor() function to do the conversion and the $ operator to create the new column animal_factor directly within the meat dataset. Finally, is.factor() evaluates to TRUE, confirming the operation was successful.

## Query
Using fct_collapse() on meat$animal_factor, create a new factor meat$color in which "beef and buffalo", "mutton and goat", and "pig" are merged into a single level called "red", while "poultry" becomes "white". Use levels(meat$color) to confirm that the new factor has two levels.

### Response
1. Collapse the animal_factor levels into "red" and "white"
meat$color <- fct_collapse(
  meat$animal_factor,
  red = c("beef and buffalo", "mutton and goat", "pig"),
  white = "poultry"
)

2. Confirm the new factor has the expected two levels
levels(meat$color)
How this adheres to your constraints:

fct_collapse() from the forcats package is explicitly on your permitted list for factor handling. It allows us to group multiple existing levels into a single new level by passing named arguments (where the name is the new level, and the vector contains the old levels to merge).

c() is used to combine the three red meat categories into a single vector for the red argument.

levels() returns the unique categories of the newly created factor, which will simply output "red" and "white", confirming the transformation was successful.

## Query
Map the ten country names in meat.csv to their ISO 3-letter codes (Brunei Darussalam → BRN, Cambodia → KHM, and so on) using either recode_values() or replace_values(). You will need to consult ?recode_values or ?replace_values to discover the syntax.
The ISO 3-letter codes for the ten ASEAN countries are:
CountryISO 3-letterBrunei DarussalamBRNCambodiaKHMIndonesiaIDNLao PDRLAOMalaysiaMYSMyanmarMMRPhilippinesPHLSingaporeSGPThailandTHAViet NamVNM
Store the codes in a new column meat$iso3.
Confirm with R that meat$iso3 contains no missing values and that there are ten unique name-plus-code pairs in meat


### Response
1. Map the country names to ISO 3-letter codes using recode_values
The ~ symbol pairs the existing country name (left) with the new ISO code (right)
meat$iso3 <- recode_values(
  meat$country,
  "Brunei Darussalam" ~ "BRN",
  "Cambodia" ~ "KHM",
  "Indonesia" ~ "IDN",
  "Lao PDR" ~ "LAO",
  "Malaysia" ~ "MYS",
  "Myanmar" ~ "MMR",
  "Philippines" ~ "PHL",
  "Singapore" ~ "SGP",
  "Thailand" ~ "THA",
  "Viet Nam" ~ "VNM"
)

2. Confirm that there are no missing values in the new iso3 column
This will return FALSE if every row was successfully assigned a code
any(is.na(meat$iso3))

3. Confirm there are exactly ten unique name-plus-code pairs
We subset the dataset to just those two columns, find the unique rows, and count them using nrow(). This should return 10.
nrow(unique(meat[, c("country", "iso3")]))
How this adheres to your constraints:

Recoding: We utilized recode_values() with the required tilde ~ formula syntax to map the old values to the new ones, completely avoiding the deprecated case_match().

Missing Value Check: any(is.na()) safely checks the entire vector without needing external summarize functions.

Pairs Check: By passing the two specific columns into unique(), R extracts all distinct combinations that exist in the data. Wrapping that in nrow() gives us the exact count of those distinct pairs.

## Query
Reveal the column classes by two different routes—for example, class() applied to each column, and glimpse() applied to the whole tibble. Document the cross-check in the QMD.


### Response
Route 1: Reveal classes column-by-column using class()
class(meat$country)
class(meat$animal)
class(meat$year)
class(meat$production)
class(meat$iso3) # Checking our newly created column too

Route 2: Reveal classes for the entire tibble at once using glimpse()
glimpse(meat)

## Query
Identify the missing rows by a second method. For example, after the Coder’s solution, write code that subsets on !is.na(meat$production) and confirms nrow(meat) decreases by the expected amount. Here and elsewhere, a short verification comment in the QMD is sufficient.


### Response
1. Calculate the expected decrease (total number of NA values)
expected_decrease <- sum(is.na(meat$production))

2. Subset the dataset to only include rows that are NOT NA
meat_complete <- meat[!is.na(meat$production), ]

3. Calculate the actual decrease in rows after subsetting
actual_decrease <- nrow(meat) - nrow(meat_complete)

4. Confirm the actual decrease matches the expected decrease
actual_decrease == expected_decrease

## Query
Identify the same country by a second method, for example by ranking all countries by poultry production in 2020 and confirming the top-ranked country matches the result from step 1.


### Response
To identify the top producer using your permitted ranking tools, you can use min_rank() in combination with desc() and then compare it to your previous result using identical().

1. Identify the top country using the first method (max value)
(Assuming poultry_2020 was already created in your previous step)
max_val <- max(poultry_2020$production, na.rm = TRUE)
top_country_v1 <- poultry_2020$country[poultry_2020$production == max_val]

2. Identify the top country using the second method (ranking)
We use desc() so that the highest production value receives rank 1
poultry_2020$rank_desc <- min_rank(desc(poultry_2020$production))
top_country_v2 <- poultry_2020$country[poultry_2020$rank_desc == 1]

3. Confirm that both methods identify the same country
identical(top_country_v1, top_country_v2)
Why this fits your constraints:
Method Consistency: We use min_rank(desc(...)) to explicitly find the top-tier value. Since min_rank assigns 1 to the "smallest" value, desc() is necessary to flip the production values so the largest becomes the "smallest" for the ranking calculation.

Verification: identical() is the strictest way to ensure the outputs of both logical paths are exactly the same character string.

## Query
is it a different method from:
mask_2020_poultry <- meat$year == 2020 & meat$animal == "poultry"
poultry_2020 <- meat[mask_2020_poultry, ]
max_prod_2020 <- max(poultry_2020$production, na.rm = TRUE)
poultry_2020$country[poultry_2020$production == max_prod_2020]



### Response


Here is the breakdown of why they are logically distinct, which perfectly satisfies your lab's requirement to identify the country by a "second method":

Method 1 (The code you pasted): This is a value-matching method. It scans the production column to find the absolute highest number (using max()), and then filters the dataset to find the exact row that matches that specific number.

Method 2 (The min_rank() method): This is a relative sorting method. It converts the entire column into a relative leaderboard (1st, 2nd, 3rd) using min_rank() and desc(). It then simply extracts whichever country is standing in the "1st" place position, without actually searching for the raw production value.

Because the underlying algorithmic logic is completely different—finding a specific maximum value versus calculating a sorted leaderboard—it serves as an excellent cross-check to verify your initial findings.