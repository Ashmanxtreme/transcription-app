# transcription-app
from flask import Flask, render_template, request, send_file
import librosa
import mido
import numpy as np
import os

app = Flask(__name__)

# Define the upload folder
UPLOAD_FOLDER = 'uploads'
os.makedirs(UPLOAD_FOLDER, exist_ok=True)

@app.route('/', methods=['GET', 'POST'])
def index():
    if request.method == 'POST':
        # Get the uploaded file
        file = request.files['file']
        if file and file.filename.endswith('.wav'):
            filepath = os.path.join(UPLOAD_FOLDER, file.filename)
            file.save(filepath)

            # Process the audio file
            midi_file_path = process_audio_to_midi(filepath)

            return send_file(midi_file_path, as_attachment=True)

    return render_template('index.html')

def process_audio_to_midi(audio_file):
    # Load audio file
    y, sr = librosa.load(audio_file, sr=None)

    # Simple onset detection
    onset_frames = librosa.onset.onset_detect(y=y, sr=sr, backtrack=True)
    notes = librosa.hz_to_midi(librosa.pitch_tuning(y, sr))

    # Create a MIDI file
    midi_notes = []
    for onset in onset_frames:
        # Here you might want to map the note and its time to MIDI
        midi_note = int(np.round(notes[onset]))
        midi_notes.append((midi_note, onset * 480))  # Assuming 480 ticks per beat

    midi_file = create_midi(midi_notes)
    return midi_file

def create_midi(midi_notes):
    midi_file_path = os.path.join(UPLOAD_FOLDER, 'output.mid')
    midi = mido.MidiFile()

    # Create a MIDI track
    track = mido.MidiTrack()
    midi.tracks.append(track)

    # Write notes to the MIDI file
    for note, time in midi_notes:
        track.append(mido.Message('note_on', note=note, velocity=64, time=time))
        track.append(mido.Message('note_off', note=note, velocity=64, time=time + 240))  # Note duration

    midi.save(midi_file_path)
    return midi_file_path

if __name__ == '__main__':
    app.run(debug=True)
    <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Music Transcription</title>
</head>
<body>
    <h1>Upload a WAV file to transcribe to MIDI</h1>
    <form method="POST" enctype="multipart/form-data">
        <input type="file" name="file" accept=".wav" required>
        <input type="submit" value="Upload">
    </form>
</body>
</html>
Flask==2.3.2
librosa==0.9.2
mido==1.2.10
numpy==1.24.3
