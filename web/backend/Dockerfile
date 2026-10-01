FROM python:3.13-slim

ENV PYTHONUNBUFFERED=1
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends \
    libxcb1 \
    libxext6 \
    libsm6 \
    libxrender1 \
    libglib2.0-0 \
    libgomp1 \
    ffmpeg \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Pré-télécharge le modèle d'embeddings DANS l'image Docker elle-même (pas
# dans un conteneur éphémère) : évite un échec de connexion réseau à
# Hugging Face à chaque démarrage du serveur en production. Voir le
# commentaire dans agents/agentmanager/rag.py qui exige explicitement
# cette étape.
RUN python3 -c "from sentence_transformers import SentenceTransformer; SentenceTransformer('sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2')"

RUN playwright install --with-deps chromium

COPY . .

EXPOSE 8000

COPY start.sh .
RUN chmod +x start.sh
CMD ["./start.sh"]