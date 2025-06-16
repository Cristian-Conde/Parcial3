import tkinter as tk
from tkinter import messagebox
import random

# Juego 1: Triki (Tic Tac Toe)
class Triki:
    def __init__(self, master):
        self.master = master
        self.turn = "X"
        self.vs_machine = False
        self.buttons = [[None]*3 for _ in range(3)]
        self.choose_mode()

    def choose_mode(self):
        ventana = tk.Toplevel(self.master)
        ventana.title("Modo de Juego")
        ventana.geometry("260x160")
        ventana.configure(bg="#cce7ff")
        ventana.resizable(False, False)
        ventana.grab_set()

        tk.Label(
            ventana, text="🎮 Elige el modo de juego:", bg="#cce7ff",
            font=("Helvetica", 13, "bold")
        ).pack(pady=10)

        tk.Button(
            ventana, text="👥 1 vs 1", width=20, height=2, bg="#70d6ff",
            font=("Arial", 11), command=lambda: self.start_game(False, ventana)
        ).pack(pady=5)

        tk.Button(
            ventana, text="🧠 1 vs Máquina", width=20, height=2, bg="#ffb3c1",
            font=("Arial", 11), command=lambda: self.start_game(True, ventana)
        ).pack(pady=5)

    def start_game(self, vs_machine, ventana):
        self.vs_machine = vs_machine
        ventana.destroy()
        self.root = tk.Toplevel(self.master)
        self.root.title("🟦 Triki - Tic Tac Toe 🟥")
        self.root.configure(bg="#f0f4ff")
        self.create_board()

    def create_board(self):
        tk.Label(
            self.root, text="¡Juega Triki!", bg="#f0f4ff",
            font=("Comic Sans MS", 18, "bold"), fg="#333"
        ).pack(pady=10)

        frame = tk.Frame(self.root, bg="#f0f4ff")
        frame.pack(pady=10)

        for i in range(3):
            for j in range(3):
                self.buttons[i][j] = tk.Button(
                    frame, text="", font=("Verdana", 24, "bold"), width=5, height=2,
                    bg="#e0f7fa", fg="#333",
                    activebackground="#aed9e0",
                    command=lambda i=i, j=j: self.play(i, j)
                )
                self.buttons[i][j].grid(row=i, column=j, padx=6, pady=6)

    def play(self, i, j):
        if self.buttons[i][j]["text"] == "":
            self.buttons[i][j]["text"] = self.turn
            self.buttons[i][j]["fg"] = "#1976d2" if self.turn == "X" else "#d32f2f"

            if self.check_winner(self.turn):
                messagebox.showinfo("🎉 Fin del juego", f"¡{self.turn} gana!")
                self.root.destroy()
            elif all(self.buttons[x][y]["text"] != "" for x in range(3) for y in range(3)):
                messagebox.showinfo("🤝 Fin del juego", "¡Empate!")
                self.root.destroy()
            else:
                self.turn = "O" if self.turn == "X" else "X"
                if self.vs_machine and self.turn == "O":
                    self.root.after(400, self.machine_move)

    def machine_move(self):
        vacios = [(i, j) for i in range(3) for j in range(3) if self.buttons[i][j]["text"] == ""]
        if vacios:
            i, j = random.choice(vacios)
            self.play(i, j)

    def check_winner(self, player):
        for i in range(3):
            if all(self.buttons[i][j]["text"] == player for j in range(3)) or \
               all(self.buttons[j][i]["text"] == player for j in range(3)):
                return True
        return all(self.buttons[i][i]["text"] == player for i in range(3)) or \
               all(self.buttons[i][2 - i]["text"] == player for i in range(3))



