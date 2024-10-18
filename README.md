/transcription-app
│
├── /static
│     └── style.css         # Optional CSS for styling
├── /templates
│     └── index.html        # HTML frontend
├── app.py                  # Flask backend
├── requirements.txt        # List of dependencies
├── Procfile                # Start command for Render
└── .gitignore              # Optional git ignore file
pip freeze > requirements.txt
Flask==2.3.2
librosa==0.9.2
mido==1.2.10
numpy==1.24.3
gunicorn==21.2.0
git add requirements.txt
git commit -m "Add requirements.txt"
git push origin main
