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

import tkinter as tk
import subprocess

class ChatbotApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Application Launcher Chatbot")

        self.chat_display = tk.Text(root, height=20, width=60, state="disabled")
        self.chat_display.pack(padx=10, pady=10)

        self.entry = tk.Entry(root, width=50)
        self.entry.pack(side="left", padx=10, pady=10)
        self.entry.bind("<Return>", self.process_input)

        send_button = tk.Button(root, text="Send", command=self.process_input)
        send_button.pack(side="left", padx=5)

        self.apps = {
            'word': 'winword.exe',
            'zoom': 'zoom.exe',
            'vscode': 'Code.exe',
            'vsstudio': 'devenv.exe',
            'edge': 'msedge.exe',
            'firefox': 'firefox.exe'
        }

    def add_to_chat(self, message):
        self.chat_display.config(state="normal")
        self.chat_display.insert(tk.END, message + "\n")
        self.chat_display.config(state="disabled")

    def process_input(self, event=None):
        user_input = self.entry.get().strip()
        self.entry.delete(0, tk.END)

        if not user_input:
            return

        self.add_to_chat(f"You: {user_input}")

        if user_input.lower() in ['quit', 'exit', 'bye']:
            self.add_to_chat("Bot: Goodbye! Have a great day!")
            self.root.quit()
            return

        if any(word in user_input.lower() for word in ['open', 'launch', 'start', 'run']):
            words = user_input.lower().split()
            for i, word in enumerate(words):
                if word in ['open', 'launch', 'start', 'run'] and i + 1 < len(words):
                    app_name = words[i + 1].strip()
                    if app_name in self.apps:
                        try:
                            subprocess.Popen(self.apps[app_name])
                            self.add_to_chat(f"Bot: ✓ Opening {app_name.capitalize()}...")
                        except Exception as e:
                            self.add_to_chat(f"Bot: ✗ Error opening {app_name}: {str(e)}")
                    else:
                        self.add_to_chat(f"Bot: ✗ Unknown app '{app_name}'. Available apps: {', '.join(self.apps.keys())}")
                    return
            self.add_to_chat("Bot: Which application would you like to open?")
        else:
            if 'hello' in user_input.lower() or 'hi' in user_input.lower():
                self.add_to_chat("Bot: Hello! Would you like me to open an application?")
            elif 'help' in user_input.lower():
                self.add_to_chat("Bot: Just ask me to open an application! For example: 'Open word'")
            else:
                self.add_to_chat("Bot: I can open applications for you. Try saying 'open zoom' or 'start vscode'!")

if __name__ == "__main__":
    root = tk.Tk()
    app = ChatbotApp(root)
    root.mainloop()