# Juego 2: Adivina el número
class AdivinaNumero:
    def __init__(self, root):
        self.master = root
        self.min = 1
        self.max = 100
        self.numero_secreto = None
        self.ventana_rango()

    def ventana_rango(self):
        ventana = tk.Toplevel(self.master)
        ventana.title("Elegir Rango")
        ventana.geometry("300x200")
        ventana.configure(bg="#e0f7fa")
        ventana.grab_set()

        tk.Label(ventana, text="Número mínimo:", font=("Arial", 11), bg="#e0f7fa").pack(pady=(10, 0))
        self.entry_min = tk.Entry(ventana, font=("Arial", 11), justify="center")
        self.entry_min.insert(0, "1")
        self.entry_min.pack(pady=5)

        tk.Label(ventana, text="Número máximo:", font=("Arial", 11), bg="#e0f7fa").pack(pady=(10, 0))
        self.entry_max = tk.Entry(ventana, font=("Arial", 11), justify="center")
        self.entry_max.insert(0, "100")
        self.entry_max.pack(pady=5)

        tk.Button(ventana, text="Iniciar Juego", font=("Arial", 11, "bold"), bg="#4fc3f7", fg="white", command=lambda: self.iniciar_juego(ventana)).pack(pady=15)

    def iniciar_juego(self, ventana):
        try:
            self.min = int(self.entry_min.get())
            self.max = int(self.entry_max.get())
            if self.min >= self.max:
                raise ValueError
            self.numero_secreto = random.randint(self.min, self.max)
            ventana.destroy()
            self.crear_ventana_juego()
        except ValueError:
            messagebox.showerror("Error", "Rango inválido. Asegúrate de que el mínimo sea menor que el máximo.")

    def crear_ventana_juego(self):
        self.root = tk.Toplevel(self.master)
        self.root.title("Adivina el Número")
        self.root.geometry("350x220")
        self.root.configure(bg="#f1f8e9")

        tk.Label(self.root, text=f"Adivina un número entre {self.min} y {self.max}:", font=("Arial", 12, "bold"), bg="#f1f8e9").pack(pady=15)
        self.entry = tk.Entry(self.root, font=("Arial", 12), justify="center")
        self.entry.pack(pady=10)

        tk.Button(self.root, text="Adivinar", font=("Arial", 11, "bold"), bg="#aed581", fg="white", command=self.verificar).pack(pady=5)
        self.resultado = tk.Label(self.root, text="", font=("Arial", 12), bg="#f1f8e9")
        self.resultado.pack(pady=15)

    def verificar(self):
        try:
            guess = int(self.entry.get())
            if guess < self.numero_secreto:
                self.resultado.config(text="🔼 Más Alto", fg="blue")
            elif guess > self.numero_secreto:
                self.resultado.config(text="🔽 Más Bajo", fg="orange")
            else:
                messagebox.showinfo("🎉 ¡Correcto!", "¡Has adivinado el número!")
                self.root.destroy()
        except ValueError:
            self.resultado.config(text="Por favor, ingresa un número válido.", fg="red")


