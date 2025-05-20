import tkinter as tk
from tkinter import ttk, messagebox, simpledialog
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.figure import Figure
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg
import matplotlib.style as mplstyle

# Aplicamos un estilo visual para las gráficas
mplstyle.use('ggplot')

class AplicacionCalculadoraRaices:
    def __init__(self, root):
        # Configuración inicial de la ventana principal
        self.root = root
        self.root.title("Calculadora Numérica - Métodos Avanzados")
        self.root.geometry("1100x780")
        self.root.minsize(900, 700)
        self.root.configure(bg="#fafafa")  # color de fondo claro para mejor visualización

        # Variables internas para controlar el estado de la aplicación
        self.raiz_calculada = False
        self.datos_iteraciones = []
        self.metodo_seleccionado = tk.StringVar(value="biseccion")  # método por defecto
        self.iter_markers = []  # para marcar puntos en la gráfica
        self.calculo_en_curso = False  # flag para evitar cálculos múltiples simultáneos
        self._after_id = None  # id para manejar llamadas repetidas con after
        self.delay_ms = tk.IntVar(value=300)  # control de velocidad para animaciones

        # Configuramos el estilo visual
        self.configurar_estilo()
        # Construimos la interfaz gráfica
        self.crear_interfaz()
        # Configuramos que la ventana sea flexible en tamaño
        self.root.grid_rowconfigure(0, weight=1)
        self.root.grid_columnconfigure(0, weight=1)

    def configurar_estilo(self):
        # Método para definir un estilo uniforme y moderno con ttk.Style
        estilo = ttk.Style()
        estilo.theme_use('clam')  # tema limpio y moderno

        # Definición de colores y fuentes para diferentes componentes
        base_bg = "#fafafa"
        base_fg = "#2c3e50"
        accent1 = "#17a2b8"  # color principal para botones y destacados
        accent2 = "#28a745"  # color para éxito
        accent3 = "#dc3545"  # color para errores
        warning = "#ffc107"  # color para advertencias

        header_font = ("Segoe UI", 16, "bold")
        self.font_normal = ("Segoe UI", 11)
        self.font_bold = ("Segoe UI", 11, "bold")

        # Configuración general para widgets
        estilo.configure(".", background=base_bg, foreground=base_fg, font=self.font_normal)
        estilo.configure("TFrame", background=base_bg)
        estilo.configure("Header.TLabel", font=header_font, background=base_bg, foreground=base_fg)
        estilo.configure("Bold.TLabel", font=self.font_bold, background=base_bg)
        estilo.configure("TLabel", font=self.font_normal, background=base_bg)
        estilo.configure("TEntry", padding=6, relief="flat", font=self.font_normal)
        estilo.map("TEntry", fieldbackground=[("!disabled", "white")])

        # Definición de estilos para botones según su tipo
        estilo.configure("Accent.TButton", font=self.font_bold, background=accent1, foreground="white")
        estilo.map("Accent.TButton", background=[('active', "#138496")])

        estilo.configure("Success.TButton", font=self.font_bold, background=accent2, foreground="white")
        estilo.map("Success.TButton", background=[('active', "#218838")])

        estilo.configure("Danger.TButton", font=self.font_bold, background=accent3, foreground="white")
        estilo.map("Danger.TButton", background=[('active', "#c82333")])

        estilo.configure("Warning.TButton", font=self.font_bold, background=warning, foreground="#212529")
        estilo.map("Warning.TButton", background=[('active', "#e0a800")])

        # Estilo para tablas (Treeview)
        estilo.configure("Treeview.Heading", background=accent1, foreground="white", font=("Segoe UI", 10, "bold"))
        estilo.configure("Treeview", font=self.font_normal, background="white", rowheight=26, fieldbackground="white")
        estilo.map("Treeview", background=[('selected', '#d4f1f9')], foreground=[('selected', base_fg)])

        estilo.configure("Horizontal.TScale", background=base_bg)

    def crear_interfaz(self):
        # Definimos el frame principal con padding para separación estética
        main_frame = ttk.Frame(self.root, padding=15)
        main_frame.grid(row=0, column=0, sticky=tk.NSEW)
        self.root.grid_rowconfigure(0, weight=1)
        self.root.grid_columnconfigure(0, weight=1)

        # Configuramos filas y columnas para un diseño flexible
        main_frame.rowconfigure(3, weight=1)
        main_frame.rowconfigure(4, weight=1)
        main_frame.columnconfigure(0, weight=1)

        # Título principal
        titulo_frame = ttk.Frame(main_frame)
        titulo_frame.grid(row=0, column=0, sticky=tk.EW, pady=(0, 15))
        ttk.Label(titulo_frame, text="Calculadora Numérica de Métodos Avanzados", style="Header.TLabel").pack(side=tk.LEFT)

        # Sección de datos de entrada con borde y espacio interno adecuado
        entrada_frame = ttk.LabelFrame(main_frame, text="Datos de Entrada", padding=12)
        entrada_frame.grid(row=1, column=0, sticky=tk.EW, pady=8)
        entrada_frame.columnconfigure(1, weight=1)
        entrada_frame.columnconfigure(3, weight=1)

        # Selección del método numérico mediante RadioButtons
        metodo_frame = ttk.Frame(entrada_frame)
        metodo_frame.grid(row=0, column=0, columnspan=6, sticky=tk.W, pady=6)
        ttk.Label(metodo_frame, text="Método:", style="Bold.TLabel").pack(side=tk.LEFT, padx=(0, 12))

        # Lista de métodos disponibles con etiquetas amigables
        metodos = [
            ("Bisección", "biseccion"),
            ("Falsa Posición", "falsa_posicion"),
            ("Secante", "secante"),
            ("Newton-Raphson", "newton_raphson"),
            ("Gauss-Seidel", "gauss_seidel"),
            ("Interpolación Lineal", "interpolacion"),
            ("Runge-Kutta 4to orden", "runge_kutta")
        ]
        # Creamos cada RadioButton asociado a la variable de selección método
        for texto, valor in metodos:
            rb = ttk.Radiobutton(metodo_frame, text=texto, variable=self.metodo_seleccionado, value=valor,
                                 command=self.update_ui_for_method)
            rb.pack(side=tk.LEFT, padx=5)

        # Entrada para la función o sistema, la cual cambia según el método
        ttk.Label(entrada_frame, text="Función f(x) o sistema:", style="Bold.TLabel").grid(row=1, column=0, sticky=tk.W, pady=4, padx=5)
        self.funcion_entry = ttk.Entry(entrada_frame, width=50)
        self.funcion_entry.grid(row=1, column=1, sticky=tk.EW, padx=5, columnspan=2)
        self.funcion_entry.insert(0, "x**3 - x - 2") # función inicial de ejemplo

        ttk.Button(entrada_frame, text="?", width=3, command=self.mostrar_ayuda_funciones).grid(row=1, column=3, sticky=tk.W)

        # Frame para contener controles dinámicos específicos para cada método
        self.params_frame = ttk.Frame(entrada_frame)
        self.params_frame.grid(row=2, column=0, columnspan=6, sticky=tk.W, pady=8, padx=5)

        # Diccionario para manejar widgets dinamicos
        self.param_widgets = {}

        # Controles para tolerancia y número máximo de iteraciones (comunes para varios métodos)
        ttk.Label(entrada_frame, text="Tolerancia:", style="Bold.TLabel").grid(row=3, column=0, sticky=tk.E, pady=4)
        self.tolerancia_entry = ttk.Entry(entrada_frame, width=15)
        self.tolerancia_entry.grid(row=3, column=1, sticky=tk.W, pady=4)
        self.tolerancia_entry.insert(0, "1e-5")  # tolerancia de ejemplo
        ttk.Label(entrada_frame, text="Iter. máx:", style="Bold.TLabel").grid(row=3, column=2, sticky=tk.E, pady=4)
        self.max_iter_entry = ttk.Entry(entrada_frame, width=15)
        self.max_iter_entry.grid(row=3, column=3, sticky=tk.W, pady=4)
        self.max_iter_entry.insert(0, "50")  # iteraciones maximas de ejemplo

        # Botones para controlar el flujo del cálculo
        control_frame = ttk.Frame(main_frame)
        control_frame.grid(row=2, column=0, sticky=tk.EW, pady=10)

        ttk.Button(control_frame, text="Verificar", style="Accent.TButton", command=self.verificar_condiciones).pack(side=tk.LEFT, padx=6)
        ttk.Button(control_frame, text="Calcular", style="Success.TButton", command=self.calcular_raiz).pack(side=tk.LEFT, padx=6)
        
        # Botón para detener un cálculo en curso
        self.stop_button = ttk.Button(control_frame, text="Detener", style="Danger.TButton",
                                      command=self.detener_calculo, state=tk.DISABLED)
        self.stop_button.pack(side=tk.LEFT, padx=6)

        ttk.Button(control_frame, text="Limpiar", style="Warning.TButton", command=self.limpiar_campos).pack(side=tk.LEFT, padx=6)

        # Control de velocidad para animación de iteraciones
        ttk.Label(control_frame, text="Velocidad (ms):").pack(side=tk.LEFT, padx=(30, 6))
        self.velocidad_scale = ttk.Scale(control_frame, from_=0, to=1000, orient=tk.HORIZONTAL,
                                        length=160, variable=self.delay_ms, style="Horizontal.TScale")
        self.velocidad_scale.pack(side=tk.LEFT)
        ttk.Label(control_frame, textvariable=self.delay_ms, width=5).pack(side=tk.LEFT, padx=6)

        # Frame para contener la gráfica y el cuadro de resultados
        centro_frame = ttk.Frame(main_frame)
        centro_frame.grid(row=3, column=0, sticky=tk.NSEW, pady=10)
        centro_frame.columnconfigure(0, weight=3)  # más espacio para gráfica
        centro_frame.columnconfigure(1, weight=1)  # espacio para resultados
        centro_frame.rowconfigure(0, weight=1)

        # Marco para la gráfica (función o iteraciones)
        grafica_frame = ttk.LabelFrame(centro_frame, text="Gráfica", padding=8)
        grafica_frame.grid(row=0, column=0, sticky=tk.NSEW, padx=(0, 8))
        grafica_frame.rowconfigure(0, weight=1)
        grafica_frame.columnconfigure(0, weight=1)

        # Objeto figura matplotlib y su widget en Tkinter para mostrar la gráfica
        self.fig = Figure(figsize=(6, 4.5), dpi=100)
        self.ax = self.fig.add_subplot(111)
        self.canvas = FigureCanvasTkAgg(self.fig, master=grafica_frame)
        self.canvas_widget = self.canvas.get_tk_widget()
        self.canvas_widget.grid(row=0, column=0, sticky=tk.NSEW)
        self.fig.tight_layout()

        # Marco para mostrar resultados numéricos del cálculo
        resultados_frame = ttk.LabelFrame(centro_frame, text="Resultados", padding=15)
        resultados_frame.grid(row=0, column=1, sticky=tk.NSEW)
        resultados_frame.columnconfigure(1, weight=1)

        # Crear etiquetas de resultados con inicialización
        etiquetas = ["Raíz / Resultado:", "f(Raíz) / Valor:", "Iteraciones:", "Error final:", "Estado:"]
        self.result_labels = {}
        for i, texto in enumerate(etiquetas):
            ttk.Label(resultados_frame, text=texto, style="Bold.TLabel").grid(row=i, column=0, sticky=tk.W, pady=6, padx=5)
            lbl = ttk.Label(resultados_frame, text="--", width=22, anchor=tk.W, font=self.font_normal)
            lbl.grid(row=i, column=1, sticky=tk.W)
            self.result_labels[texto] = lbl

        # Marco para mostrar el historial de iteraciones como tabla
        historial_frame = ttk.LabelFrame(main_frame, text="Historial de Iteraciones", padding=6)
        historial_frame.grid(row=4, column=0, sticky=tk.NSEW, pady=(10, 0))
        historial_frame.columnconfigure(0, weight=1)
        historial_frame.rowconfigure(0, weight=1)

        self.historial_tree = ttk.Treeview(historial_frame, show="headings", height=7)
        vsb = ttk.Scrollbar(historial_frame, orient="vertical", command=self.historial_tree.yview)
        hsb = ttk.Scrollbar(historial_frame, orient="horizontal", command=self.historial_tree.xview)
        self.historial_tree.configure(yscrollcommand=vsb.set, xscrollcommand=hsb.set)
        self.historial_tree.grid(row=0, column=0, sticky=tk.NSEW)
        vsb.grid(row=0, column=1, sticky=tk.NS)
        hsb.grid(row=1, column=0, sticky=tk.EW)

        # Colores para filas pares e impares para mejor legibilidad
        self.historial_tree.tag_configure('even', background='#e9f7fa')
        self.historial_tree.tag_configure('odd', background='white')

        # Barra de estado al pie para mensajes generales
        self.barra_estado = ttk.Label(main_frame, text="Listo.", relief=tk.SUNKEN, anchor=tk.W, padding=(5, 3))
        self.barra_estado.grid(row=5, column=0, sticky=tk.EW, pady=(5, 0))

        # Preparamos la UI para el método seleccionado inicialmente
        self.update_ui_for_method()

    def update_ui_for_method(self, *args):
        """
        Actualiza la interfaz para mostrar los controles de entrada
        específicos al método numérico seleccionado.
        """
        metodo = self.metodo_seleccionado.get()
        # Limpiamos controles dinámicos antes de crear nuevos
        for widget in self.params_frame.winfo_children():
            widget.destroy()
        self.param_widgets.clear()

        # Limpiamos historial e inicializamos resultados
        self.historial_tree.delete(*self.historial_tree.get_children())
        for lbl in self.result_labels.values():
            lbl.config(text="--")
        self.barra_estado.config(text=f"Método seleccionado: {metodo.replace('_', ' ').title()}")
        self.raiz_calculada = False

        # Dependiendo del método mostramos controles específicos
        if metodo in ["biseccion", "falsa_posicion"]:
            ttk.Label(self.params_frame, text="Intervalo a:", style="Bold.TLabel").grid(row=0, column=0, sticky=tk.W, padx=4, pady=4)
            a_ent = ttk.Entry(self.params_frame, width=12)
            a_ent.grid(row=0, column=1, sticky=tk.W, padx=4, pady=4)
            a_ent.insert(0, "1")

            ttk.Label(self.params_frame, text="b:", style="Bold.TLabel").grid(row=0, column=2, sticky=tk.W, padx=4, pady=4)
            b_ent = ttk.Entry(self.params_frame, width=12)
            b_ent.grid(row=0, column=3, sticky=tk.W, padx=4, pady=4)
            b_ent.insert(0, "2")

            self.param_widgets.update({"a": a_ent, "b": b_ent})

            cols = ("Iter", "a", "b", "c", "f(c)", "Error")

        elif metodo == "secante":
            ttk.Label(self.params_frame, text="x0:", style="Bold.TLabel").grid(row=0, column=0, sticky=tk.W, padx=4, pady=4)
            x0_ent = ttk.Entry(self.params_frame, width=12)
            x0_ent.grid(row=0, column=1, sticky=tk.W, padx=4, pady=4)
            x0_ent.insert(0, "1")

            ttk.Label(self.params_frame, text="x1:", style="Bold.TLabel").grid(row=0, column=2, sticky=tk.W, padx=4, pady=4)
            x1_ent = ttk.Entry(self.params_frame, width=12)
            x1_ent.grid(row=0, column=3, sticky=tk.W, padx=4, pady=4)
            x1_ent.insert(0, "1.5")

            self.param_widgets.update({"x0": x0_ent, "x1": x1_ent})

            cols = ("Iter", "x(i-1)", "x(i)", "x(i+1)", "f(xi+1)", "Error")

        elif metodo == "newton_raphson":
            ttk.Label(self.params_frame, text="x0:", style="Bold.TLabel").grid(row=0, column=0, sticky=tk.W, padx=4, pady=4)
            x0_ent = ttk.Entry(self.params_frame, width=12)
            x0_ent.grid(row=0, column=1, sticky=tk.W, padx=4, pady=4)
            x0_ent.insert(0, "1")

            self.param_widgets.update({"x0": x0_ent})

            cols = ("Iter", "x(i)", "f(xi)", "f'(xi)", "x(i+1)", "Error")

        elif metodo == "gauss_seidel":
            # Para el método Gauss-Seidel pedimos el sistema de Ecuaciones Ax=b
            ttk.Label(self.params_frame, text="Sistema Ax = b", style="Bold.TLabel").grid(row=0, column=0, sticky=tk.W, pady=4)
            ttk.Button(self.params_frame, text="Ingresar Sistema...",
                       command=self.ingresar_sistema_gauss_seidel).grid(row=0, column=1, sticky=tk.W, padx=10)

            cols = ("Iter", "x Vector", "Error")

        elif metodo == "interpolacion":
            # Para interpolación ingresamos puntos x, puntos y y valor x a interpolar
            ttk.Label(self.params_frame, text="Puntos x (separados por coma):", style="Bold.TLabel").grid(row=0, column=0, sticky=tk.W, padx=4, pady=4)
            x_ent = ttk.Entry(self.params_frame, width=30)
            x_ent.grid(row=0, column=1, sticky=tk.W, padx=4, pady=4)
            x_ent.insert(0, "1,2,3,4")

            ttk.Label(self.params_frame, text="Puntos y (separados por coma):", style="Bold.TLabel").grid(row=1, column=0, sticky=tk.W, padx=4, pady=4)
            y_ent = ttk.Entry(self.params_frame, width=30)
            y_ent.grid(row=1, column=1, sticky=tk.W, padx=4, pady=4)
            y_ent.insert(0, "1,4,9,16")

            ttk.Label(self.params_frame, text="Valor para interpolar:", style="Bold.TLabel").grid(row=2, column=0, sticky=tk.W, padx=4, pady=4)
            xi_ent = ttk.Entry(self.params_frame, width=15)
            xi_ent.grid(row=2, column=1, sticky=tk.W, padx=4, pady=4)
            xi_ent.insert(0, "2.5")

            self.param_widgets.update({"x_points": x_ent, "y_points": y_ent, "x_interp": xi_ent})

            cols = ("Interpolación",)

        elif metodo == "runge_kutta":
            # Parámetros para resolver EDO con Runge-Kutta 4to orden
            ttk.Label(self.params_frame, text="Función f(t,y):", style="Bold.TLabel").grid(row=0, column=0, sticky=tk.W, pady=4)
            ttk.Label(self.params_frame, text="Ejemplo: t + y", font=("Segoe UI", 9, "italic")).grid(row=1, column=0, sticky=tk.W)
            funcion_edo = ttk.Entry(self.params_frame, width=40)
            funcion_edo.grid(row=0, column=1, sticky=tk.W, pady=4, padx=4, rowspan=2)
            funcion_edo.insert(0, "t + y")

            ttk.Label(self.params_frame, text="Valor y(t0):", style="Bold.TLabel").grid(row=2, column=0, sticky=tk.W, pady=4)
            y0_ent = ttk.Entry(self.params_frame, width=15)
            y0_ent.grid(row=2, column=1, sticky=tk.W, pady=4)

            ttk.Label(self.params_frame, text="Tiempo inicial t0:", style="Bold.TLabel").grid(row=3, column=0, sticky=tk.W, pady=4)
            t0_ent = ttk.Entry(self.params_frame, width=15)
            t0_ent.grid(row=3, column=1, sticky=tk.W, pady=4)

            ttk.Label(self.params_frame, text="Tiempo final tfinal:", style="Bold.TLabel").grid(row=4, column=0, sticky=tk.W, pady=4)
            tfinal_ent = ttk.Entry(self.params_frame, width=15)
            tfinal_ent.grid(row=4, column=1, sticky=tk.W, pady=4)

            ttk.Label(self.params_frame, text="Paso h:", style="Bold.TLabel").grid(row=5, column=0, sticky=tk.W, pady=4)
            h_ent = ttk.Entry(self.params_frame, width=15)
            h_ent.grid(row=5, column=1, sticky=tk.W, pady=4)

            y0_ent.insert(0, "1")
            t0_ent.insert(0, "0")
            tfinal_ent.insert(0, "2")
            h_ent.insert(0, "0.1")

            self.param_widgets.update({
                "function_edo": funcion_edo,
                "y0": y0_ent,
                "t0": t0_ent,
                "tfinal": tfinal_ent,
                "h": h_ent,
            })

            cols = ("Paso", "t", "y")

        else:
            cols = ("Iter", "a", "b", "c", "f(c)", "Error")

        # Configuramos columnas de tabla de iteraciones para el historial
        self.historial_tree.configure(columns=cols, show="headings")
        for col in cols:
            header_text = col
            self.historial_tree.heading(col, text=header_text)
            if col in ["Iter", "Paso"]:
                self.historial_tree.column(col, width=70, anchor="center")
            elif col == "Error":
                self.historial_tree.column(col, width=110, anchor="e")
            else:
                self.historial_tree.column(col, width=100, anchor="center")

    def ingresar_sistema_gauss_seidel(self):
        """
        Permite al usuario ingresar la matriz A y vector b para resolver sistema Ax=b.
        Se abre un dialogo con entradas para cada elemento.
        """
        n = simpledialog.askinteger("Sistema Gauss-Seidel", "Ingrese el tamaño del sistema (n x n):", minvalue=2, maxvalue=10, parent=self.root)
        if n is None:
            return
        self.sistema_n = n

        self.A = np.zeros((n, n))
        self.b_vec = np.zeros(n)

        self.dialog_sistema = tk.Toplevel(self.root)
        self.dialog_sistema.title("Ingresar sistema Ax=b")
        self.dialog_sistema.grab_set()

        # Crear etiquetas columna para matriz A y vector b
        for j in range(n):
            ttk.Label(self.dialog_sistema, text=f"a[{j+1}]", font=self.font_bold).grid(row=0, column=j + 1, padx=4, pady=3)
        ttk.Label(self.dialog_sistema, text="b", font=self.font_bold).grid(row=0, column=n + 1, padx=6, pady=3)

        self.entries_A = []
        self.entries_b = []

        # Crear entradas para toda la matriz A y vector b
        for i in range(n):
            ttk.Label(self.dialog_sistema, text=f"Fila {i+1}:", font=self.font_bold).grid(row=i + 1, column=0, padx=6, pady=4, sticky=tk.E)
            fila_entries = []
            for j in range(n):
                ent = ttk.Entry(self.dialog_sistema, width=8)
                ent.grid(row=i + 1, column=j + 1, padx=2, pady=2)
                fila_entries.append(ent)
            self.entries_A.append(fila_entries)

            ent_b = ttk.Entry(self.dialog_sistema, width=10)
            ent_b.grid(row=i + 1, column=n + 1, padx=6, pady=2)
            self.entries_b.append(ent_b)

        ttk.Button(self.dialog_sistema, text="Guardar", command=self.guardar_sistema_gauss_seidel).grid(row=n + 2, column=0, columnspan=n + 2, pady=10)

    def guardar_sistema_gauss_seidel(self):
        """
        Lee los valores ingresados del sistema y los almacena en las variables A y b.
        """
        try:
            for i in range(self.sistema_n):
                for j in range(self.sistema_n):
                    val = float(self.entries_A[i][j].get())
                    self.A[i, j] = val
                self.b_vec[i] = float(self.entries_b[i].get())
            self.barra_estado.config(text=f"Sistema guardado: {self.sistema_n}x{self.sistema_n}")
            self.dialog_sistema.destroy()
        except Exception as e:
            messagebox.showerror("Error", f"Error al ingresar sistema: {str(e)}")

    def evaluar_funcion(self, x):
        """
        Evalúa la función matemática ingresada en función del valor x.
        Se permite uso limitado de funciones numéricas de numpy.
        """
        try:
            expresion = self.funcion_entry.get()
            allowed_names = {'np': np, 'x': x, 'sin': np.sin, 'cos': np.cos, 'tan': np.tan,
                             'exp': np.exp, 'log': np.log, 'log10': np.log10, 'sqrt': np.sqrt,
                             'abs': np.abs, 'pi': np.pi, 'e': np.e}
            return eval(expresion, {"__builtins__": {}}, allowed_names)
        except Exception as e:
            messagebox.showerror("Error Evaluación", f"f({x}) inválido.\n{str(e)}")
            return None

    def evaluar_derivada(self, x):
        """
        Calcula la derivada numérica aproximada de la función en un punto x usando diferencia central.
        """
        try:
            f = self.evaluar_funcion
            h = 1e-7  # pequeño incremento para derivada
            f_ph, f_mh = f(x + h), f(x - h)
            if f_ph is None or f_mh is None: return None
            if abs(h) < 1e-15: return None
            return (f_ph - f_mh) / (2 * h)
        except:
            return None

    def mostrar_ayuda_funciones(self):
        """
        Muestra ayuda básica para ingresar funciones en la entrada de texto.
        """
        ayuda = (
            "Operadores: +, -, *, /, ** (potencia)\n"
            "Constantes: pi, e\n"
            "Funciones: sin, cos, tan, exp, log (ln), log10, sqrt, abs\n"
            "Ejemplo: x**3 - x - 2\n"
            "Para Gauss-Seidel debe ingresar el sistema con botón 'Ingresar sistema...'"
        )
        messagebox.showinfo("Ayuda - Funciones", ayuda)

    def verificar_condiciones(self):
        """
        Verifica que las condiciones iniciales y entradas sean correctas para el método seleccionado.
        Esto incluye validación de intervalos, entradas numéricas, y estructura del sistema.
        """
        metodo = self.metodo_seleccionado.get()
        try:
            if metodo in ["biseccion", "falsa_posicion", "secante", "newton_raphson"]:
                if not self.funcion_entry.get():
                    messagebox.showwarning("Advertencia", "Debe ingresar una función f(x).")
                    return False
                # Validar entradas numéricas necesarias según método
                if metodo in ["biseccion", "falsa_posicion"]:
                    a = float(self.param_widgets["a"].get())
                    b = float(self.param_widgets["b"].get())
                    if a >= b:
                        messagebox.showwarning("Advertencia", "Debe cumplirse a < b.")
                        return False
                    fa = self.evaluar_funcion(a)
                    fb = self.evaluar_funcion(b)
                    if fa is None or fb is None:
                        return False
                    if fa * fb > 0:
                        messagebox.showwarning("Condiciones", f"f(a) * f(b) > 0.\nf({a})={fa}, f({b})={fb}")
                        return False
                elif metodo == "secante":
                    x0 = float(self.param_widgets["x0"].get())
                    x1 = float(self.param_widgets["x1"].get())
                    if x0 == x1:
                        messagebox.showwarning("Advertencia", "x0 debe ser distinto de x1.")
                        return False
                elif metodo == "newton_raphson":
                    x0 = float(self.param_widgets["x0"].get())
                    f0 = self.evaluar_funcion(x0)
                    df0 = self.evaluar_derivada(x0)
                    if df0 is None or abs(df0) < 1e-10:
                        messagebox.showwarning("Advertencia", "La derivada en x0 es nula o muy pequeña.")
                        return False

            elif metodo == "gauss_seidel":
                # Verificar que sistema haya sido definido
                if not hasattr(self, "A") or not hasattr(self, "b_vec"):
                    messagebox.showwarning("Advertencia", "Debe ingresar el sistema Ax=b primero.")
                    return False
                if self.A.shape[0] != self.A.shape[1]:
                    messagebox.showwarning("Error", "La matriz A debe ser cuadrada.")
                    return False
                if self.A.shape[0] != self.b_vec.size:
                    messagebox.showwarning("Error", "Dimensión de A y b no coinciden.")
                    return False
                # Comprobar diagonal dominante para Gauss-Seidel es recomendable
                for i in range(len(self.A)):
                    suma_filas = np.sum(np.abs(self.A[i])) - abs(self.A[i, i])
                    if abs(self.A[i, i]) < suma_filas:
                        messagebox.showwarning("Advertencia",
                                               f"Fila {i + 1} no es diagonalmente dominante. Gauss-Seidel puede no converger.")
                        return True
                return True

            elif metodo == "interpolacion":
                # Validar entrada de puntos x, y y punto a interpolar
                try:
                    x_str = self.param_widgets["x_points"].get().strip()
                    y_str = self.param_widgets["y_points"].get().strip()
                    xi_str = self.param_widgets["x_interp"].get().strip()

                    x_vals = [float(val) for val in x_str.split(",")]
                    y_vals = [float(val) for val in y_str.split(",")]
                    xi_val = float(xi_str)

                    if len(x_vals) != len(y_vals):
                        messagebox.showwarning("Error", "Los puntos x e y deben tener la misma cantidad.")
                        return False
                    if xi_val < min(x_vals) or xi_val > max(x_vals):
                        messagebox.showwarning("Error", "El valor de interpolación debe estar en el rango dado.")
                        return False
                    return True

                except Exception as e:
                    messagebox.showwarning("Error", f"Error en datos de interpolación: {str(e)}")
                    return False

            elif metodo == "runge_kutta":
                # Validamos datos para resolver ecuación diferencial
                try:
                    func_str = self.param_widgets["function_edo"].get().strip()
                    y0 = float(self.param_widgets["y0"].get())
                    t0 = float(self.param_widgets["t0"].get())
                    tf = float(self.param_widgets["tfinal"].get())
                    h = float(self.param_widgets["h"].get())
                    if h <= 0 or tf <= t0:
                        messagebox.showwarning("Error", "Paso h debe ser > 0 y tfinal debe ser mayor que t0.")
                        return False
                    # Probar evaluar función con datos iniciales
                    test_val = eval(func_str, {"__builtins__": None}, {"t": t0, "y": y0, "np": np})
                except Exception as e:
                    messagebox.showwarning("Error", f"Error en datos para Runge-Kutta: {str(e)}")
                    return False
                return True

            return True

        except Exception as e:
            messagebox.showerror("Error", f"Error verificando condiciones: {str(e)}")
            return False

    def graficar_funcion(self, a_vis, b_vis, raiz=None, iter_data=None, plot_function=True, metodo=None):
        """
        Función que grafica la función matemática, los puntos de iteración y marca la raíz final.
        Aplica diferentes gráficas para los diversos métodos integrados.
        """
        try:
            # Limpiamos marcadores previos
            if self.iter_markers:
                for marker in self.iter_markers:
                    try:
                        marker.remove()
                    except:
                        pass
            self.iter_markers = []

            if plot_function and metodo in ["biseccion", "falsa_posicion", "secante", "newton_raphson", "interpolacion"]:
                self.ax.clear()
                # Graficamos función para ventanas [a_vis, b_vis]
                x_vals = np.linspace(a_vis, b_vis, 400)
                y_vals = []
                valid_x = []
                for x in x_vals:
                    y = self.evaluar_funcion(x)
                    if y is not None and np.isfinite(y):
                        y_vals.append(y)
                        valid_x.append(x)
                    else:
                        if valid_x:
                            self.ax.plot(valid_x, y_vals, 'b-', linewidth=2, alpha=0.8)
                        valid_x, y_vals = [], []
                if valid_x:
                    self.ax.plot(valid_x, y_vals, 'b-', linewidth=2, alpha=0.8, label='f(x)')
                self.ax.axhline(0, color='black', linewidth=0.9, alpha=0.4)
                self.ax.set_xlabel("x")
                self.ax.set_ylabel("f(x)")
                self.ax.set_title("Gráfica de la función")
                self.ax.grid(True, linestyle='--', alpha=0.5)

                # Marcamos iteraciones para métodos específicos
                if iter_data:
                    if metodo in ["biseccion", "falsa_posicion"]:
                        if all(k in iter_data for k in ['a', 'b', 'c', 'fa', 'fb', 'fc']):
                            a, b, c = iter_data['a'], iter_data['b'], iter_data['c']
                            fa, fb, fc = iter_data['fa'], iter_data['fb'], iter_data['fc']
                            l_a, = self.ax.plot([a, a], [0, fa], 'r:', lw=1.5)
                            l_b, = self.ax.plot([b, b], [0, fb], 'g:', lw=1.5)
                            p_a, = self.ax.plot(a, fa, 'ro', ms=7, alpha=0.8)
                            p_b, = self.ax.plot(b, fb, 'go', ms=7, alpha=0.8)
                            p_c, = self.ax.plot(c, fc, 'mo', ms=9, label=f'c≈{c:.4f}')
                            l_c, = self.ax.plot([c, c], [0, fc], 'm--', lw=1.5, alpha=0.8)
                            self.iter_markers.extend([l_a, l_b, p_a, p_b, p_c, l_c])
                            if metodo == "falsa_posicion":
                                l_fp, = self.ax.plot([a, b], [fa, fb], 'k--', lw=1.3, alpha=0.5)
                                self.iter_markers.append(l_fp)
                    elif metodo == "secante":
                        if all(k in iter_data for k in ['x0', 'x1', 'x2', 'f0', 'f1']):
                            x0, x1, x2 = iter_data['x0'], iter_data['x1'], iter_data['x2']
                            f0, f1 = iter_data['f0'], iter_data['f1']
                            f2 = self.evaluar_funcion(x2) or 0
                            l_sec, = self.ax.plot([x0, x1], [f0, f1], 'k--', lw=1.5, alpha=0.6)
                            p0, = self.ax.plot(x0, f0, 'co', ms=7, alpha=0.8)
                            p1, = self.ax.plot(x1, f1, 'yo', ms=7, alpha=0.8)
                            p2, = self.ax.plot(x2, f2, 'mo', ms=9, label=f'x₂≈{x2:.4f}')
                            l_x2, = self.ax.plot([x2, x2], [0, f2], 'm--', lw=1.5, alpha=0.7)
                            self.iter_markers.extend([l_sec, p0, p1, p2, l_x2])
                    elif metodo == "newton_raphson":
                        if all(k in iter_data for k in ['x0', 'x1', 'f0', 'df0']):
                            x0, x1 = iter_data['x0'], iter_data['x1']
                            f0, df0 = iter_data['f0'], iter_data['df0']
                            f1 = self.evaluar_funcion(x1) or 0
                            tan_x = np.linspace(x0 - 1, x1 + 1, 20)
                            tan_y = df0 * (tan_x - x0) + f0
                            l_tan, = self.ax.plot(tan_x, tan_y, 'k--', lw=1.5, alpha=0.6)
                            p0, = self.ax.plot(x0, f0, 'co', ms=7)
                            p1, = self.ax.plot(x1, f1, 'mo', ms=9, label=f'x₁≈{x1:.4f}')
                            l_x1, = self.ax.plot([x1, x1], [0, f1], 'm--', lw=1.5, alpha=0.7)
                            self.iter_markers.extend([l_tan, p0, p1, l_x1])

            elif metodo == "runge_kutta" and plot_function and iter_data:
                # Graficar evolución y(t) vs t
                self.ax.clear()
                t_vals = iter_data.get("t_vals", [])
                y_vals = iter_data.get("y_vals", [])
                if t_vals and y_vals and len(t_vals) == len(y_vals):
                    self.ax.plot(t_vals, y_vals, 'b-o', linewidth=2, markersize=5, alpha=0.9)
                    self.ax.set_title("Solución EDO (Runge-Kutta 4to Orden)")
                    self.ax.set_xlabel("t")
                    self.ax.set_ylabel("y(t)")
                    self.ax.grid(True, linestyle='--', alpha=0.6)
                else:
                    self.ax.text(0.5, 0.5, "No hay datos para graficar.",
                                 ha='center', va='center', fontsize=12)
            else:
                # Mensaje si no hay gráfica disponible o método no soportado
                self.ax.clear()
                self.ax.text(0.5, 0.5, "No hay gráfica disponible para este método.",
                             ha='center', va='center', fontsize=14)
            # Marcamos la raíz final si está definida
            if raiz is not None:
                f_val = self.evaluar_funcion(raiz)
                if f_val is not None and np.isfinite(f_val):
                    self.ax.plot(raiz, f_val, 'r*', ms=12, label=f'Raíz ≈ {raiz:.6f}')
                    self.ax.axvline(x=raiz, color='r', linestyle=':', lw=1.7, alpha=0.85)
            # Generamos una leyenda limpia y sin repeticiones
            handles, labels = self.ax.get_legend_handles_labels()
            unique = {}
            for h, l in zip(handles, labels):
                if l not in unique:
                    unique[l] = h
            if unique:
                self.ax.legend(unique.values(), unique.keys(), fontsize=9, loc='best')
            self.fig.tight_layout()
        except Exception as e:
            print(f"Error en graficar_funcion: {e}")

    def calcular_raiz(self):
        """
        Función principal que invoca los métodos numéricos para el cálculo de raíces,
        maneja la animación paso a paso y actualiza la interfaz.
        """
        if self.calculo_en_curso:
            messagebox.showwarning("Advertencia", "Cálculo ya en progreso. Deténgalo primero.")
            return

        if not self.verificar_condiciones():
            self.barra_estado.config(text="Verificación fallida.")
            return

        try:
            metodo = self.metodo_seleccionado.get()
            tolerancia = float(self.tolerancia_entry.get())
            max_iter = int(self.max_iter_entry.get())
            if tolerancia <= 0 or max_iter <= 0:
                raise ValueError("Tolerancia e iteraciones deben ser > 0")
        except Exception as e:
            messagebox.showerror("Error", f"Parámetros inválidos: {e}")
            return

        try:
            self.calculo_en_curso = True
            self.stop_button.config(state=tk.NORMAL)
            self.barra_estado.config(text=f"Iniciando cálculo: {metodo.replace('_', ' ').title()}...")
            self.root.update_idletasks()

            # Limpiar historial y resultados iniciales
            self.historial_tree.delete(*self.historial_tree.get_children())
            for lbl in self.result_labels.values():
                lbl.config(text="--")

            # Determinar rango para graficar si aplica
            a_vis, b_vis = 0, 1
            if metodo in ["biseccion", "falsa_posicion"]:
                a_vis = float(self.param_widgets["a"].get())
                b_vis = float(self.param_widgets["b"].get())
            elif metodo == "secante":
                x0 = float(self.param_widgets["x0"].get())
                x1 = float(self.param_widgets["x1"].get())
                a_vis, b_vis = min(x0, x1), max(x0, x1)
            elif metodo == "newton_raphson":
                x0 = float(self.param_widgets["x0"].get())
                a_vis, b_vis = x0 - 1, x0 + 1

            # Seleccionar y ejecutar método correspondiente
            if metodo == "biseccion":
                generator = self.generador_biseccion(tolerancia, max_iter, a_vis, b_vis)
                self._ejecutar_paso_iteracion(generator)

            elif metodo == "falsa_posicion":
                generator = self.generador_falsa_posicion(tolerancia, max_iter, a_vis, b_vis)
                self._ejecutar_paso_iteracion(generator)

            elif metodo == "secante":
                generator = self.generador_secante(tolerancia, max_iter, a_vis, b_vis)
                self._ejecutar_paso_iteracion(generator)

            elif metodo == "newton_raphson":
                generator = self.generador_newton_raphson(tolerancia, max_iter, a_vis, b_vis)
                self._ejecutar_paso_iteracion(generator)

            elif metodo == "gauss_seidel":
                x0_vec = np.zeros(self.A.shape[0])
                resultado, iter_count = self.metodo_gauss_seidel(self.A, self.b_vec, x0_vec, tolerancia, max_iter)

                self.result_labels["Raíz / Resultado:"].config(text=np.array2string(resultado, precision=6))
                self.result_labels["Iteraciones:"].config(text=str(iter_count))
                self.result_labels["Estado:"].config(text="Convergencia" if iter_count < max_iter else "Max iteraciones")
                self.barra_estado.config(text="Cálculo Gauss-Seidel finalizado.")
                self.calculo_en_curso = False
                self.stop_button.config(state=tk.DISABLED)

                # Insertar resultado en historial como vector solución
                self.historial_tree.configure(columns=("VectorX", ""))
                self.historial_tree.heading("VectorX", text="Vector X (Resultado)")
                self.historial_tree.column("VectorX", width=400, anchor="w")
                self.historial_tree.insert('', 'end', values=(np.array2string(resultado, precision=6), ""))

            elif metodo == "interpolacion":
                x_points = [float(valor.strip()) for valor in self.param_widgets["x_points"].get().split(",")]
                y_points = [float(valor.strip()) for valor in self.param_widgets["y_points"].get().split(",")]
                x_interp = float(self.param_widgets["x_interp"].get())

                val_interp = self.metodo_interpolacion(x_points, y_points, x_interp)
                if val_interp is None:
                    self.barra_estado.config(text="Valor a interpolar fuera de rango.")
                    messagebox.showwarning("Error", "Valor fuera del rango de puntos.")
                    self.calculo_en_curso = False
                    self.stop_button.config(state=tk.DISABLED)
                    return

                self.result_labels["Raíz / Resultado:"].config(text=f"x = {x_interp:.6f}")
                self.result_labels["f(Raíz) / Valor:"].config(text=f"Interpolado = {val_interp:.6f}")
                self.result_labels["Estado:"].config(text="Interpolación completada")
                self.barra_estado.config(text="Interpolación completada.")

                # Graficar datos de interpolación con puntos y línea
                self.ax.clear()
                self.ax.plot(x_points, y_points, 'bo-', label="Datos")
                self.ax.plot(x_interp, val_interp, 'r*', ms=12, label="Interpolado")
                self.ax.set_title("Interpolación lineal")
                self.ax.set_xlabel("x")
                self.ax.set_ylabel("y")
                self.ax.grid(True, linestyle='--', alpha=0.6)
                self.ax.legend()
                self.canvas.draw()

                self.calculo_en_curso = False
                self.stop_button.config(state=tk.DISABLED)

                # Insertar resultado simple en historial
                self.historial_tree.configure(columns=("x interp", "y interp"))
                self.historial_tree.heading("x interp", text="x")
                self.historial_tree.heading("y interp", text="Interpolado y")
                self.historial_tree.column("x interp", width=100, anchor="center")
                self.historial_tree.column("y interp", width=120, anchor="center")
                self.historial_tree.insert('', 'end', values=(f"{x_interp:.6f}", f"{val_interp:.6f}"))

            elif metodo == "runge_kutta":
                func_str = self.param_widgets["function_edo"].get()
                y0 = float(self.param_widgets["y0"].get())
                t0 = float(self.param_widgets["t0"].get())
                tfinal = float(self.param_widgets["tfinal"].get())
                h = float(self.param_widgets["h"].get())

                # Definimos la función f(t,y) para evaluar la EDO
                def f(t, y):
                    try:
                        return eval(func_str, {"__builtins__": None}, {"t": t, "y": y, "np": np,
                                                                      "sin": np.sin, "cos": np.cos,
                                                                      "exp": np.exp, "log": np.log,
                                                                      "sqrt": np.sqrt, "pi": np.pi})
                    except:
                        return 0

                # Resolvemos paso a paso usando Runge-Kutta 4to orden sin animación intermedia
                t_vals = [t0]
                y_vals = [y0]
                t = t0
                y = y0
                while t < tfinal:
                    k1 = h * f(t, y)
                    k2 = h * f(t + h / 2, y + k1 / 2)
                    k3 = h * f(t + h / 2, y + k2 / 2)
                    k4 = h * f(t + h, y + k3)
                    y = y + (k1 + 2 * k2 + 2 * k3 + k4) / 6
                    t += h
                    t_vals.append(t)
                    y_vals.append(y)
                self.result_labels["Raíz / Resultado:"].config(text=f"y({tfinal:.4f}) ≈ {y:.6f}")
                self.result_labels["Estado:"].config(text="Runge-Kutta completado")
                self.barra_estado.config(text="Runge-Kutta terminado con éxito.")

                iter_data = {"t_vals": t_vals, "y_vals": y_vals}
                self.graficar_funcion(None, None, iter_data=iter_data, plot_function=True, metodo=metodo)
                self.canvas.draw()

                self.calculo_en_curso = False
                self.stop_button.config(state=tk.DISABLED)

                # Insertar pasos de Runge-Kutta en el historial para seguimiento detallado
                self.historial_tree.configure(columns=("Paso", "t", "y"))
                self.historial_tree.heading("Paso", text="Paso")
                self.historial_tree.heading("t", text="t")
                self.historial_tree.heading("y", text="y")
                self.historial_tree.column("Paso", width=55, anchor="center")
                self.historial_tree.column("t", width=100, anchor="center")
                self.historial_tree.column("y", width=150, anchor="center")

                for i, (t_val, y_val) in enumerate(zip(t_vals, y_vals)):
                    self.historial_tree.insert("", "end", values=(i, f"{t_val:.6f}", f"{y_val:.6f}"))

            else:
                messagebox.showerror("Error", f"Método '{metodo}' no implementado.")
                self.finalizar_calculo("Error método")
                return

        except Exception as e:
            messagebox.showerror("Error", f"Error al iniciar cálculo: {e}")
            self.finalizar_calculo("Error inicial")

    # Generadores son funciones que "pausan" la ejecución para animación paso a paso.

    def generador_biseccion(self, tol, max_iter, a, b):
        """
        Implementa el método de Bisección generador para permitir animación y mostrar iteraciones.
        """
        iteracion = 0
        c_old = a
        fa = self.evaluar_funcion(a)
        fb = self.evaluar_funcion(b)
        while iteracion < max_iter:
            c = (a + b) / 2
            fc = self.evaluar_funcion(c)
            error = abs(c - c_old) if iteracion > 0 else float('inf')

            tag = 'even' if iteracion % 2 == 0 else 'odd'
            self.historial_tree.insert("", "end", values=(
                iteracion + 1, f"{a:.7f}", f"{b:.7f}", f"{c:.7f}", f"{fc:.3e}", f"{error:.3e}" if error != float('inf') else "---"
            ), tags=(tag,))

            iter_data = {'a': a, 'b': b, 'c': c, 'fa': fa, 'fb': fb, 'fc': fc}
            self.graficar_funcion(a, b, iter_data=iter_data, plot_function=False, metodo="biseccion")
            self.canvas.draw()
            self.barra_estado.config(text=f"Bisección - Iter {iteracion+1}, c={c:.5f}, Error={error:.2e}")

            yield

            if error < tol or abs(fc) < 1e-12:
                estado = "Convergencia"
                break

            if fa * fc < 0:
                b = c
                fb = fc
            else:
                a = c
                fa = fc

            c_old = c
            iteracion += 1
        else:
            estado = "Max iteraciones"

        raiz = c
        f_raiz = self.evaluar_funcion(raiz)
        yield
        raise StopIteration({"raiz": raiz, "f_raiz": f_raiz, "iter_count": iteracion + 1, "error": error, "estado": estado})

    def generador_falsa_posicion(self, tol, max_iter, a, b):
        """
        Implementa el método de Falsa Posición como generador para animar las iteraciones.
        """
        iteracion = 0
        c_old = a
        fa = self.evaluar_funcion(a)
        fb = self.evaluar_funcion(b)
        while iteracion < max_iter:
            if abs(fb - fa) < 1e-15:
                estado = "Estancamiento"
                break
            c = (a * fb - b * fa) / (fb - fa)
            fc = self.evaluar_funcion(c)
            error = abs(c - c_old) if iteracion > 0 else float('inf')

            tag = 'even' if iteracion % 2 == 0 else 'odd'
            self.historial_tree.insert("", "end", values=(
                iteracion + 1, f"{a:.7f}", f"{b:.7f}", f"{c:.7f}", f"{fc:.3e}", f"{error:.3e}"
            ), tags=(tag,))

            iter_data = {'a': a, 'b': b, 'c': c, 'fa': fa, 'fb': fb, 'fc': fc}
            self.graficar_funcion(a, b, iter_data=iter_data, plot_function=False, metodo="falsa_posicion")
            self.canvas.draw()
            self.barra_estado.config(text=f"Falsa Posición - Iter {iteracion+1}, c={c:.5f}, Error={error:.2e}")

            yield

            if error < tol or abs(fc) < 1e-12:
                estado = "Convergencia"
                break

            if fa * fc < 0:
                b = c
                fb = fc
            else:
                a = c
                fa = fc

            c_old = c
            iteracion += 1
        else:
            estado = "Max iteraciones"

        raiz = c
        f_raiz = self.evaluar_funcion(raiz)
        yield
        raise StopIteration({"raiz": raiz, "f_raiz": f_raiz, "iter_count": iteracion + 1, "error": error, "estado": estado})

    def generador_secante(self, tol, max_iter, a_vis, b_vis):
        """
        Implementa el método de Secante como generador para mostrar iteraciones paso a paso.
        """
        x0 = float(self.param_widgets["x0"].get())
        x1 = float(self.param_widgets["x1"].get())
        iteracion = 0
        error = float('inf')
        f0 = self.evaluar_funcion(x0)
        f1 = self.evaluar_funcion(x1)
        while iteracion < max_iter:
            if abs(f1 - f0) < 1e-15:
                estado = "Estancamiento"
                break
            x2 = x1 - f1 * (x1 - x0) / (f1 - f0)
            f2 = self.evaluar_funcion(x2)
            if f2 is None:
                estado = "Error en función f(x2)"
                break
            error = abs(x2 - x1)

            tag = 'even' if iteracion % 2 == 0 else 'odd'
            self.historial_tree.insert("", "end", values=(
                iteracion + 1, f"{x0:.7f}", f"{x1:.7f}", f"{x2:.7f}", f"{f2:.3e}", f"{error:.3e}"
            ), tags=(tag,))

            iter_data = {'x0': x0, 'x1': x1, 'x2': x2, 'f0': f0, 'f1': f1}
            self.graficar_funcion(a_vis, b_vis, iter_data=iter_data, plot_function=False, metodo="secante")
            self.canvas.draw()
            self.barra_estado.config(text=f"Secante - Iter {iteracion+1}, x2={x2:.5f}, Error={error:.2e}")

            yield

            if error < tol or abs(f2) < 1e-12:
                estado = "Convergencia"
                break

            x0, f0 = x1, f1
            x1, f1 = x2, f2
            iteracion += 1
        else:
            estado = "Max iteraciones"

        raiz = x1
        f_raiz = self.evaluar_funcion(raiz)
        yield
        raise StopIteration({"raiz": raiz, "f_raiz": f_raiz, "iter_count": iteracion + 1, "error": error, "estado": estado})

    def generador_newton_raphson(self, tol, max_iter, a_vis, b_vis):
        """
        Implementa el método de Newton-Raphson con generador para animar iteraciones.
        """
        x0 = float(self.param_widgets["x0"].get())
        iteracion = 0
        error = float('inf')
        while iteracion < max_iter:
            f0 = self.evaluar_funcion(x0)
            df0 = self.evaluar_derivada(x0)
            if f0 is None or df0 is None or abs(df0) < 1e-15:
                estado = "Error función o derivada"
                break
            x1 = x0 - f0 / df0
            error = abs(x1 - x0)

            tag = 'even' if iteracion % 2 == 0 else 'odd'
            self.historial_tree.insert("", "end", values=(
                iteracion + 1, f"{x0:.7f}", f"{f0:.3e}", f"{df0:.3e}", f"{x1:.7f}", f"{error:.3e}"
            ), tags=(tag,))

            iter_data = {'x0': x0, 'x1': x1, 'f0': f0, 'df0': df0}
            self.graficar_funcion(a_vis, b_vis, iter_data=iter_data, plot_function=False, metodo="newton_raphson")
            self.canvas.draw()
            self.barra_estado.config(text=f"Newton-Raphson - Iter {iteracion+1}, x1={x1:.5f}, Error={error:.2e}")

            yield

            if error < tol or abs(f0) < 1e-12:
                estado = "Convergencia"
                break

            x0 = x1
            iteracion += 1
        else:
            estado = "Max iteraciones"

        raiz = x1
        f_raiz = self.evaluar_funcion(raiz)
        yield
        raise StopIteration({"raiz": raiz, "f_raiz": f_raiz, "iter_count": iteracion + 1, "error": error, "estado": estado})

    def metodo_gauss_seidel(self, A, b, x0=None, tolerancia=1e-10, max_iter=100):
        """
        Implementa el método numérico iterativo de Gauss-Seidel para resolver sistemas lineales Ax = b.
        x0: vector inicial, tolerancia y iter max controlan la convergencia.
        """
        n = len(b)
        x = np.zeros_like(b) if x0 is None else x0.copy()
        for it_count in range(max_iter):
            x_old = x.copy()
            for i in range(n):
                sum1 = np.dot(A[i, :i], x[:i])   # suma para coeficientes antes de i (actualizados esta iteración)
                sum2 = np.dot(A[i, i+1:], x_old[i+1:])  # suma para coeficientes después de i (iter pasada)
                if A[i, i] == 0:
                    raise ZeroDivisionError("Elemento nulo en diagonal principal.")
                x[i] = (b[i] - sum1 - sum2) / A[i, i]  # actualizar resultado para x_i
            # Verificamos si la solución ha convergido dentro de la tolerancia dada
            if np.linalg.norm(x - x_old, ord=np.inf) < tolerancia:
                return x, it_count + 1  # retorna vector solución y número iteraciones
        return x, max_iter  # retorna última aproximación si no convergió en max_iter

    def metodo_interpolacion(self, x, y, x_interp):
        """
        Implementa interpolación lineal simple para conjuntos de datos conocidos.
        Busca el segmento donde está x_interp y calcula el valor interpolado.
        """
        n = len(x)
        for i in range(n - 1):
            if x[i] <= x_interp <= x[i + 1]:
                # Fórmula lineal para interpolar entre puntos i y i+1
                return y[i] + (y[i + 1] - y[i]) * (x_interp - x[i]) / (x[i + 1] - x[i])
        return None  # si x_interp no está en el rango proporcionado

    def detener_calculo(self):
        """
        Función para detener el cálculo en ejecución.
        Cancela la próxima iteración programada y actualiza estado.
        """
        if self._after_id:
            self.root.after_cancel(self._after_id)
            self._after_id = None
        self.calculo_en_curso = False
        self.stop_button.config(state=tk.DISABLED)
        self.barra_estado.config(text="Cálculo detenido por usuario.")

    def _ejecutar_paso_iteracion(self, generator):
        """
        Ejecuta un paso del generador (método iterativo) y programa
        la siguiente iteración tras un delay configurable.
        """
        if not self.calculo_en_curso:
            return
        try:
            next(generator)
            delay = self.delay_ms.get()
            self._after_id = self.root.after(delay, self._ejecutar_paso_iteracion, generator)
        except StopIteration as e:
            resultado_final = e.value
            if resultado_final is None:
                resultado_final = {}
            self.finalizar_calculo(resultado_final.get("estado", "Completado"), resultado_final)
        except Exception as e:
            messagebox.showerror("Error Iteración", f"Error durante iteración:\n{e}")
            self.finalizar_calculo("Error iteración")

    def finalizar_calculo(self, estado_final, resultados=None):
        """
        Finaliza el cálculo: actualiza indicadores, estado interface, resultados y botón.
        """
        self.calculo_en_curso = False
        self._after_id = None
        self.stop_button.config(state=tk.DISABLED)
        self.barra_estado.config(text=f"Cálculo finalizado ({estado_final}).")

        if resultados:  # si se reciben resultados
            raiz = resultados.get("raiz")
            f_raiz = resultados.get("f_raiz")
            iter_count = resultados.get("iter_count")
            error = resultados.get("error")

            if raiz is not None: self.result_labels["Raíz / Resultado:"].config(text=f"{raiz:.8f}" if not isinstance(raiz, np.ndarray) else np.array2string(raiz, precision=6))
            if f_raiz is not None: self.result_labels["f(Raíz) / Valor:"].config(text=f"{f_raiz:.2e}" if isinstance(f_raiz, (float,int)) else str(f_raiz))
            if iter_count is not None: self.result_labels["Iteraciones:"].config(text=str(iter_count))
            if error is not None:
                error_text = f"{error:.2e}" if (not isinstance(error, str) and error != float('inf')) else "N/A"
                self.result_labels["Error final:"].config(text=error_text)
            self.result_labels["Estado:"].config(text=estado_final)

        # Auto desplazamiento al final del historial para mostrar info última iteración
        if self.historial_tree.get_children():
            last_item = self.historial_tree.get_children()[-1]
            self.historial_tree.see(last_item)

    def limpiar_campos(self):
        """
        Limpia campos de entrada, tablas y gráficos,
        dejando la app lista para nuevo cálculo.
        """
        if self.calculo_en_curso:
            self.detener_calculo()
        self.funcion_entry.delete(0, tk.END)
        self.funcion_entry.insert(0, "x**3 - x - 2")
        self.tolerancia_entry.delete(0, tk.END)
        self.tolerancia_entry.insert(0, "1e-5")
        self.max_iter_entry.delete(0, tk.END)
        self.max_iter_entry.insert(0, "50")
        self.delay_ms.set(300)
        for widget in self.param_widgets.values():
            widget.delete(0, tk.END)
        self.param_widgets.clear()
        self.raiz_calculada = False
        self.barra_estado.config(text="Campos reiniciados.")
        self.historial_tree.delete(*self.historial_tree.get_children())
        self.ax.clear()
        self.ax.set_title("Gráfica de función")
        self.ax.set_xlabel("x")
        self.ax.set_ylabel("f(x)")
        self.ax.grid(True, linestyle='--', alpha=0.5)
        self.canvas.draw()
        self.metodo_seleccionado.set("biseccion")
        self.update_ui_for_method()

def main():
    root = tk.Tk()
    try:
        style = ttk.Style(root)
        if 'clam' in style.theme_names():
            style.theme_use('clam')
    except tk.TclError:
        pass
    app = AplicacionCalculadoraRaices(root)
    root.mainloop()

if __name__ == "__main__":
    main()
