- 👋 Hi, I’m @Pernreiter
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
Pernreiter/Pernreiter is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
import tkinter as tk
import RPi.GPIO as GPIO
import time
from picamera2 import Picamera2

# GPIO-Setup
GPIO.setmode(GPIO.BCM)
GPIO.setwarnings(False)

# GPIO-Pins definieren
PUMP_PIN = 17     # Pin für die Pumpe
MIXER_PIN = 27    # Pin für den Rührer
VALVE_PIN = 22    # Pin für das Ventil

# GPIO-Pins konfigurieren
GPIO.setup(PUMP_PIN, GPIO.OUT)
GPIO.setup(MIXER_PIN, GPIO.OUT)
GPIO.setup(VALVE_PIN, GPIO.OUT)

# Funktion: Pumpe ein-/ausschalten
def toggle_pump():
    current_state = GPIO.input(PUMP_PIN)
    GPIO.output(PUMP_PIN, not current_state)
    pump_button.config(text="Pumpe AUS" if not current_state else "Pumpe AN")

# Funktion: Rührer starten
def toggle_mixer():
    current_state = GPIO.input(MIXER_PIN)
    GPIO.output(MIXER_PIN, not current_state)
    mixer_button.config(text="Rührer AUS" if not current_state else "Rührer AN")

# Funktion: Ventil öffnen
def open_valve():
    GPIO.output(VALVE_PIN, GPIO.HIGH)
    time.sleep(5)  # Ventil für 5 Sekunden öffnen
    GPIO.output(VALVE_PIN, GPIO.LOW)

# Funktion: Bild mit Kamera aufnehmen
def capture_image():
    camera = Picamera2()
    camera.start()
    time.sleep(2)  # Kamera aufwärmen lassen
    camera.capture_file("/home/pi/schlammvolumen.jpg")
    camera.stop()
    status_label.config(text="Bild aufgenommen!")

# GUI erstellen
root = tk.Tk()
root.title("Steuerung für Ventil, Pumpe und Rührer")

# Pumpen-Knopf
pump_button = tk.Button(root, text="Pumpe AN", command=toggle_pump, width=20, height=2)
pump_button.pack(pady=10)

# Rührer-Knopf
mixer_button = tk.Button(root, text="Rührer AN", command=toggle_mixer, width=20, height=2)
mixer_button.pack(pady=10)

# Ventil-Knopf
valve_button = tk.Button(root, text="Ventil öffnen", command=open_valve, width=20, height=2)
valve_button.pack(pady=10)

# Kamera-Knopf
camera_button = tk.Button(root, text="Bild aufnehmen", command=capture_image, width=20, height=2)
camera_button.pack(pady=10)

# Status-Anzeige
status_label = tk.Label(root, text="", fg="green", font=("Helvetica", 12))
status_label.pack(pady=10)

# GUI starten
try:
    root.mainloop()
finally:
    GPIO.cleanup()
    
