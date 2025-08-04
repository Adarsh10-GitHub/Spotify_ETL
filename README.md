# 🎧 Spotify Playlist ETL & Analysis

This project delivers an end-to-end **Extract, Transform, Load (ETL)** pipeline implemented in **PySpark** on **Databricks Serverless Compute**. It extracts track and album metadata from a specified Spotify playlist, enriches it with artist and album details, performs analytical transformations, and loads the results into **Delta Lake** tables for querying and insights.

---

## ✨ Features

- **Data Extraction**:  
  Retrieves detailed track and album information from Spotify playlists using the [Spotify Web API](https://developer.spotify.com/documentation/web-api) via the `spotipy` library.

- **Data Enrichment**:  
  - Artist genres, popularity, and follower counts  
  - Album types (`album`, `single`, `compilation`) and popularity  
  - Explicit content flags  
  - Temporal features: `ReleaseYear`, `ReleaseMonth`, `ReleaseDayOfWeek`, `ReleaseDecade`

- **Data Transformation**:  
  Converts nested JSON to tabular Spark DataFrames.

- **Data Loading**:  
  Stores the final data into **Delta Lake** tables inside a dedicated `spotify` database.

- **Analytical Insights**:  
  - Longest albums by track duration  
  - Album categorization by track/artist popularity  
  - Genre distribution analysis  
  - Track recommendations based on popularity

---

## 🚀 Technologies Used

- **Apache Spark (PySpark)** – Distributed processing engine  
- **Databricks Serverless Compute** – Scalable, managed Spark environment  
- **Spotipy** – Python client for the Spotify Web API  
- **Spotify Web API** – Music metadata provider  
- **Delta Lake** – ACID-compliant storage layer for Spark  
- **SQL** – Querying and schema definitions  
- **Databricks Secrets** – Secure credential storage

---

## 📂 Project Structure

- **Initialization & Configuration**: Spark setup, Spotipy config, credentials  
- **Helper Functions**: API batching, pagination  
- **Core Extraction**: `process_playlists`, `get_tracks`, `get_albums`  
- **Enrichment Functions**: `get_artist_details_for_enrichment`, `get_album_details_for_enrichment`  
- **ETL Orchestration**: Full pipeline logic  
- **SQL Table Definitions**: Delta table schemas  
- **Analytical Queries**: PySpark SQL & DataFrame transformations  
- **Result Storage**: Final tables with insights

---

## ⚙️ Setup & Configuration

### 1. Spotify Developer Account
- Sign up at [Spotify Developer Dashboard](https://developer.spotify.com/dashboard)
- Create a new app, and note your **Client ID** and **Client Secret**
- Optional Redirect URI: `http://localhost:8888/callback`

### 2. Databricks Workspace
- Use a workspace with **Serverless Compute** enabled

### 3. Store Credentials Securely
<pre><code>
# Create secret scope
databricks secrets create-scope --scope Spotify_auth_details

# Store Client ID
databricks secrets put --scope Spotify_auth_details --key client_id

# Store Client Secret
databricks secrets put --scope Spotify_auth_details --key client_secret
</code></pre>

### 4. Install Spotipy
```python
%pip install spotipy
```

---

## 🚀 Usage

1. **Import Notebook**: Upload to your Databricks workspace  
2. **Attach Cluster**: Use a Serverless Compute instance  
3. **Set Playlist ID**:
```python
dbutils.widgets.text("playlist_id", "")
```
4. Paste the playlist ID from a Spotify URL like:  
   `https://open.spotify.com/playlist/<PLAYLIST_ID>?si=...`

5. **Run All Cells**: Execute all cells sequentially

The pipeline will:
- Extract playlist data  
- Enrich with metadata  
- Load enriched data to `spotify.full_albums_enriched`  
- Save analytical results to tables like:
  - `spotify.top_5_longest_albums`
  - `spotify.tracks_per_genre`
  - `spotify.recommended_tracks`

---

## 📊 Data Model

### `spotify.full_albums`

| Column Name     | Type         | Description                          |
|-----------------|--------------|--------------------------------------|
| ArtistName      | STRING       | Name of the primary artist           |
| AlbumName       | STRING       | Name of the album                    |
| ReleaseDate     | DATE         | Release date of the album            |
| TrackId         | STRING       | Spotify track ID                     |
| TrackName       | STRING       | Track name                           |
| DurationSeconds | DECIMAL(8,2) | Track duration in seconds            |
| Duration        | STRING       | Formatted duration (mm:ss)           |
| Popularity      | INT          | Popularity score (0–100)             |
| TrackNumber     | INT          | Track’s position in the album        |

### `spotify.full_albums_enriched`

| Column Name         | Type           | Description                              |
|---------------------|----------------|------------------------------------------|
| ArtistName          | STRING         | Primary artist name                      |
| AlbumName           | STRING         | Album name                               |
| ReleaseDate         | DATE           | Album release date                       |
| TrackId             | STRING         | Spotify track ID                         |
| TrackName           | STRING         | Track name                               |
| DurationSeconds     | DECIMAL(8,2)   | Duration in seconds                      |
| Duration            | STRING         | mm:ss format                             |
| Popularity          | INT            | Track popularity                         |
| TrackNumber         | INT            | Album position                           |
| Explicit            | BOOLEAN        | Explicit flag                            |
| ArtistGenres        | ARRAY<STRING>  | Genres associated with the artist        |
| ArtistPopularity    | INT            | Artist popularity                        |
| ArtistFollowers     | BIGINT         | Artist follower count                    |
| AlbumType           | STRING         | Album type (album, single, compilation)  |
| AlbumPopularity     | INT            | Album popularity                         |
| ReleaseYear         | INT            | Year of release                          |
| ReleaseMonth        | INT            | Month of release                         |
| ReleaseDayOfWeek    | INT            | Day of week (1 = Sunday)                 |
| ReleaseDecade       | INT            | Decade (e.g., 1990, 2000)                |

---

## 📈 Analysis & Insights

- **Longest Albums** – Albums with maximum playback duration  
- **Track Popularity** – Popular vs. non-popular track count  
- **Artist Influence** – Albums grouped by artist popularity tiers  
- **Genre Trends** – Dominant genres in the playlist  
- **Recommendations** – Tracks based on popular song features

---

## 🔮 Future Enhancements

- **Error Handling & Logging** – Improve resilience and traceability  
- **Incremental Loads** – Support partial updates  
- **Advanced Analytics**:
  - Clustering by features
  - Time-series trends
  - Sentiment analysis (via NLP)  
- **Visualizations** – Databricks or BI dashboards  
- **UI Layer** – Web app for playlist input and results

---

## 📄 License

This project is licensed under the **MIT License** – see the [LICENSE](LICENSE) file.

---


## 🙏 Acknowledgements

- [Spotify Web API](https://developer.spotify.com/documentation/web-api/)  
- [Spotipy](https://spotipy.readthedocs.io/)  
- [Apache Spark](https://spark.apache.org/) & [Databricks](https://databricks.com/)