# Juego 3: Piedra, Papel o Tijera
class PiedraPapelTijera:
    def __init__(self, master):
        self.master = master
        self.vs_machine = False
        self.turno = "Jugador 1"
        self.emoji = {"Piedra": "✊", "Papel": "✋", "Tijera": "✌️"}
        self.elecciones = {}
        self.nombres = {}
        self.choose_mode()

    def choose_mode(self):
        self.ventana = tk.Toplevel(self.master)
        self.ventana.title("Modo de Juego")
        self.ventana.geometry("300x250")
        self.ventana.configure(bg="lightblue")
        self.ventana.grab_set()

        tk.Label(self.ventana, text="Elige el modo de juego:", bg="lightblue", font=("Arial", 12, "bold")).pack(pady=10)
        tk.Button(self.ventana, text="1 vs 1", width=18, command=lambda: self.show_name_inputs(False)).pack(pady=5)
        tk.Button(self.ventana, text="1 vs Máquina", width=18, command=lambda: self.show_name_inputs(True)).pack(pady=5)

    def show_name_inputs(self, vs_machine):
        self.vs_machine = vs_machine
        for widget in self.ventana.winfo_children():
            widget.destroy()

        tk.Label(self.ventana, text="Ingrese nombre del Jugador 1:", bg="lightblue").pack(pady=5)
        self.entry_j1 = tk.Entry(self.ventana)
        self.entry_j1.pack(pady=5)

        if not vs_machine:
            tk.Label(self.ventana, text="Ingrese nombre del Jugador 2:", bg="lightblue").pack(pady=5)
            self.entry_j2 = tk.Entry(self.ventana)
            self.entry_j2.pack(pady=5)
        else:
            self.entry_j2 = None

        tk.Button(self.ventana, text="Comenzar", command=self.init_ui).pack(pady=10)

    def init_ui(self):
        self.nombres["Jugador 1"] = self.entry_j1.get() or "Jugador 1"
        if self.vs_machine:
            self.nombres["Jugador 2"] = "Máquina"
        else:
            self.nombres["Jugador 2"] = self.entry_j2.get() or "Jugador 2"

        self.ventana.destroy()

        self.root = tk.Toplevel(self.master)
        self.root.title("Piedra, Papel o Tijera")
        self.root.configure(bg="#f0f0ff")

        self.label_turno = tk.Label(self.root, text=f"Turno de: {self.nombres['Jugador 1']}", font=("Arial", 12, "bold"), bg="#f0f0ff")
        self.label_turno.pack(pady=10)

        frame_botones = tk.Frame(self.root, bg="#f0f0ff")
        frame_botones.pack()

        for opcion in ["Piedra", "Papel", "Tijera"]:
            tk.Button(
                frame_botones,
                text=f"{self.emoji[opcion]} {opcion}",
                width=14,
                font=("Arial", 11),
                bg="#d0e1ff",
                command=lambda o=opcion: self.jugar(o)
            ).pack(side=tk.LEFT, padx=5)

        self.resultado = tk.Label(self.root, text="", font=("Arial", 12), bg="#f0f0ff")
        self.resultado.pack(pady=20)

    def jugar(self, eleccion):
        if self.vs_machine:
            pc = random.choice(["Piedra", "Papel", "Tijera"])
            self.ver_resultado(self.nombres["Jugador 1"], eleccion, self.nombres["Jugador 2"], pc)
        else:
            if self.turno == "Jugador 1":
                self.elecciones["Jugador 1"] = eleccion
                self.turno = "Jugador 2"
                self.label_turno.config(text=f"Turno de: {self.nombres['Jugador 2']}")
            else:
                self.elecciones["Jugador 2"] = eleccion
                self.ver_resultado(
                    self.nombres["Jugador 1"], self.elecciones["Jugador 1"],
                    self.nombres["Jugador 2"], self.elecciones["Jugador 2"]
                )
                self.turno = "Jugador 1"
                self.elecciones.clear()
                self.label_turno.config(text=f"Turno: {self.nombres['Jugador 1']}")

    def ver_resultado(self, nombre1, jug1, nombre2, jug2):
        if jug1 == jug2:
            resultado = "¡Empate!"
            color = "orange"
        elif (jug1 == "Piedra" and jug2 == "Tijera") or \
             (jug1 == "Papel" and jug2 == "Piedra") or \
             (jug1 == "Tijera" and jug2 == "Papel"):
            resultado = f"¡{nombre1} gana!"
            color = "green"
        else:
            resultado = f"¡{nombre2} gana!"
            color = "red"

        texto = (
            f"{nombre1} eligió: {self.emoji[jug1]} {jug1}\n"
            f"{nombre2} eligió: {self.emoji[jug2]} {jug2}\n\n"
            f"{resultado}"
        )
        self.resultado.config(text=texto, fg=color)


# Juego 4: Encontrar el Tesoro

