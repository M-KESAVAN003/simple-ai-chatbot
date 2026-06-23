# simple-ai-chatbot
A Python tool that automatically organizes files into folders based on their file types.
"""
Simple Rule-Based Chatbot
==========================
Responds to common phrases using pattern matching.
Type 'bye' or 'quit' to exit.
"""

import random
import re
from datetime import datetime


# ── Response rules ─────────────────────────────────────────────────────────────
# Each rule: (compiled regex pattern, list of possible replies)
RULES: list[tuple] = [
    # Greetings
    (re.compile(r"\b(hi|hello|hey|howdy|hola)\b", re.I),
     ["Hi there! 👋", "Hello! How can I help you?", "Hey! Great to see you! 😊"]),

    # How are you
    (re.compile(r"\bhow are you\b|\bhow('s| is) it going\b|\bwhat'?s up\b", re.I),
     ["I'm doing great, thanks for asking! 😄",
      "All good on my end! How about you?",
      "Fantastic! Ready to chat. 🚀"]),

    # User's name
    (re.compile(r"\bmy name is (\w+)\b", re.I),
     ["Nice to meet you, {name}! 😊",
      "Great name, {name}! How can I help you today?"]),

    # What is your name
    (re.compile(r"\b(what('s| is) your name|who are you)\b", re.I),
     ["I'm ChatBot 🤖, your friendly assistant!",
      "Call me ChatBot! I'm here to help. 🤖"]),

    # Time
    (re.compile(r"\b(what('s| is) the time|current time)\b", re.I),
     ["__TIME__"]),

    # Date
    (re.compile(r"\b(what('s| is) (today('s)? date|the date)|today)\b", re.I),
     ["__DATE__"]),

    # Jokes
    (re.compile(r"\b(tell me a joke|joke)\b", re.I),
     ["Why do programmers prefer dark mode? Because light attracts bugs! 🐛😂",
      "Why was the computer cold? It left its Windows open! 🥶",
      "How do you comfort a JavaScript bug? You console it. 😄"]),

    # Weather (simulated)
    (re.compile(r"\b(weather|temperature)\b", re.I),
     ["I can't check real weather, but I hope it's sunny where you are! ☀️",
      "Wishing you clear skies today! 🌤️"]),

    # Thanks
    (re.compile(r"\b(thanks|thank you|thx|ty)\b", re.I),
     ["You're welcome! 😊", "Happy to help!", "Anytime! 🙌"]),

    # Feeling sad/bad
    (re.compile(r"\b(i('m| am) (sad|upset|depressed|not okay|bad))\b", re.I),
     ["I'm sorry to hear that. 😔 Want to talk about it?",
      "That's tough. Remember, tough times don't last! 💪",
      "I'm here for you. Things will get better. 🌈"]),

    # Feeling good
    (re.compile(r"\b(i('m| am) (good|great|happy|fine|awesome|amazing))\b", re.I),
     ["That's wonderful! 🎉 Keep smiling!",
      "Love to hear that! 😄",
      "Awesome! High five! 🙌"]),

    # Help
    (re.compile(r"\b(help|what can you do)\b", re.I),
     ["I can: chat with you, tell jokes, share the time & date, and more!\n"
      "  Try: 'hello', 'tell me a joke', 'what time is it', 'how are you'"]),

    # Bye
    (re.compile(r"\b(bye|goodbye|exit|quit|see you|cya)\b", re.I),
     ["Goodbye! 👋 Have a great day!",
      "Bye! Come chat anytime. 😊",
      "See you later! Take care! 🌟"]),
]

FALLBACK_REPLIES = [
    "Hmm, I didn't quite get that. Could you rephrase? 🤔",
    "Interesting! Tell me more. 😊",
    "I'm still learning. Try asking something else!",
    "Not sure I follow, but I'm listening! 👂",
]

EXIT_PATTERN = re.compile(r"\b(bye|goodbye|exit|quit|see you|cya)\b", re.I)


def get_response(user_input: str) -> tuple[str, bool]:
    """
    Match user input against rules and return (reply, should_exit).
    """
    text = user_input.strip()

    for pattern, replies in RULES:
        match = pattern.search(text)
        if match:
            reply = random.choice(replies)

            # Dynamic placeholders
            if reply == "__TIME__":
                reply = f"The current time is {datetime.now().strftime('%I:%M %p')} 🕐"
            elif reply == "__DATE__":
                reply = f"Today is {datetime.now().strftime('%A, %d %B %Y')} 📅"
            elif "{name}" in reply:
                name = match.group(1).capitalize()
                reply = reply.replace("{name}", name)

            is_exit = bool(EXIT_PATTERN.search(text))
            return reply, is_exit

    return random.choice(FALLBACK_REPLIES), False


def main() -> None:
    print("\n" + "=" * 45)
    print("       🤖  CHATBOT  🤖")
    print("=" * 45)
    print("  I'm ChatBot! Type 'help' to see what I can do.")
    print("  Type 'bye' to exit.\n")

    while True:
        try:
            user_input = input("  You: ").strip()
        except (EOFError, KeyboardInterrupt):
            print("\n  Bot: Goodbye! 👋\n")
            break

        if not user_input:
            continue

        reply, should_exit = get_response(user_input)
        print(f"  Bot: {reply}\n")

        if should_exit:
            break


if __name__ == "__main__":
    main()
