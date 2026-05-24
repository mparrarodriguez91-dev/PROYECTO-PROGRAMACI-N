# Proyecto de Programacion
Proyecto de Maria Isabel Kuan, Alejandra Parra e Isabel Ruiz Yepes ❀⸜(˶´ ˘ `˶)⸝❀
#-----Definir la función e importar datos-----#
import matplotlib.pyplot as plt
 
#-----Limpieza de datos-----#
def limpiar_datos(filas, columns):
    filas_limpias = []
    filas_vistas = []       # Para recordar qué filas ya pasaron y detectar duplicados
 
    for fila in filas:
        # Filtro 1: si la fila está completamente vacía, la saltamos
        if fila == [] or fila == [""]:
            continue
 
        # Filtro 2: si la fila ya la habíamos visto antes, es duplicado, la saltamos
        if fila in filas_vistas:
            continue
 
        # Filtro 3: revisamos dato por dato si hay alguno vacío (sería un NaN)
        fila_valida = True
        for dato in fila:
            if dato.strip() == "":  # strip() quita espacios para que "  " también cuente como vacío
                fila_valida = False
                break               # Con un solo dato vacío ya es suficiente para descartar la fila
 
        # Solo si pasó los 3 filtros la guardamos
        if fila_valida:
            filas_limpias.append(fila)
            filas_vistas.append(fila)
 
    return filas_limpias
 
 
#-----Función para gráfica de dispersión-----#
def graficar(ruta, x, y):
    plt.figure()                                         # Crea la ventana de la gráfica
    with open(ruta, "r", encoding="utf-8-sig") as file: # Abre el CSV, encoding evita problemas con tildes
        filas = file.read().split("\n")                  # Lee todo el archivo y lo parte por saltos de línea
        columns = filas[0].split(",")                    # La primera línea son los nombres de las columnas
        clasificacion = {}                               # Diccionario donde cada columna tendrá su lista de datos
        for variable in columns:
            clasificacion[variable] = []                 # Crea una lista vacía por cada columna
 
        del(filas[0])  # Borra el encabezado porque ya guardamos los nombres de columnas arriba
 
        # Parte cada fila por comas para convertirla de string a lista, luego la limpia
        filas = limpiar_datos([f.split(",") for f in filas], columns)
 
 
        # Llena el diccionario clasificacion con los datos de cada fila
        for fila in filas:
            datos = fila
            for columna_index in range(len(columns)):
                clasificacion[columns[columna_index]].append(datos[columna_index])
 
    columna1 = clasificacion[columns[0]]  # Columna para clasificar los puntos por color
    columna2 = clasificacion[columns[1]]
    columna3 = clasificacion[x]           # Eje X (se pasa como argumento)
    columna4 = clasificacion[y]           # Eje Y (se pasa como argumento)
 
    # Lista de colores pastel disponibles
    colores_disponibles = ["#FFB3BA", "#B3ECFF", "#B3FFB3", "#FFD9B3",
                           "#FFFFB3", "#C5B3FF", "#FFB3F0", "#B3FFF0"]
 
    # Recorre columna2 y guarda cada categoría nueva que encuentre
    categorias_unicas = []
    for categoria in columna2:
        if categoria not in categorias_unicas:
            categorias_unicas.append(categoria)
 
    # Le asigna un color pastel a cada categoría
    # % len(colores_disponibles) hace que si hay más categorías que colores, vuelva al primero
    mapa_colores = {}
    for i in range(len(categorias_unicas)):
        mapa_colores[categorias_unicas[i]] = colores_disponibles[i % len(colores_disponibles)]
 
    # Dibuja cada punto con el color que le corresponde según su categoría
    for dato in range(len(columna1)):
        plt.scatter(float(columna3[dato]),
                    float(columna4[dato]),
                    color=mapa_colores[columna2[dato]]
                    )
 
    # Leyenda automática: puntos vacíos solo para registrar el color en la leyenda
    for categoria in categorias_unicas:
        plt.scatter([], [], color=mapa_colores[categoria], label=categoria)
    plt.legend()
    plt.show()
 
 
#-----Función para gráfica de barras simples-----#
def graficar_barras(ruta, col_x):
    plt.figure(figsize=(10, 6))             # Crea la ventana de la gráfica
    with open(ruta, "r", encoding="utf-8-sig") as file:
        filas = file.read().split("\n")
        columns = filas[0].strip().split(",")
        clasificacion = {}
        for variable in columns:
            clasificacion[variable] = []
 
        del(filas[0])
 
        filas = limpiar_datos([f.strip().split(",") for f in filas], columns)
 
        for fila in filas:
            datos = fila
            for columna_index in range(len(columns)):
                if columna_index < len(fila):
                    clasificacion[columns[columna_index]].append(datos[columna_index])
 
    columna_x = clasificacion[col_x]        # La columna que queremos contar
 
    # Contamos cuántas veces aparece cada valor en col_x
    conteo = {}
    for valor in columna_x:
        if valor not in conteo:
            conteo[valor] = 0
        conteo[valor] += 1
 
    # Sacamos los valores únicos ordenados
    x_unicas = sorted(conteo.keys())
 
    # Colores pastel automáticos — uno por barra
    colores_disponibles = ["#FFB3BA", "#B3ECFF", "#B3FFB3", "#FFD9B3",
                           "#FFFFB3", "#C5B3FF", "#FFB3F0", "#B3FFF0"]
    colores = []
    for i in range(len(x_unicas)):
        colores.append(colores_disponibles[i % len(colores_disponibles)])
 
    # Dibuja una barra por cada valor único
    valores = [conteo[x] for x in x_unicas]
    plt.bar(x_unicas, valores, color=colores, edgecolor="white", linewidth=0.5)
 
    plt.xlabel(col_x)
    plt.ylabel("Conteo")
    plt.title(f"Distribución de {col_x}")
    plt.xticks(rotation=45, ha="right", fontsize=8)
    plt.tight_layout()
    plt.show()
  #-----Función para gráfica de barras apiladas-----#
def graficar_barras_apiladas(ruta, col_x, col_color):
    plt.figure(figsize=(15, 7))             # Más ancha porque suele haber muchas categorías en X
    with open(ruta, "r", encoding="utf-8-sig") as file:
        filas = file.read().split("\n")
        columns = filas[0].strip().split(",")
        clasificacion = {}
        for variable in columns:
            clasificacion[variable] = []
 
        del(filas[0])
 
        filas = limpiar_datos([f.strip().split(",") for f in filas], columns)
 
        for fila in filas:
            datos = fila
            for columna_index in range(len(columns)):
                if columna_index < len(fila):
                    clasificacion[columns[columna_index]].append(datos[columna_index])
 
    columna_x     = clasificacion[col_x]        # Eje X: las categorías principales
    columna_color = clasificacion[col_color]    # Lo que colorea las barras
 
    # Contamos cuántas veces aparece cada combinación col_x + col_color
    # conteo["Cat"]["Lazy"] = 5 significa "hay 5 gatos lazy"
    conteo = {}
    for i in range(len(columna_x)):
        cx = columna_x[i]
        cc = columna_color[i]
        if cx not in conteo:            # Si la categoría X no existe aún, la creamos
            conteo[cx] = {}
        if cc not in conteo[cx]:        # Si la categoría color no existe para esa X, la creamos
            conteo[cx][cc] = 0
        conteo[cx][cc] += 1             # Sumamos 1 al contador
 
    # Sacamos los valores únicos ordenados
    x_unicas = []
    for e in columna_x:
        if e not in x_unicas:
            x_unicas.append(e)
    x_unicas = sorted(x_unicas)
 
    color_unicas = []
    for c in columna_color:
        if c not in color_unicas:
            color_unicas.append(c)
    color_unicas = sorted(color_unicas)
 
    # Colores pastel automáticos
    colores_disponibles = ["#FFB3BA", "#B3ECFF", "#B3FFB3", "#FFD9B3",
                           "#FFFFB3", "#C5B3FF", "#FFB3F0", "#B3FFF0"]
    mapa_colores = {}
    for i in range(len(color_unicas)):
        mapa_colores[color_unicas[i]] = colores_disponibles[i % len(colores_disponibles)]
 
    # bases guarda desde dónde empieza cada barra — arranca en 0 para todas las categorías X
    bases = [0] * len(x_unicas)
    for color in color_unicas:
        valores = []
        for cx in x_unicas:
            if cx in conteo and color in conteo[cx]:
                valores.append(conteo[cx][color])
            else:
                valores.append(0)   # Si esa combinación no existe, ponemos 0
 
        # Dibuja las barras de este color encima de las anteriores (bottom=bases)
        plt.bar(x_unicas, valores, bottom=bases,
                color=mapa_colores[color], label=color,
                edgecolor="white", linewidth=0.5)
 
        # Subimos la base para que el siguiente color empiece donde este terminó
        for i in range(len(bases)):
            bases[i] += valores[i]
 
    # Leyenda automática
    plt.legend(title=col_color, bbox_to_anchor=(1.01, 1), loc="upper left")
    plt.xlabel(col_x)
    plt.ylabel("Conteo")
    plt.title(f"Barras apiladas: {col_x} por {col_color}")
    plt.xticks(rotation=45, ha="right", fontsize=8)
    plt.tight_layout()
    plt.show()
    
