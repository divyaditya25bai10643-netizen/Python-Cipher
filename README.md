import tkinter as tk
from tkinter import ttk, messagebox


class CaesarCipherApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Caesar Cipher")
        self.root.geometry("600x500")
        self.root.resizable(True, True)
        self.root.configure(bg='#f0f0f0')  # Light background

        # Set a modern theme
        self.style = ttk.Style()
        self.style.theme_use('clam')

        # Configure custom styles for light theme
        self.style.configure('Title.TLabel',
                             background='#f0f0f0',
                             foreground='#2c3e50',
                             font=('Arial', 18, 'bold'))

        self.style.configure('Subtitle.TLabel',
                             background='#f0f0f0',
                             foreground='#34495e',
                             font=('Arial', 11))

        self.style.configure('Custom.TButton',
                             background='#3498db',
                             foreground='white',
                             font=('Arial', 10, 'bold'),
                             padding=(10, 5))

        self.style.map('Custom.TButton',
                       background=[('active', '#2980b9')])

        self.style.configure('Custom.TFrame',
                             background='#ffffff',
                             relief='raised',
                             borderwidth=1)

        # Create the UI
        self.create_widgets()

    def create_widgets(self):
        # Title
        title_label = ttk.Label(self.root, text="Caesar Cipher", style='Title.TLabel')
        title_label.pack(pady=10)

        # Subtitle
        subtitle_label = ttk.Label(self.root,
                                   text="Encrypt and decrypt messages with the classic Caesar cipher",
                                   style='Subtitle.TLabel')
        subtitle_label.pack(pady=(0, 20))

        # Main container
        main_frame = ttk.Frame(self.root, style='Custom.TFrame')
        main_frame.pack(fill='both', expand=True, padx=20, pady=10)

        # Input section
        input_frame = ttk.Frame(main_frame, style='Custom.TFrame')
        input_frame.pack(fill='x', padx=10, pady=10)

        ttk.Label(input_frame, text="Input Text:", style='Subtitle.TLabel').grid(row=0, column=0, sticky='w', pady=5)

        self.input_text = tk.Text(input_frame, height=4, width=60, bg='white', fg='black',
                                  font=('Arial', 10), wrap='word', borderwidth=1, relief='solid')
        self.input_text.grid(row=1, column=0, columnspan=2, sticky='we', pady=5)

        # Shift value section
        shift_frame = ttk.Frame(main_frame, style='Custom.TFrame')
        shift_frame.pack(fill='x', padx=10, pady=10)

        ttk.Label(shift_frame, text="Shift Value:", style='Subtitle.TLabel').grid(row=0, column=0, sticky='w', pady=5)

        self.shift_var = tk.IntVar(value=3)
        shift_scale = ttk.Scale(shift_frame, from_=1, to=25, variable=self.shift_var,
                                orient='horizontal')
        shift_scale.grid(row=1, column=0, sticky='we', pady=5)

        self.shift_label = ttk.Label(shift_frame, text="3", style='Subtitle.TLabel')
        self.shift_label.grid(row=1, column=1, padx=10)

        # Update shift label when scale changes
        shift_scale.configure(command=self.update_shift_label)

        # Buttons
        button_frame = ttk.Frame(main_frame, style='Custom.TFrame')
        button_frame.pack(fill='x', padx=10, pady=10)

        encrypt_btn = ttk.Button(button_frame, text="Encrypt", style='Custom.TButton',
                                 command=self.encrypt)
        encrypt_btn.grid(row=0, column=0, padx=5, pady=5, sticky='we')

        decrypt_btn = ttk.Button(button_frame, text="Decrypt", style='Custom.TButton',
                                 command=self.decrypt)
        decrypt_btn.grid(row=0, column=1, padx=5, pady=5, sticky='we')

        clear_btn = ttk.Button(button_frame, text="Clear", style='Custom.TButton',
                               command=self.clear_text)
        clear_btn.grid(row=0, column=2, padx=5, pady=5, sticky='we')

        # Configure column weights for responsive buttons
        button_frame.columnconfigure(0, weight=1)
        button_frame.columnconfigure(1, weight=1)
        button_frame.columnconfigure(2, weight=1)

        # Output section
        output_frame = ttk.Frame(main_frame, style='Custom.TFrame')
        output_frame.pack(fill='both', expand=True, padx=10, pady=10)

        ttk.Label(output_frame, text="Output Text:", style='Subtitle.TLabel').pack(anchor='w', pady=5)

        self.output_text = tk.Text(output_frame, height=4, width=60, bg='white', fg='black',
                                   font=('Arial', 10), wrap='word', state='disabled',
                                   borderwidth=1, relief='solid')
        self.output_text.pack(fill='both', expand=True, pady=5)

        # Information section
        info_frame = ttk.Frame(main_frame, style='Custom.TFrame')
        info_frame.pack(fill='x', padx=10, pady=10)

        info_text = (
            "The Caesar cipher is one of the simplest and most widely known encryption techniques. "
            "It is a type of substitution cipher in which each letter in the plaintext is replaced "
            "by a letter some fixed number of positions down the alphabet."
        )

        info_label = ttk.Label(info_frame, text=info_text, style='Subtitle.TLabel', wraplength=550)
        info_label.pack(pady=5)

    def update_shift_label(self, value):
        self.shift_label.config(text=str(int(float(value))))

    def caesar_cipher(self, text, shift, mode='encrypt'):
        result = ""

        for char in text:
            if char.isalpha():
                # Determine the appropriate case
                start = ord('A') if char.isupper() else ord('a')

                if mode == 'encrypt':
                    # Shift forward for encryption
                    new_char = chr((ord(char) - start + shift) % 26 + start)
                else:
                    # Shift backward for decryption
                    new_char = chr((ord(char) - start - shift) % 26 + start)

                result += new_char
            else:
                # Non-alphabetic characters remain unchanged
                result += char

        return result

    def encrypt(self):
        input_text = self.input_text.get("1.0", tk.END).strip()
        if not input_text:
            messagebox.showwarning("Input Error", "Please enter some text to encrypt.")
            return

        shift = self.shift_var.get()
        encrypted_text = self.caesar_cipher(input_text, shift, 'encrypt')

        self.output_text.config(state='normal')
        self.output_text.delete("1.0", tk.END)
        self.output_text.insert("1.0", encrypted_text)
        self.output_text.config(state='disabled')

    def decrypt(self):
        input_text = self.input_text.get("1.0", tk.END).strip()
        if not input_text:
            messagebox.showwarning("Input Error", "Please enter some text to decrypt.")
            return

        shift = self.shift_var.get()
        decrypted_text = self.caesar_cipher(input_text, shift, 'decrypt')

        self.output_text.config(state='normal')
        self.output_text.delete("1.0", tk.END)
        self.output_text.insert("1.0", decrypted_text)
        self.output_text.config(state='disabled')

    def clear_text(self):
        self.input_text.delete("1.0", tk.END)
        self.output_text.config(state='normal')
        self.output_text.delete("1.0", tk.END)
        self.output_text.config(state='disabled')


if __name__ == "__main__":
    root = tk.Tk()
    app = CaesarCipherApp(root)
    root.mainloop()
