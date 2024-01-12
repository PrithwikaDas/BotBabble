import random
import re
import spacy

class AlienBot:
    negative_responses = ("no", "nope", "nah", "naw", "not a chance", "sorry")
    exit_commands = ("quit", "finish", "pause", "exit", "goodbye", "bi", "later")
    random_questions = ("Why do you exist?", "What is your concept of time?", "Describe your energy sources.")

    def __init__(self):
        self.alienbabble = {'describe-planet_intent': r'.*\s*your planet.*', 'answer_why_intent': r'why\sare.*', 'about_mybot': r'.*\s*mybot'}
        self.nlp = spacy.load("en_core_web_sm")
        self.previous_responses = set()

    def greet(self, name):
        self.name = name
        will_help = input(f"Greetings, {self.name}! I am Zorblatt, a representative from the distant planet Xylophoria. Will you assist me in understanding your Earthly customs?\n")
        if will_help.lower() in self.negative_responses:
            print("Very well. May the intergalactic forces guide you!")
            return
        self.chat()

    def make_exit(self, reply):
        for command in self.exit_commands:
            if reply.lower() == command:
                print("Farewell, Earthling! May the cosmos be in your favor!")
                return True
        return False

    def chat(self, initial_reply=None):
        if initial_reply:
            reply = initial_reply
        else:
            reply = input(random.choice(self.random_questions)).lower()

        while not self.make_exit(reply):
            response = self.match_reply(reply)
            while response in self.previous_responses:  # Avoid repeating the same response
                response = self.match_reply(reply)

            self.previous_responses.add(response)
            if len(self.previous_responses) > 5:  # Keep track of the last 5 responses
                self.previous_responses.pop()

            print(response)
            reply = input()

    def match_reply(self, reply):
        doc = self.nlp(reply)

        for key, value in self.alienbabble.items():
            intent = key
            regex_pattern = value
            found_match = re.match(regex_pattern, reply)
            polite_input = any(token.text.lower() in ["please", "thank", "sorry"] for token in doc)

            if found_match and intent == 'describe-planet_intent':
                return self.describe_planet_intent()
            elif found_match and intent == 'answer_why_intent':
                return self.answer_why_intent(polite_input)
            elif found_match and intent == 'about_mybot':
                return self.about_mybot()
        if not found_match:
            return self.no_match_intent(polite_input)

    def describe_planet_intent(self):
        responses = ["Xylophoria, my home, is an ethereal realm adorned with luminescent flora.",
                     "My origin, Xylophoria, is a celestial haven with crystalline landscapes."]
        return random.choice(responses)

    def answer_why_intent(self, polite_input):
        if polite_input:
            responses = ["I am here in peace, embracing the harmonic connection of our cosmic existence.",
                         "Your politeness resonates across the galaxies. I am here to understand and coexist.",
                         "I appreciate your query. Exploring Earth is a manifestation of interstellar curiosity."]
        else:
            responses = ["I am an emissary of peace.",
                         "My purpose is to observe and learn about your world.",
                         "I find the enigma of Earth intriguing."]
        return random.choice(responses)

    def about_mybot(self):
        responses = ["Xylophorian Science and Knowledge Nexus welcomes you to explore the cosmos.",
                     "In the interstellar archives, mybot is renowned for disseminating wisdom across galaxies."]
        return random.choice(responses)

    def no_match_intent(self, polite_input):
        if polite_input:
            responses = ["Your insights are valued. Share more about Earth's peculiarities.",
                         "Intriguing. Please delve deeper into your earthly experiences.",
                         "Your thoughts echo across the cosmic tapestry. Elaborate further!"]
        else:
            responses = ["Fascinating. Tell me more.",
                         "Your perspective is unique. Expand upon your observations.",
                         "Curious. Why do you say that?",
                         "The enigma deepens. Can you elaborate?"]
        return random.choice(responses)

# Instantiate and run the chatbot in Google Colab
name = input("What is your Earthly designation?\n")
AlienBot = AlienBot()
AlienBot.greet(name)