class EncontrarTesoro:
    def __init__(self, root):
        self.root = tk.Toplevel(root)
        self.root.title("💰 Encontrar el Tesoro")
        self.root.configure(bg="#1e1e2f")
        self.jugador = "Jugador"
        self.intentos_realizados = 0
        self.intentos_maximos = 0
        self.tam = 4
        self.tesoro_pos = ()
        self.botones = []
        self.mostrar_pantalla_nombre()

    def mostrar_pantalla_nombre(self):
        for widget in self.root.winfo_children():
            widget.destroy()

        tk.Label(self.root, text="¡Bienvenido!", font=("Arial", 20, "bold"), bg="#1e1e2f", fg="white").pack(pady=10)
        tk.Label(self.root, text="Ingresa tu nombre:", font=("Arial", 14), bg="#1e1e2f", fg="white").pack(pady=5)

        self.entry_nombre = tk.Entry(self.root, font=("Arial", 14))
        self.entry_nombre.pack(pady=5)

        tk.Button(self.root, text="Continuar", font=("Arial", 14), bg="#3498db", fg="white",
                  command=self.mostrar_menu_dificultad).pack(pady=10)

    def mostrar_menu_dificultad(self):
        nombre = self.entry_nombre.get().strip()
        self.jugador = nombre if nombre else "Jugador"

        for widget in self.root.winfo_children():
            widget.destroy()

        tk.Label(self.root, text=f"Hola {self.jugador} 👋", font=("Arial", 16, "bold"), bg="#1e1e2f", fg="white").pack(pady=10)
        tk.Label(self.root, text="Selecciona la dificultad:", font=("Arial", 14), bg="#1e1e2f", fg="white").pack(pady=10)

        tk.Button(self.root, text="Fácil", font=("Arial", 14), bg="#2ecc71", fg="white", width=20,
                  command=lambda: self.iniciar_juego(4, 6)).pack(pady=5)
        tk.Button(self.root, text="Medio", font=("Arial", 14), bg="#f1c40f", fg="black", width=20,
                  command=lambda: self.iniciar_juego(6, 10)).pack(pady=5)
        tk.Button(self.root, text="Difícil", font=("Arial", 14), bg="#e74c3c", fg="white", width=20,
                  command=lambda: self.iniciar_juego(8, 15)).pack(pady=5)

    def iniciar_juego(self, tam, intentos_maximos):
        self.tam = tam
        self.intentos_maximos = intentos_maximos
        self.intentos_realizados = 0
        self.tesoro_pos = (random.randint(0, tam - 1), random.randint(0, tam - 1))
        self.botones = []

        for widget in self.root.winfo_children():
            widget.destroy()

        tk.Label(self.root, text=f"Jugador: {self.jugador}", font=("Arial", 12), bg="#1e1e2f", fg="white").pack(pady=5)
        self.label_intentos = tk.Label(self.root,
            text=f"Intentos: 0 / {self.intentos_maximos}",
            font=("Arial", 12), bg="#1e1e2f", fg="white")
        self.label_intentos.pack(pady=5)

        marco = tk.Frame(self.root, bg="#1e1e2f")
        marco.pack(padx=10, pady=10)

        for i in range(tam):
            fila = []
            for j in range(tam):
                btn = tk.Button(marco, text="❓", width=4, height=2, font=("Arial", 18), bg="#2980b9", fg="white",
                                command=lambda x=i, y=j: self.verificar_tesoro(x, y))
                btn.grid(row=i, column=j, padx=2, pady=2)
                fila.append(btn)
            self.botones.append(fila)

    def verificar_tesoro(self, fila, col):
        if self.botones[fila][col]["state"] == "disabled":
            return  # ya fue presionado

        self.intentos_realizados += 1
        self.label_intentos.config(text=f"Intentos: {self.intentos_realizados} / {self.intentos_maximos}")

        if (fila, col) == self.tesoro_pos:
            self.botones[fila][col].config(text="💎", bg="gold")
            messagebox.showinfo("¡Ganaste!", f"🎉 {self.jugador}, ¡encontraste el tesoro en {self.intentos_realizados} intentos!")
            self.root.destroy()
        else:
            self.botones[fila][col].config(text="❌", state="disabled", bg="#34495e")
            if self.intentos_realizados >= self.intentos_maximos:
                self.revelar_tesoro()
                messagebox.showinfo("¡Perdiste!", f"😢 {self.jugador}, ¡Se acabaron los intentos, perdistes!")
                self.root.destroy()

    def revelar_tesoro(self):
        fila, col = self.tesoro_pos
        self.botones[fila][col].config(text="💎", bg="gold")

