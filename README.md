# Proyecto de Programacion
Proyecto de Maria Isabel Kuan, Alejandra Parra e Isabel Ruiz Yepes ❀⸜(˶´ ˘ `˶)⸝❀
#-----Definir la función e importar datos-----#
def graficar(ruta, x, y):
    plt.figure()
    with open(ruta, "r") as file:          
                                            
        filas = file.read().split("\n")
        columns = filas[0].split(",")
        clasificacion = {}
        for variable in columns:
            clasificacion[variable] = []
        print(clasificacion)
 
        del(filas[0])
 
        filas = limpiar_datos([f.split(",") for f in filas], columns)
 
        for fila in filas:
            print(fila)
            for columna_index in range(len(columns)):
                clasificacion[columns[columna_index]].append(fila[columna_index])  # FIX 2: era wine[...], debe ser clasificacion[...]
 
    columna_categoria = clasificacion[columns[0]]  # Columna para clasificar los puntos por color
    columna3 = clasificacion[x]                    # Eje X (se pasa como argumento)
    columna4 = clasificacion[y]                    # Eje Y (se pasa como argumento)
 
    # Detectar automáticamente las categorías únicas y asignarles un color
    colores_disponibles = ["r", "g", "b", "c", "m", "y", "orange", "purple", "brown", "pink"]
    categorias_unicas = []
    for cat in columna_categoria:
        if cat not in categorias_unicas:
            categorias_unicas.append(cat)
 
    mapa_colores = {}
    for i in range(len(categorias_unicas)):
        mapa_colores[categorias_unicas[i]] = colores_disponibles[i % len(colores_disponibles)]
 
    for dato in range(len(columna_categoria)):
        color = mapa_colores[columna_categoria[dato]]
        plt.scatter(float(columna3[dato]),
                    float(columna4[dato]),
                    color=color
                    )
 
    # Leyenda automática con los colores y categorías
    for cat in categorias_unicas:
        plt.scatter([], [], color=mapa_colores[cat], label=cat)
    plt.legend()
    plt.show()
 
graficar("ruta_de_acceso.csv", "columna3", "columna4")
