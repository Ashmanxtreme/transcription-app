# transcription-app
/transcription-app
│
├── /static
│     └── style.css         # Optional CSS for styling
├── /templates
│     └── index.html        # HTML frontend
├── app.py                  # Flask backend
├── requirements.txt         # List of dependencies
├── Procfile                 # Start command for Render
└── .gitignore               # Optional git ignore file
pip freeze > requirements.txt
web: gunicorn app:app
__pycache__/
*.pyc
uploads/
.env
