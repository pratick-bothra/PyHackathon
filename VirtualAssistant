import speech_recognition as sr
import pyttsx3
import webbrowser
import datetime
import os
import requests
import wikipedia
import smtplib
from googletrans import Translator
from fuzzywuzzy import process

# Initialize Text-to-Speech engine
engine = pyttsx3.init()
engine.setProperty("rate", 150)
engine.setProperty("volume", 1)

def speak(text):
    """Convert text to speech."""
    engine.say(text)
    engine.runAndWait()

def listen():
    """Capture voice input and convert it to text."""
    recognizer = sr.Recognizer()
    with sr.Microphone() as source:
        print("Listening...")
        recognizer.adjust_for_ambient_noise(source)
        audio = recognizer.listen(source)

    try:
        command = recognizer.recognize_google(audio).lower()
        print(f"You said: {command}")
        return command
    except sr.UnknownValueError:
        print("Sorry, I couldn't understand.")
        return None
    except sr.RequestError:
        print("Speech service unavailable.")
        return None

# Smart Home Controls
def control_device(device, state):
    speak(f"Turning {state} the {device}.")
    print(f"{device.capitalize()} turned {state}.")

# Get Weather
def get_weather(city="Bengaluru"):
    api_key = "7f9c3fb150230d2cef01999b9f4093e1"  # Replace with your API Key
    url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={api_key}&units=metric"
    response = requests.get(url).json()
    if response["cod"] == 200:
        temp = response["main"]["temp"]
        weather = response["weather"][0]["description"]
        speak(f"The temperature in {city} is {temp} degrees Celsius with {weather}.")
    else:
        speak("Sorry, I couldn't fetch the weather.")

# Google & Wikipedia Search
def search_web(query):
    speak(f"Searching for {query}")
    webbrowser.open(f"https://www.google.com/search?q={query}")

def search_wikipedia(query):
    try:
        summary = wikipedia.summary(query, sentences=2)
        speak(summary)
    except wikipedia.exceptions.DisambiguationError:
        speak("There are multiple results, please be more specific.")

# Play Music or Videos
def play_music():
    speak("Playing your favorite music.")
    os.system("start ms-windows-store://music")  # Windows Music Player

def play_youtube(query):
    speak(f"Playing {query} on YouTube.")
    webbrowser.open(f"https://www.youtube.com/results?search_query={query}")

# Time, Date & Alarms
def tell_time():
    now = datetime.datetime.now().strftime("%I:%M %p")
    speak(f"The current time is {now}")

def tell_date():
    today = datetime.datetime.now().strftime("%A, %B %d, %Y")
    speak(f"Today's date is {today}")

def set_alarm(time):
    speak(f"Alarm set for {time}.")
    print(f"Alarm set for {time} ⏰")

# Notes & Reminders
def take_note():
    speak("What should I note down?")
    note = listen()
    if note:
        with open("notes.txt", "a") as file:
            file.write(note + "\n")
        speak("Note saved.")

def read_notes():
    try:
        with open("notes.txt", "r") as file:
            notes = file.readlines()
            if notes:
                speak("Your notes are:")
                for note in notes:
                    speak(note.strip())
            else:
                speak("You have no notes.")
    except FileNotFoundError:
        speak("You have no notes.")

# AI Chat Mode
def ai_chat():
    speak("Sure! Let's chat. Say 'exit chat' to stop.")
    while True:
        user_input = listen()
        if "exit chat" in user_input:
            speak("Exiting chat mode.")
            break
        else:
            speak(f"You said: {user_input}")  # Replace this with AI responses later

# Email Sending (Requires SMTP setup)
def send_email():
    try:
        speak("Who do you want to email?")
        recipient = listen()
        speak("What should I say?")
        message = listen()
        
        # Configure your email account here
        sender_email = "your_email@gmail.com"
        sender_password = "your_password"
        recipient_email = "recipient_email@gmail.com"

        server = smtplib.SMTP("smtp.gmail.com", 587)
        server.starttls()
        server.login(sender_email, sender_password)
        server.sendmail(sender_email, recipient_email, message)
        server.quit()
        
        speak("Email sent successfully.")
    except Exception as e:
        speak("Failed to send email.")

# Perform Actions with Fuzzy Matching
def perform_action(command):
    commands = {
        "turn on lights": lambda: control_device("lights", "on"),
        "turn off lights": lambda: control_device("lights", "off"),
        "set temperature": lambda: speak("Temperature set!"),
        "weather": get_weather,
        "news": lambda: speak("Fetching news..."),
        "play music": play_music,
        "play youtube": lambda: play_youtube(command.split("play youtube")[-1]),
        "search google": lambda: search_web(command.split("search google for")[-1]),
        "search wikipedia": lambda: search_wikipedia(command.split("search wikipedia for")[-1]),
        "time": tell_time,
        "date": tell_date,
        "set alarm": lambda: set_alarm(command.split("set alarm for")[-1]),
        "take note": take_note,
        "read notes": read_notes,
        "chat mode": ai_chat,
        "send email": send_email,
        "exit": lambda: (speak("Thank You So Much .Have A Nice Day . Goodbye!"), exit())
    }

    best_match, confidence = process.extractOne(command, commands.keys())
    if confidence > 70:
        commands[best_match]()
    else:
        speak("Sorry, I don't understand that command.")

# Main Loop
if __name__ == "__main__":
    speak("Hello! I am PRATIK your smart assistant. How can I assist you?")
    while True:
        command = listen()
        if command:
            perform_action(command)