# Juego 5: Memorama (Memoria)

class Memorama:
    def __init__(self, root):
        self.root = tk.Toplevel(root)
        self.root.title("🧠 Memorama - Selección")
        self.root.configure(bg="#222831")
        self.cartas = []
        self.botones = []
        self.seleccionadas = []
        self.tablero_size = (4, 4)

        self.categorias = {
    "Animales 🐾": [
        '🐶', '🐱', '🐭', '🐹', '🐰', '🦊', '🐻', '🐼', '🐨',
        '🐯', '🦁', '🐮', '🐷', '🐸', '🐵', '🐔', '🐧', '🐤'
    ],
    "Letras 🔤": list("ABCDEFGHIJKLMNOPQRSTUVWXYZ")[:18],  # A-R (18 letras)
    "Frutas 🍎": [
        '🍎', '🍌', '🍇', '🍓', '🍉', '🍍', '🥝', '🍒', '🍑',
        '🍈', '🍊', '🍐', '🍋', '🥥', '🍅', '🥭', '🥑', '🍆'
    ],
    "Caritas 😄": [
        '😀', '😅', '😂', '😎', '😍', '😭', '😡', '🤔', '😇',
        '😱', '🥵', '😴', '🥳', '🤯', '🥺', '😤', '😷', '🤓'
    ],
    "Símbolos ✨": [
        '⭐', '❤️', '💎', '☀️', '🌙', '🌈', '⚡', '❄️', '🔥',
        '🌀', '💡', '📌', '🔔', '🔒', '🧲', '🔮', '🛡️', '📎'
    ]
}

        self.elegida = None
        self.emojis_disponibles = []
        self.mostrar_menu_categoria()

    def mostrar_menu_categoria(self):
        for widget in self.root.winfo_children():
            widget.destroy()

        tk.Label(self.root, text="Selecciona una categoría de emojis", font=("Arial", 16, "bold"), bg="#222831", fg="white").pack(pady=15)

        for nombre, emojis in self.categorias.items():
            tk.Button(
                self.root, text=nombre,
                font=("Arial", 14),
                bg="#00adb5", fg="white",
                width=25, height=2,
                command=lambda e=emojis: self.seleccionar_categoria(e)
            ).pack(pady=5)

    def seleccionar_categoria(self, emojis):
        self.emojis_disponibles = emojis
        self.mostrar_menu_dificultad()

    def mostrar_menu_dificultad(self):
        for widget in self.root.winfo_children():
            widget.destroy()

        tk.Label(self.root, text="Selecciona la dificultad", font=("Arial", 16, "bold"), bg="#222831", fg="white").pack(pady=20)

        tk.Button(self.root, text="Fácil", font=("Arial", 14), bg="#2ac70b", fg="white", width=20, command=lambda: self.iniciar_juego((3, 2))).pack(pady=5)
        tk.Button(self.root, text="Media ", font=("Arial", 14), bg="#d3d615", fg="white", width=20, command=lambda: self.iniciar_juego((4, 4))).pack(pady=5)
        tk.Button(self.root, text="Difícil", font=("Arial", 14), bg="#f82b14", fg="white", width=20, command=lambda: self.iniciar_juego((6, 6))).pack(pady=5)

    def iniciar_juego(self, size):
        for widget in self.root.winfo_children():
            widget.destroy()

        self.tablero_size = size
        self.botones = []
        self.cartas = []
        self.seleccionadas = []

        self.generar_cartas()
        self.mostrar_tablero()

    def generar_cartas(self):
        filas, cols = self.tablero_size
        total_pares = (filas * cols) // 2

        if len(self.emojis_disponibles) < total_pares:
            extras = random.sample(self.categorias["Símbolos ✨"], total_pares - len(self.emojis_disponibles))
            emojis = self.emojis_disponibles + extras
        else:
            emojis = random.sample(self.emojis_disponibles, total_pares)

        pares = emojis * 2
        random.shuffle(pares)

        self.cartas = [pares[i:i+cols] for i in range(0, len(pares), cols)]

    def mostrar_tablero(self):
        filas, cols = self.tablero_size
        for fila in range(filas):
            fila_botones = []
            for col in range(cols):
                btn = tk.Button(
                    self.root, text="❓", width=5, height=2,
                    font=("Arial", 20),
                    bg="#00adb5", fg="white",
                    command=lambda f=fila, c=col: self.revelar_carta(f, c)
                )
                btn.grid(row=fila, column=col, padx=3, pady=3)
                fila_botones.append(btn)
            self.botones.append(fila_botones)

    def revelar_carta(self, fila, col):
        if len(self.seleccionadas) == 2:
            return

        carta = self.cartas[fila][col]
        boton = self.botones[fila][col]
        boton.config(text=carta, state="disabled", bg="#eeeeee", fg="#222831")

        self.seleccionadas.append((fila, col))

        if len(self.seleccionadas) == 2:
            self.root.after(700, self.comparar_cartas)

    def comparar_cartas(self):
        f1, c1 = self.seleccionadas[0]
        f2, c2 = self.seleccionadas[1]

        carta1 = self.cartas[f1][c1]
        carta2 = self.cartas[f2][c2]

        if carta1 == carta2:
            self.botones[f1][c1].config(bg="lightgreen")
            self.botones[f2][c2].config(bg="lightgreen")
        else:
            self.botones[f1][c1].config(text="❓", state="normal", bg="#00adb5", fg="white")
            self.botones[f2][c2].config(text="❓", state="normal", bg="#00adb5", fg="white")

        self.seleccionadas = []
        self.verificar_ganador()

    def verificar_ganador(self):
        for fila in self.botones:
            for btn in fila:
                if btn.cget("text") == "❓":
                    return
        messagebox.showinfo("¡Felicidades!", "🎉 ¡Has emparejado todos los emojis!")
        self.root.destroy()


