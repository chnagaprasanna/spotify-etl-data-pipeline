<h2 align="center">
  Spotify ETL Data Pipeline on AWS <br/>
  <a href="https://github.com/prasanna-chintamaneni" target="_blank">By Naga Prasanna Chintamaneni</a>
</h2>

<p align="center">
  <img src="https://github.com/user-attachments/assets/c9b06b42-27f1-4dd6-98d8-4bd1a7eb808e" alt="AWS ETL Pipeline" width="100%"/>
</p>

<br/>

## 📌 Project Overview  

In this project, I have built an **ETL (Extract, Transform, Load) pipeline** using the **Spotify API** on **AWS**.  
The pipeline automatically retrieves playlist data from Spotify, transforms it into a structured format, and loads it into AWS services for querying and analysis.  

The pipeline leverages:  
- **AWS Lambda** for serverless processing  
- **S3** as the data lake  
- **EventBridge** for scheduling automation  
- **AWS Glue** for cataloging data  
- **Athena** for running SQL queries on the processed dataset  

---

## 🚀 Built With  

This project was built using the following technologies:  

- **Python 3.9**  
- **Spotify Web API (Spotipy)** *(installed via AWS Lambda Layer)*  
- **AWS Lambda**  
- **Amazon S3**  
- **Amazon EventBridge**  
- **AWS Glue** (Crawlers & Data Catalog)  
- **Amazon Athena**  

---

## ⚙️ Architecture  

**1. Extract**  
- Spotify playlist data is extracted using the **Spotipy** library.  
- Implemented inside a Lambda function with API credentials stored securely in environment variables.  
- **Lambda Layer** is used to include external Spotipy dependency.  

**2. Transform**  
- Raw JSON is read from the S3 bucket.  
- Transformation Lambda parses track, album, and artist data into clean structured JSON files.  

**3. Load**  
- Transformed data is stored back in a separate S3 prefix (`processed/`).  
- AWS Glue Crawler is triggered to update the Data Catalog.  
- Amazon Athena is used to query structured Spotify data with SQL.  

---

## 🛠 Installation and Setup Instructions  
To run this project locally or deploy it to AWS:  

### 1️⃣ Clone Repository
```bash

git clone https://github.com/prasanna-chintamaneni/spotify-etl-pipeline.git
cd spotify-etl-pipeline
```
### 2️⃣ Create & Activate Virtual Environment
```bash

python -m venv venv
venv\Scripts\activate   # For Windows
source venv/bin/activate  # For Mac/Linux
```
### 3️⃣ Install Dependencies (for local testing)
```bash

pip install spotipy boto3
```
### 4️⃣ Prepare Lambda Layer (for Spotipy)

Since AWS Lambda does not include Spotipy by default, create a Lambda Layer:
```bash

mkdir python
pip install spotipy -t ./python
zip -r spotipy_layer.zip python
```
Upload spotipy_layer.zip in AWS Console → Lambda → Layers → Create Layer.
<p align="center">
<img width="700" height="414" alt="Screenshot 2025-08-31 031852" src="https://github.com/user-attachments/assets/8714cf57-9474-4ab7-9480-a54e3bb196c9" />
</p>
Attach this layer to your Lambda function.
<p align="center">
<img width="700" height="686" alt="Screenshot 2025-08-31 031807" src="https://github.com/user-attachments/assets/befd7d47-5fa9-493f-9e36-c2b4cc511b54" />
</p>
### 5️⃣ Configure AWS Credentials

Ensure IAM role has permissions for S3, Lambda, Glue, Athena.

### 6️⃣ Deploy Lambda Functions

Extract Lambda: Fetches data from Spotify API and uploads to raw_data/to_process/ in S3.
Transform Lambda: Cleans and restructures the raw data into albums, tracks, and artists, then stores in raw_data/processed/.
<p align="center">
<img width="800" height="941" alt="Screenshot 2025-08-31 021642" src="https://github.com/user-attachments/assets/35ec20d2-79ce-4a88-ad0e-7c48cea109b9" />
</p>

### 7️⃣ Set Up Automation

Create an EventBridge Rule to trigger the Extract Lambda periodically (e.g., every 1 hour/day).
<p align="center">
<img width="700" height="373" alt="Screenshot 2025-08-31 021910" src="https://github.com/user-attachments/assets/eafd7a9c-7880-4140-aab1-2f94b91ac2c4" />
</p>

Configure S3 Trigger for the Transform Lambda whenever new raw data is uploaded.
<p align="center">
<img width="700" height="441" alt="Screenshot 2025-08-31 021713" src="https://github.com/user-attachments/assets/4c5da734-82ed-43e7-b6c8-2241da7c81ff" />
</p>

### 8️⃣ Data Querying

Use AWS Glue Crawler to scan the processed data and update the catalog.
<p align="center">
<img width="700" height="504" alt="Screenshot 2025-08-31 021330" src="https://github.com/user-attachments/assets/4e086591-4e0f-4cf5-9c9d-3a0680c856e8" />
<img width="00" height="441" alt="Screenshot 2025-08-31 021442" src="https://github.com/user-attachments/assets/527a4385-797c-4cd8-8921-e16419e5b8bc" />

Query datasets using Amazon Athena with SQL.
<p align="center">
<img width="800" height="944" alt="Screenshot 2025-08-31 021558" src="https://github.com/user-attachments/assets/a8e6b614-65cc-4b11-8938-2c3fca7bf1c2" />


## 📊 Example Athena Query

```sql
SELECT artist_name, COUNT(*) AS track_count
FROM "spotify_database"."album_data"
GROUP BY artist_name
ORDER BY track_count DESC
LIMIT 10;
```

## ✨ Features

Automated Data Extraction using EventBridge.

Serverless ETL Pipeline with AWS Lambda.

Data Lake Architecture using S3 (raw + processed layers).

Schema Discovery & Querying with Glue & Athena.

Scalable & Cost-Effective since it uses serverless components.

## 🏆 Challenges & Solutions

Challenge: AWS Lambda does not include external libraries like Spotipy.

Solution: Created a Lambda Layer with Spotipy dependency.

Challenge: Automating daily extraction from Spotify API.

Solution: Used EventBridge to schedule Lambda triggers.

Challenge: Converting messy Spotify JSON into structured data.

Solution: Wrote a transformation function to split data into Albums, Artists, and Tracks.

## 📂 Project Structure
``` bash
spotify-etl-data-pipeline/
│
├── functions/                         # Lambda functions
│   ├── spotify-api-data-extraction/   # Extract function
│   └── spotify-transform/             # Transform & Load function
│
├── Layer/                             # Spotipy dependency packaged as Lambda Layer
│   └── spotipy_layer.zip
│
├── S3 Bucket (spotify-etl-project-np) # Data lake
│   ├── raw_data/
│   │   ├── to_process/                # Raw JSON data from Spotify API
│   │   └── processed/                 # Intermediate cleaned JSON
│   │
│   └── transformed_data/              # Final structured data
│       ├── albums/                    # Album-level data
│       ├── artists/                   # Artist-level data
│       └── songs/                     # Track-level data
│
└── README.md
