# Data-Cleaning-and-Structural-Validation.-
# Data Cleaning and Structural Validation using Pandas

import pandas as pd

# ---------------------------------------------------------
# STEP 1: Load the dataset
# ---------------------------------------------------------

# Change this filename to your dataset filename
file_name = "messy_dataset.csv"

df = pd.read_csv(file_name)

print("========== ORIGINAL DATA ==========")
print(df)

print("\n========== DATA INFORMATION ==========")
print(df.info())


# ---------------------------------------------------------
# STEP 2: Inspect missing values
# ---------------------------------------------------------

print("\n========== MISSING VALUES ==========")
print(df.isnull().sum())


# ---------------------------------------------------------
# STEP 3: Check duplicate records
# ---------------------------------------------------------

print("\n========== DUPLICATE RECORDS ==========")

duplicate_count = df.duplicated().sum()

print("Number of duplicate records:", duplicate_count)

if duplicate_count > 0:
    print("\nDuplicate rows:")
    print(df[df.duplicated()])

    # Remove duplicate records
    df = df.drop_duplicates()

    print("\nDuplicates removed successfully.")
else:
    print("No duplicate records found.")


# ---------------------------------------------------------
# STEP 4: Standardize column names
# ---------------------------------------------------------

print("\n========== STANDARDIZING COLUMN NAMES ==========")

# Remove extra spaces
df.columns = df.columns.str.strip()

# Convert column names to lowercase
df.columns = df.columns.str.lower()

# Replace spaces with underscore
df.columns = df.columns.str.replace(" ", "_")

# Remove unwanted special characters
df.columns = df.columns.str.replace(r"[^a-zA-Z0-9_]", "", regex=True)

print("New column names:")
print(df.columns.tolist())


# ---------------------------------------------------------
# STEP 5: Handle missing values
# ---------------------------------------------------------

print("\n========== HANDLING MISSING VALUES ==========")

# Fill missing numerical values with median
numeric_columns = df.select_dtypes(include=["int64", "float64"]).columns

for column in numeric_columns:
    if df[column].isnull().sum() > 0:
        df[column] = df[column].fillna(df[column].median())
        print("Filled missing values in", column, "with median.")


# Fill missing categorical values with mode
categorical_columns = df.select_dtypes(include=["object"]).columns

for column in categorical_columns:
    if df[column].isnull().sum() > 0:
        mode_value = df[column].mode()

        if len(mode_value) > 0:
            df[column] = df[column].fillna(mode_value[0])
            print("Filled missing values in", column, "with mode.")


# ---------------------------------------------------------
# STEP 6: Remove extra spaces from text columns
# ---------------------------------------------------------

print("\n========== CLEANING TEXT VALUES ==========")

for column in df.select_dtypes(include=["object"]).columns:
    df[column] = df[column].astype(str).str.strip()


# ---------------------------------------------------------
# STEP 7: Standardize categorical strings
# ---------------------------------------------------------

# Example:
# "Male", "male", "MALE" -> "Male"
# "Female", "female", "FEMALE" -> "Female"

for column in df.select_dtypes(include=["object"]).columns:

    # Convert text to lowercase temporarily
    df[column] = df[column].str.lower()

    # Convert first letter to uppercase
    df[column] = df[column].str.title()


# ---------------------------------------------------------
# STEP 8: Convert date columns into proper date format
# ---------------------------------------------------------

# If your dataset contains a date column, change the name here.
# Example: date, dob, joining_date, purchase_date, etc.

date_columns = []

for column in date_columns:
    df[column] = pd.to_datetime(
        df[column],
        errors="coerce"
    )

print("\nDate columns converted successfully.")


# ---------------------------------------------------------
# STEP 9: Validate data types
# ---------------------------------------------------------

print("\n========== DATA TYPES AFTER CLEANING ==========")

print(df.dtypes)


# ---------------------------------------------------------
# STEP 10: Check missing values again
# ---------------------------------------------------------

print("\n========== MISSING VALUES AFTER CLEANING ==========")

print(df.isnull().sum())


# ---------------------------------------------------------
# STEP 11: Check duplicates again
# ---------------------------------------------------------

print("\n========== DUPLICATES AFTER CLEANING ==========")

print("Duplicate records:", df.duplicated().sum())


# ---------------------------------------------------------
# STEP 12: Display cleaned dataset
# ---------------------------------------------------------

print("\n========== CLEANED DATA ==========")

print(df)


# ---------------------------------------------------------
# STEP 13: Export cleaned dataset to CSV
# ---------------------------------------------------------

output_file = "cleaned_dataset.csv"

df.to_csv(output_file, index=False)

print("\n======================================")
print("Data cleaning completed successfully!")
print("Cleaned file saved as:", output_file)
print("======================================")


# ---------------------------------------------------------
# STEP 14: Export to Excel (Optional)
# ---------------------------------------------------------

excel_file = "cleaned_dataset.xlsx"

df.to_excel(excel_file, index=False)

print("Excel file saved as:", excel_file)