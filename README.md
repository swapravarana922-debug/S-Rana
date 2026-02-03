import speech_recognition as sr
import pyttsx3
import json
import os
from collections import deque

# -----------------------
# 1. Voice Engine Setup
# -----------------------
engine = pyttsx3.init()
engine.setProperty('rate', 180)  # speaking rate
engine.setProperty('volume', 1.0)

def speak(text):
    engine.say(text)
    engine.runAndWait()

# -----------------------
# 2. Context Memory Setup
# -----------------------
# Stores last 350 interactions
context_memory = deque(maxlen=350)

# -----------------------
# 3. Command Processing
# -----------------------
def process_command(command):
    command = command.lower()
    response = "I did not understand that."
    
    if "email" in command:
        response = "Fetching your latest emails..."
    elif "alarm" in command:
        response = "Setting your alarm now."
    elif "read" in command or "pdf" in command:
        response = "Reading the document aloud..."
    elif "summary" in command:
        response = "Summarizing the content..."
    # Add more commands here
    
    # Store interaction
    context_memory.append({"command": command, "response": response})
    
    return response

# -----------------------
# 4. Voice Input Capture
# -----------------------
def listen():
    recognizer = sr.Recognizer()
    with sr.Microphone() as source:
        print("ES is listening...")
        recognizer.adjust_for_ambient_noise(source)
        audio = recognizer.listen(source)
        
        try:
            command = recognizer.recognize_google(audio)
            print(f"You said: {command}")
            return command
        except sr.UnknownValueError:
            return "Sorry, I didn't catch that."
        except sr.RequestError:
            return "Speech service unavailable."

# -----------------------
# 5. Main Loop
# -----------------------
def main():
    speak("Hello! I am ES, your Essential Systems AI.")
    while True:
        command = listen()
        if "exit" in command.lower():
            speak("Goodbye!")
            break
        response = process_command(command)
        speak(response)

# Run ES
if __name__ == "__main__":
    main()
