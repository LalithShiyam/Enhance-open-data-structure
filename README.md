# 🌐 ENHANCE: Total-Body PET/CT Dataset Standardization

At MedUniWien, we've taken steps to standardize our Total-Body PET/CT datasets. In our efforts to contribute to the open-source initiative, we present this as a proposed standard. It's designed to ensure easy navigation, consistent representation, and programmable data retrieval.

## 📂 Directory Structure

```
CohortName/
├── metadata.json
├── sub001/
│   ├── scan01/
│   │   ├── PET/
│   │   │   ├── static/
│   │   │   ├── dynamic/
│   │   │   ├── listmode/
│   │   │   ├── parametric/
│   │   ├── ACCT/
│   ├── scan02/
│   │   ├── PET/
│   │   │   ├── static/
│   │   │   ├── dynamic/
│   │   │   ├── listmode/
│   │   │   ├── parametric/
│   │   ├── ACCT/
├── sub002/
│   ├── scan01/
│   │   ├── PET/
│   │   │   ├── static/
│   │   │   ├── dynamic/
│   │   │   ├── listmode/
│   │   │   ├── parametric/
│   │   ├── ACCT/
│   ├── scan02/
│   │   ├── PET/
│   │   │   ├── static/
│   │   │   ├── dynamic/
│   │   │   ├── listmode/
│   │   │   ├── parametric/
│   │   ├── ACCT/
```

## 🗝 Key for Directories

- **PET**: Positron Emission Tomography
- **ACCT**: Computed Tomography - Attenuation Correction
- **static**: Static PET data
- **dynamic**: Dynamic PET data
- **listmode**: List-mode PET data
- **parametric**: Parametric PET images

## 🔍 `metadata.json` Structure

The `metadata.json` file serves as a comprehensive overview of the dataset for each cohort:

```json
{
    "cohort": "LungCancerPatients",
    "description": "A collection of PET/CT datasets for lung cancer patients.",
    "directory_key": {
        "PET": "Positron Emission Tomography",
        "ACCT": "Computed Tomography - Attenuation Correction",
        "static": "Static PET data",
        "dynamic": "Dynamic PET data",
        "listmode": "List-mode PET data",
        "parametric": "Parametric PET images"
    },
    "expected_datasets": ["static", "dynamic", "listmode", "parametric", "ACCT"],
    "subjects": [
        {
            "id": "sub001",
            "scans": [
                {
                    "scan_id": "scan01",
                    "datasets": {
                        "static": true,
                        "dynamic": true,
                        "listmode": true,
                        "parametric": true,
                        "ACCT": true
                    }
                },
                {
                    "scan_id": "scan02",
                    "datasets": {
                        "static": true,
                        "dynamic": true,
                        "listmode": true,
                        "parametric": true,
                        "ACCT": true
                    }
                }
            ]
        },
        {
            "id": "sub002",
            "scans": [
                {
                    "scan_id": "scan01",
                    "datasets": {
                        "static": true,
                        "dynamic": true,
                        "listmode": true,
                        "parametric": true,
                        "ACCT": true
                    }
                },
                {
                    "scan_id": "scan02",
                    "datasets": {
                        "static": true,
                        "dynamic": false,
                        "listmode": false,
                        "parametric": true,
                        "ACCT": true
                    }
                }
            ]
        }
    ]
}
```

## 📌 Generating `metadata.json` from Folder Structure

Before diving into generating `metadata.json`, there are naming conventions to be followed for subjects and their scans:

- **Subjects**: Each subject should have a name in the format `subXXXX` where `XXXX` is a unique identifier for the subject. For example: `sub0012`, `sub1204`.
  
- **Scans**: Each longitudinal scan for a subject should be named as `scanXXX` where `XXX` is the identifier for that scan. For example: `scan001`, `scan032`.

Adhering to these conventions ensures accurate and consistent metadata generation. We provide a convenient Python script to generate `metadata.json` directly from your dataset's root folder. It works as long as the folder conventions we've described are adhered to.

### 🔧 Prerequisites

Make sure you have Python installed on your machine.

### 🚀 How to use

1. Copy the script below:

```python
import os
import json

def generate_metadata(root_folder):
    metadata = {
        "cohort": os.path.basename(root_folder),
        "description": f"A collection of PET/CT datasets for {os.path.basename(root_folder)}.",
        "directory_key": {
            "PET": "Positron Emission Tomography",
            "ACCT": "Computed Tomography - Attenuation Correction",
            "static": "Static PET data",
            "dynamic": "Dynamic PET data",
            "listmode": "List-mode PET data",
            "parametric": "Parametric PET images"
        },
        "expected_datasets": ["static", "dynamic", "listmode", "parametric", "ACCT"],
        "subjects": []
    }

    for subject in sorted(os.listdir(root_folder)):
        subject_path = os.path.join(root_folder, subject)
        if os.path.isdir(subject_path) and "sub" in subject:
            subject_data = {
                "id": subject,
                "scans": []
            }
            for scan in sorted(os.listdir(subject_path)):
                scan_path = os.path.join(subject_path, scan)
                if os.path.isdir(scan_path) and "scan" in scan:
                    scan_data = {
                        "scan_id": scan,
                        "datasets": {
                            "static": os.path.exists(os.path.join(scan_path, "PET", "static")),
                            "dynamic": os.path.exists(os.path.join(scan_path, "PET", "dynamic")),
                            "listmode": os.path.exists(os.path.join(scan_path, "PET", "listmode")),
                            "parametric": os.path.exists(os.path.join(scan_path, "PET", "parametric")),
                            "ACCT": os.path.exists(os.path.join(scan_path, "ACCT"))
                        }
                    }
                    subject_data["scans"].append(scan_data)
            metadata["subjects"].append(subject_data)

    with open(os.path.join(root_folder, 'metadata.json'), 'w') as f:
        json.dump(metadata, f, indent=4)

    print(f"metadata.json generated in {root_folder}")

# To execute the script, just call the generate_metadata function with your root folder path.
# For example: generate_metadata('/path/to/your/CohortName')
```

2. Save the script in a `.py` file, e.g., `generate_metadata.py`.
3. Run the script and provide the root folder path. 

Example:

```bash
python generate_metadata.py
```

4. After execution, `metadata.json` will be generated in the provided root folder.
