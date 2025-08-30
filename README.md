<h2 align="center">
  Spotify ETL Data Pipeline on AWS <br/>
  <a href="https://github.com/prasanna-chintamaneni" target="_blank">By Naga Prasanna Chintamaneni</a>
</h2>

<div align="center">
  <img alt="AWS ETL Pipeline" src="D:\prasanna\Data Engineering Prep\Spotify-ETL-Data-Pipeline\Project Archicture.webpitecture.png">
</div>

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
git clone https://github.com/prasanna-chintamaneni/spotify-etl-pipeline.git
cd spotify-etl-pipeline

###2️⃣ Create & Activate Virtual Environment
python -m venv venv
venv\Scripts\activate   # For Windows
source venv/bin/activate  # For Mac/Linux

###3️⃣ Install Dependencies (for local testing)
pip install spotipy boto3

###4️⃣ Prepare Lambda Layer (for Spotipy)
Since AWS Lambda does not include Spotipy by default, create a Lambda Layer:

mkdir python
pip install spotipy -t ./python
zip -r spotipy_layer.zip python

Upload spotipy_layer.zip in AWS Console → Lambda → Layers → Create Layer.
Attach this layer to your Lambda function.

###5️⃣ Configure AWS Credentials
Ensure IAM role has permissions for S3, Lambda, Glue, Athena.

###6️⃣ Deploy Lambda Functions
Extract Lambda: Fetches data from Spotify API and uploads to raw_data/to_process/ in S3.
Transform Lambda: Cleans and restructures the raw data into albums, tracks, and artists, then stores in raw_data/processed/.

###7️⃣ Set Up Automation
Create an EventBridge Rule to trigger the Extract Lambda periodically (e.g., every 1 hour/day).
Configure S3 Trigger for the Transform Lambda whenever new raw data is uploaded.

###8️⃣ Data Querying
Use AWS Glue Crawler to scan the processed data and update the catalog.
Query datasets using Amazon Athena with SQL.

**📊 Example Athena Query**
SELECT artist_name, COUNT(*) as track_count
FROM "spotify_database"."album_data"
GROUP BY artist_name
ORDER BY track_count DESC
LIMIT 10;

**✨ Features**

Automated Data Extraction using EventBridge.

Serverless ETL Pipeline with AWS Lambda.

Data Lake Architecture using S3 (raw + processed layers).

Schema Discovery & Querying with Glue & Athena.

Scalable & Cost-Effective since it uses serverless components.

**🏆 Challenges & Solutions**

Challenge: AWS Lambda does not include external libraries like Spotipy.

Solution: Created a Lambda Layer with Spotipy dependency.

Challenge: Automating daily extraction from Spotify API.

Solution: Used EventBridge to schedule Lambda triggers.

Challenge: Converting messy Spotify JSON into structured data.

Solution: Wrote a transformation function to split data into Albums, Artists, and Tracks.

##📂 Project Structure
```bash
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