# Menú principal 
class MenuJuegos:
    def __init__(self, root):
        self.root = root
        self.root.title("🎮 Menú de Juegos")
        self.root.configure(bg="#222831")
        self.root.attributes("-fullscreen", True)  # Pantalla completa


        # Permitir salir con la tecla Esc
        self.root.bind("<Escape>", lambda e: self.root.attributes("-fullscreen", False))

        self.crear_interfaz()

    def crear_interfaz(self):
        tk.Label(
            self.root,
            text="🎮 Bienvenido al Centro de Juegos",
            font=("Comic Sans MS", 28, "bold"),
            fg="#ffffff",
            bg="#222831"
        ).pack(pady=30)

        frame_botones = tk.Frame(self.root, bg="#222831")
        frame_botones.pack()

        botones_info = [
            ("🟦 Triki (Tic Tac Toe)", Triki),
            ("🔢 Adivina el Número", AdivinaNumero),
            ("✊🖐️✌️ Piedra, Papel o Tijera", PiedraPapelTijera),
            ("🪙 Encontrar el Tesoro", EncontrarTesoro),
            ("🧠 Memorama", Memorama),

        ]

        for texto, clase_juego in botones_info:
            tk.Button(
                frame_botones,
                text=texto,
                font=("Verdana", 14, "bold"),
                width=30,
                height=2,
                bg="#00adb5",
                fg="white",
                activebackground="#007c84",
                command=lambda c=clase_juego: c(self.root)
            ).pack(pady=10)

        tk.Button(
            self.root,
            text="🚪 Salir",
            font=("Verdana", 12, "bold"),
            width=20,
            bg="#ff5722",
            fg="white",
            activebackground="#d84315",
            command=self.root.quit
        ).pack(pady=30)


if __name__ == "__main__":
    root = tk.Tk()
    app = MenuJuegos(root)
    root.mainloop()







