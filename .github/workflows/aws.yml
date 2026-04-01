import socket
import pyaudio
import threading
import tkinter as tk
from tkinter import messagebox

# إعدادات الاتصال
HOST = '127.0.0.1'
PORT = 12345

# إعدادات الصوت
CHUNK = 1024
FORMAT = pyaudio.paInt16
CHANNELS = 1
RATE = 44100

class ChatApp:
    def __init__(self):
        self.root = tk.Tk()
        self.root.title('دردشة صوتية')

        # إنشاء واجهة المستخدم
        self.username_label = tk.Label(self.root, text='اسم المستخدم:')
        self.username_label.pack()
        self.username_entry = tk.Entry(self.root)
        self.username_entry.pack()
        self.join_button = tk.Button(self.root, text='انضمام', command=self.join_chat)
        self.join_button.pack()
        self.chat_log = tk.Text(self.root)
        self.chat_log.pack()
        self.message_entry = tk.Entry(self.root)
        self.message_entry.pack()
        self.send_button = tk.Button(self.root, text='إرسال', command=self.send_message)
        self.send_button.pack()

        # إنشاء socket
        self.sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

    def join_chat(self):
        # الاتصال بالسيرفر
        self.sock.connect((HOST, PORT))
        self.username = self.username_entry.get()
        self.sock.sendall(self.username.encode())

        # بدء استقبال الرسائل
        self.receive_thread = threading.Thread(target=self.receive_messages)
        self.receive_thread.start()

        # بدء استقبال الصوت
        self.audio_thread = threading.Thread(target=self.receive_audio)
        self.audio_thread.start()

    def send_message(self):
        # إرسال الرسالة
        message = self.message_entry.get()
        self.sock.sendall(message.encode())
        self.chat_log.insert(tk.END, f'أنت: {message}\n')
        self.message_entry.delete(0, tk.END)

    def receive_messages(self):
        while True:
            # استقبال الرسالة
            message = self.sock.recv(1024).decode()
            self.chat_log.insert(tk.END, f'{message}\n')

    def receive_audio(self):
        # إنشاء pyaudio
        p = pyaudio.PyAudio()
        stream = p.open(format=FORMAT, channels=CHANNELS, rate=RATE, output=True, frames_per_buffer=CHUNK)

        while True:
            # استقبال الصوت
            data = self.sock.recv(CHUNK)
            stream.write(data)

    def run(self):
        self.root.mainloop()

if __name__ == '__main__':
    app = ChatApp()
    app.run()
