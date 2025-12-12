# chatbot
opens apps
[main.py](https://github.com/user-attachments/files/24121203/main.py)
# Chatbot: Application Launcher

A simple Python chatbot with a Tkinter GUI that opens apps based on user input.

### 🔗 Live Demo
[Try the chatbot here](https://your-chatbot-demo.netlify.app)

### 📄 Features
- Launches apps via natural language
- GUI interface using Tkinter
- Built with subprocess and keyword matching

# Import the tkinter library for GUI development
import tkinter as tk

# Import subprocess to allow launching external applications
import subprocess

# Define a class to encapsulate the chatbot application
class ChatbotApp:
    # Constructor method that runs when the class is created
    def __init__(self, root):
        # Store the main Tkinter window in self.root
        self.root = root
        # Set the window title
        self.root.title("Application Launcher Chatbot")

        # Create a Text widget to display chat history
        # height=20 lines, width=60 characters
        # state="disabled" prevents direct editing by the user
        self.chat_display = tk.Text(root, height=20, width=60, state="disabled")
        # Place the Text widget in the window with padding
        self.chat_display.pack(padx=10, pady=10)

        # Create an Entry widget for user input
        self.entry = tk.Entry(root, width=50)
        # Place the Entry widget on the left side with padding
        self.entry.pack(side="left", padx=10, pady=10)
        # Bind the Enter key to trigger the process_input method
        self.entry.bind("<Return>", self.process_input)

        # Create a Send button that also triggers process_input
        send_button = tk.Button(root, text="Send", command=self.process_input)
        # Place the button next to the Entry field
        send_button.pack(side="left", padx=5)

        # Dictionary mapping app names to their executable files
        self.apps = {
            'notepad': 'notepad.exe',
            'calculator': 'calc.exe',
            'paint': 'mspaint.exe',
            'explorer': 'explorer.exe',
            'cmd': 'cmd.exe'
        }

    # Method to insert text into the chat history
    def add_to_chat(self, message):
        # Temporarily enable editing of the Text widget
        self.chat_display.config(state="normal")
        # Insert the message at the end of the chat history
        self.chat_display.insert(tk.END, message + "\n")
        # Disable editing again to keep chat history read-only
        self.chat_display.config(state="disabled")

    # Method to process user input from the Entry widget
    def process_input(self, event=None):
        # Get the text entered by the user and remove extra spaces
        user_input = self.entry.get().strip()
        # Clear the Entry field after reading input
        self.entry.delete(0, tk.END)

        # If the user typed nothing, do nothing
        if not user_input:
            return

        # Display the user's input in the chat history
        self.add_to_chat(f"You: {user_input}")

        # Check for exit commands
        if user_input.lower() in ['quit', 'exit', 'bye']:
            # Display goodbye message
            self.add_to_chat("Bot: Goodbye! Have a great day!")
            # Close the Tkinter window
            self.root.quit()
            return

        # Check if the user wants to open an application
        if any(word in user_input.lower() for word in ['open', 'launch', 'start', 'run']):
            # Split the input into words
            words = user_input.lower().split()
            # Loop through words to find the app name after a keyword
            for i, word in enumerate(words):
                if word in ['open', 'launch', 'start', 'run'] and i + 1 < len(words):
                    app_name = words[i + 1].strip()
                    # If the app is in the dictionary, try to open it
                    if app_name in self.apps:
                        try:
                            subprocess.Popen(self.apps[app_name])
                            self.add_to_chat(f"Bot: ✓ Opening {app_name.capitalize()}...")
                        except Exception as e:
                            # Handle errors if the app fails to launch
                            self.add_to_chat(f"Bot: ✗ Error opening {app_name}: {str(e)}")
                    else:
                        # Inform the user if the app is unknown
                        self.add_to_chat(f"Bot: ✗ Unknown app '{app_name}'. Available apps: {', '.join(self.apps.keys())}")
                    return
            # If no app name was found, ask the user again
            self.add_to_chat("Bot: Which application would you like to open?")
        else:
            # Simple conversational responses
            if 'hello' in user_input.lower() or 'hi' in user_input.lower():
                self.add_to_chat("Bot: Hello! Would you like me to open an application?")
            elif 'help' in user_input.lower():
                self.add_to_chat("Bot: Just ask me to open an application! For example: 'Open notepad'")
            else:
                self.add_to_chat("Bot: I can open applications for you. Try saying 'open notepad'!")

# Run the chatbot application
if __name__ == "__main__":
    # Create the main Tkinter window
    root = tk.Tk()
    # Create an instance of ChatbotApp with the window
    app = ChatbotApp(root)
    # Start the Tkinter event loop (keeps the window open)
    root.mainloop()