🎮 Explicación del Proyecto: Centro de Juegos Interactivo
🖥️ 1. ¿Qué es el proyecto?
Este proyecto es una aplicación en Python que funciona como un centro de juegos, donde el usuario puede elegir entre 5 juegos diferentes. Está hecho con la biblioteca Tkinter, que me permite crear interfaces gráficas como ventanas, botones y textos para que el usuario pueda interactuar fácilmente con el programa.

🧩 2. ¿Qué juegos incluye?
Al iniciar, aparece un menú principal con botones que dan acceso a cada uno de los juegos. Cada juego tiene su propia ventana independiente. Los juegos que incluye son:

1. Triki (Tic Tac Toe):
Es el clásico juego de tres en raya.

Se puede jugar entre dos personas o contra la computadora.

En el modo contra la computadora, esta elige posiciones al azar, no utiliza inteligencia artificial.

Gana quien logre alinear 3 símbolos iguales en fila, columna o diagonal.

2. Adivina el Número:
El usuario elige un rango de números (por ejemplo, del 1 al 100).

El programa genera un número aleatorio dentro de ese rango.

El jugador intenta adivinar el número y el programa da pistas:

“Más alto” si el número es mayor.

“Más bajo” si es menor.

El juego termina cuando se adivina correctamente.

3. Piedra, Papel o Tijera:
Se puede jugar entre dos personas o contra la computadora.

En el modo contra la computadora, esta elige aleatoriamente una opción (Piedra, Papel o Tijera).

El programa determina al ganador según las reglas normales del juego.

4. Encontrar el Tesoro:
Se muestra un tablero de botones con casillas ocultas.

El jugador debe encontrar el tesoro escondido (💎) en un número limitado de intentos.

Cada vez que se hace clic en una casilla:

Si no es el tesoro, aparece una ❌.

Si se encuentra, aparece el 💎 y el jugador gana.

Hay tres niveles de dificultad: fácil, medio y difícil.

5. Memorama (Juego de memoria):
El jugador selecciona una categoría de emojis (como frutas, animales, letras).

Luego elige la dificultad: fácil, media o difícil.

El tablero muestra cartas ocultas y el jugador debe emparejar pares iguales.

Si acierta, las cartas se quedan visibles; si falla, se ocultan otra vez.

Gana cuando encuentra todos los pares.

💻 3. ¿Qué tecnología utilicé?
Utilicé Python como lenguaje de programación y Tkinter como biblioteca para crear toda la interfaz gráfica del usuario.

Tkinter me permitió usar los siguientes elementos visuales:

Tk() → ventana principal

Toplevel() → ventanas para cada juego

Button → botones para jugar, seleccionar opciones

Label → textos de instrucciones, resultados y nombres

Entry → cuadros para escribir datos (nombre o números)

Frame → para organizar los botones del tablero

messagebox → ventanas emergentes que muestran mensajes como "Ganaste" o "Empate"

🧠 4. ¿Qué lógica tiene el programa?
Cada juego está separado en su propia clase, lo que hace que el código esté organizado.

Las decisiones del programa, como los movimientos de la computadora, se hacen con el módulo random, para que elija opciones de forma aleatoria.

No hay inteligencia artificial ni aprendizaje automático, solo lógica estructurada.

Usé estructuras como condicionales, listas, funciones, eventos de botones y ciclos para manejar la interacción con el usuario.

🎨 5. ¿Qué aspectos visuales tiene?
Se usaron colores agradables, botones grandes y emojis para hacer cada juego más divertido y visual.

Las fuentes de texto y estilos están personalizados.

Todo está hecho para que cualquier persona pueda entender y usar el programa de forma intuitiva.

📚 6. ¿Qué aprendí o qué demuestro con este proyecto?
Con este proyecto demuestro que sé:

Programar en Python.

Usar la biblioteca Tkinter para interfaces gráficas.

Organizar el código en clases y funciones.

Aplicar lógica de juegos.

Hacer que un programa sea interactivo, visual y funcional.

Este proyecto no solo muestra varios juegos clásicos en un mismo programa, sino que también demuestra cómo se puede combinar lógica de programación con diseño visual para crear una experiencia completa para el usuario."


