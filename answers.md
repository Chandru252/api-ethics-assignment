# Task 1 — Classify and Handle PII Fields

| Field               | Type of PII    | Action                                   | Justification                                                                                                             |
| ------------------- | -------------- | ---------------------------------------- | ----------------------------------------------------------------------------------
| **full_name**       | Direct PII     | Drop / Pseudonymize                      | Full name directly identifies an individual. It should be removed or replaced                                                                                         with a unique ID to prevent identification. |
| **email**           | Direct PII     | Drop                                     | Email is a unique identifier and highly sensitive. It is not required for                                                                                             research purposes.                              |
| **date_of_birth**   | Indirect PII   | Mask (e.g., keep only year or age range) | Exact DOB can be used to re-identify individuals when combined with other data.                                           |
| **zip_code**        | Indirect PII   | Mask (e.g., first 3 digits only)         | Full ZIP codes can enable re-identification, especially in small populations.                                             |
| **job_title**       | Indirect PII   | Keep / Generalize                        | Not directly identifying, but can be generalized (e.g., “Healthcare Worker”) to                                                                                       reduce risk.                              |
| **diagnosis_notes** | Sensitive Data | Pseudonymize / Clean                     | Contains sensitive health info. Remove any personal references and ensure                                                                                             anonymization before sharing.                   |


# Task 2 — Audit the API Script for Ethical Compliance

import os
import requests

API_URL = "https://healthstats-api.example.com/records"
API_KEY = os.getenv("API_KEY")

records = []
for page in range(1, 101):
    response = requests.get(API_URL, params={"page": page, "key": API_KEY})
    data = response.json()
    records.extend(data["results"])

    records = []

for page in range(1, 101):
    response = requests.get(API_URL, params={"page": page, "key": API_KEY})
    
    if response.status_code != 200:
        break  # Stop if request fails

    data = response.json()

    for record in data["results"]:
        cleaned_record = {
            "age": calculate_age(record["date_of_birth"]),
            "zip_prefix": record["zip_code"][:3],
            "job_title": record["job_title"],
            "diagnosis_notes": clean_text(record["diagnosis_notes"])
        }
        records.append(cleaned_record)

# Store only cleaned/anonymized data
save_to_database(records)
