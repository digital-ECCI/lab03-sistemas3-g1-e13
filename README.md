[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/xB5owuT7)
[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-2e0aaae1b6195c2367325f4f02e2d04e9abb55f0b24a779b69b11b9e10269abc.svg)](https://classroom.github.com/online_ide?assignment_repo_id=23120878&assignment_repo_type=AssignmentRepo)
# Lab03: Visualización interactiva de datos en Raspberry Pi usando Python y Matplotlib

## Integrantes
- Johan Romero  
- Cristian Archila 

## Documentación
Se desarrolló un script en Python que permite leer la temperatura del CPU de la Raspberry Pi utilizando el comando `vcgencmd measure_temp`.  

Los datos se procesan y se muestran en una gráfica en tiempo real usando la librería `matplotlib`.  

Se utilizó VNC Viewer para acceder de forma remota al entorno gráfico de la Raspberry Pi y ejecutar el programa.  

El script implementa programación orientada a objetos, manejo de excepciones y actualización dinámica de la gráfica.

---

## Preguntas

1. ¿Qué función cumple ```plt.fignum_exists(self.fig.number)``` en el ciclo principal?
Permite verificar si la ventana de la gráfica sigue abierta. Cuando se cierra, el ciclo termina.

---
2. ¿Por qué se usa ```time.sleep(self.intervalo)``` y qué pasa si se quita?
Controla el tiempo entre cada lectura. Si se quita, el programa consume demasiados recursos y se ejecuta demasiado rápido.

---
3. ¿Qué ventaja tiene usar ```__init__``` para inicializar listas y variables?
Permite inicializar variables automáticamente al crear el objeto, organizando mejor el código.

---
4. ¿Qué se está midiendo con ```self.inicio = time.time()```?
Se guarda el tiempo inicial para calcular el tiempo transcurrido durante la ejecución.

---
5. ¿Qué hace exactamente ```subprocess.check_output(...)```?
Ejecuta un comando del sistema y devuelve su resultado.

---

6. ¿Por qué se almacena ```ahora = time.time() - self.inicio``` en lugar del tiempo absoluto?
Para trabajar con tiempo relativo desde que inicia el programa, facilitando la lectura de la gráfica.

---

7. ¿Por qué se usa ```self.ax.clear()``` antes de graficar?
Para limpiar la gráfica anterior y evitar que las líneas se sobrepongan.

---
8. ¿Qué captura el bloque ```try...except``` dentro de ```leer_temperatura()```?
Captura errores al ejecutar el comando o procesar los datos, evitando que el programa se detenga.

---

9. ¿Cómo podría modificar el script para guardar las temperaturas en un archivo .```csv```?
Agregando una función que escriba los datos en un archivo `.csv` usando `open()` y recorriendo las listas de datos.