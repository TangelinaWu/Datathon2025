# Datathon2025

Vibe Search™
Created by Angelina Wu, Yuyang Hu, Hugo Lee, Emily 

A Retrieval-Augmented “vibe engine” that turns any song into any place recommendations.
**How it works **
**And What it does
**Song-in → Places-out
Type a song title (e.g. Laufey – From the Start) or a free-form mood phrase.
And we will match the vibes of the song, via its bpm, musicality and spit out a place

Multimodal embedding
Text model (MiniLM) converts the query to a 384-dim vector.

Pre-clustered catalogue
Every place (restaurant / store / activity) is embedded, then grouped with K-Means into 100 “vibe buckets”.

Nearest-cluster search
We pinpoint the closest centroids by cosine similarity and pull items from those clusters.

Never empty
If fewer than N matches are found the search automatically widens; as a last resort it falls back to random popular spots—so the user always sees results.

Optional Spotify boost
If a Spotify Client ID / Secret is provided we also fetch audio-features for deeper similarity.

Folder structure
css
Copy code
.
├── vibe_search.ipynb      ← one-cell Colab / Jupyter notebook (main demo)
├── src/
│   ├─ vibe_search.py      ← importable engine (same logic as notebook)
│   └─ utils.py            ← helpers for embeddings, Spotify, faiss, etc.
├── data/
│   ├─ places.csv
│   ├─ reviews.csv
│   └─ media.csv
└── README.md              ← you are here
Quick-start (Colab)
Open vibe_search.ipynb in Google Colab.

GPU runtime → Runtime ▸ Change runtime type ▸ GPU.

Run the first cell – it pins NumPy 1.26, installs Torch 2.6 + CUDA 12.4, Sentence-Transformers, Faiss, etc.

Upload places.csv, reviews.csv, media.csv when prompted.

Type a song / vibe at the console prompt → instantly get 10 matching places.

Local-dev (VS Code)
bash
Copy code
poetry install           # or pip install -r requirements.txt
export SPOTIPY_CLIENT_ID=xxx          # optional for Spotify audio-features
export SPOTIPY_CLIENT_SECRET=yyy
python -m src.vibe_search
The CLI will ask for a query and print JSON rows with name, text_blob, similarity.

Key Dependencies
Library	Why we use it
sentence-transformers	all-MiniLM-L6-v2 for fast 384-dim embeddings.
faiss-cpu / faiss-gpu	O(1) cosine retrieval once vectors are L2-normalised.
scikit-learn	K-Means for offline clustering.
spotipy (optional)	Pull audio_features for deeper song vectors.
Python ≥ 3.10, Torch 2.6 + CUDA 12.4 pinned for Colab/GPU consistency.

Extending the project
Idea	How
Audio embeddings	Swap text-only song vector for CLAP or MERT audio model.
Cross-encoder rerank	Use cross-encoder/ms-marco-MiniLM-L-6-v2 on top-50 hits.
Feedback fine-tune	Log user clicks, build triplets (query, positive, negative), fine-tune MiniLM monthly.
Bigger catalogue	Pull extra restaurants via Yelp/Google API; embed & assign to nearest centroid on-the-fly.
Security notes
We store Spotify credentials in environment variables (never hard-code).

Client Secret never leaves the backend; frontend passes only short-lived access tokens.

Random-sample fallback never exposes private data—just public places.

