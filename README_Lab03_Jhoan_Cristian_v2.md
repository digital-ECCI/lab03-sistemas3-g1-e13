# Lab 03: Visualización de Datos en Raspberry Pi con VNC Viewer — Documentación Técnica

**Asignatura:** Sistemas Digitales III — ECCI 2026-I  
**Integrantes:** Jhoan Romero — Cristian Archila  
**Fecha:** Abril 2026

---

## 1. Descripción General

En este laboratorio se desarrolló un programa en Python que realiza dos tareas simultáneas sobre una Raspberry Pi Zero W:

1. **Lee la temperatura real del CPU** usando el comando del sistema `vcgencmd measure_temp`.
2. **Simula datos de un sensor** mediante valores aleatorios generados con la librería `random`.

Ambas fuentes de datos se grafican en **tiempo real** usando `matplotlib` en modo interactivo, visualizadas de forma remota desde un computador mediante **VNC Viewer**, sin necesidad de conectar monitor, teclado ni mouse a la Raspberry Pi una vez configurada.

---

## 2. Arquitectura del Sistema

El sistema se compone de cuatro elementos principales que interactúan entre sí a través de la red WiFi local:

```
   COMPUTADOR                     RASPBERRY PI ZERO W
  ┌─────────────┐   WiFi local   ┌──────────────────────────────────┐
  │             │  Puerto 5900   │                                  │
  │  VNC Viewer │◄──────────────►│  Servidor VNC + Script Python   │
  │             │                │  Gráfica matplotlib              │
  └─────────────┘                └──────────┬───────────────────────┘
                                            │
                           ┌────────────────┴────────────────┐
                           │                                 │
                           ▼                                 ▼
              ┌────────────────────────┐      ┌─────────────────────────┐
              │    CPU real            │      │    Sensor simulado      │
              │  vcgencmd measure_temp │      │  random.uniform(20, 80) │
              │  Temperatura en °C     │      │  Valores entre 20-80 °C │
              └────────────┬───────────┘      └──────────┬──────────────┘
                           │                             │
                           └──────────────┬──────────────┘
                                          ▼
                             ┌─────────────────────────┐
                             │   Gráfica tiempo real   │
                             │  Línea roja = CPU real  │
                             │  Línea azul = simulado  │
                             └─────────────────────────┘
```

**Descripción de cada componente:**

| Componente | Rol | Tecnología |
|---|---|---|
| Computador | Cliente visual remoto | VNC Viewer |
| Raspberry Pi Zero W | Servidor VNC + ejecución del script | Raspberry Pi OS + Python 3 |
| CPU real | Fuente de datos física | `vcgencmd measure_temp` |
| Sensor simulado | Fuente de datos virtual | `random.uniform()` |
| Gráfica | Visualización en tiempo real | `matplotlib` modo interactivo |

---

## 3. Diagrama de Flujo del Código Python

```
                       INICIO
                         │
                         ▼
           ┌─────────────────────────┐
           │   Importar librerías:   │
           │   matplotlib, subprocess│
           │   random, time          │
           └────────────┬────────────┘
                        │
                        ▼
           ┌─────────────────────────┐
           │  Configurar plt.ion()   │
           │  Modo interactivo ON    │
           │  Inicializar listas     │
           │  temp_cpu[], temp_sim[] │
           └────────────┬────────────┘
                        │
                        ▼
           ┌─────────────────────────┐
           │       while True        │◄────────────────────────────┐
           └────────────┬────────────┘                             │
                        │                                          │
                        ▼                                          │
           ┌─────────────────────────┐                            │
           │  subprocess.check_output│                            │
           │  ("vcgencmd             │                            │
           │   measure_temp")        │                            │
           │  Parsear valor °C       │                            │
           └────────────┬────────────┘                            │
                        │                                          │
                        ▼                                          │
           ┌─────────────────────────┐                            │
           │  random.uniform(20, 80) │                            │
           │  Generar valor simulado │                            │
           └────────────┬────────────┘                            │
                        │                                          │
                        ▼                                          │
           ┌─────────────────────────┐                            │
           │  Agregar a listas:      │                            │
           │  temp_cpu.append(temp)  │                            │
           │  temp_sim.append(val)   │                            │
           └────────────┬────────────┘                            │
                        │                                          │
                        ▼                                          │
           ┌─────────────────────────┐                            │
           │  plt.clf()              │                            │
           │  plt.plot(temp_cpu)     │                            │
           │  plt.plot(temp_sim)     │                            │
           │  plt.legend()           │                            │
           │  plt.draw()             │                            │
           │  plt.pause(0.1)         │                            │
           └────────────┬────────────┘                            │
                        │                                          │
                        ▼                                          │
           ┌─────────────────────────┐                            │
           │  time.sleep(0.5)        │                            │
           │  Esperar 0.5 segundos   │                            │
           └────────────┬────────────┘                            │
                        │                                          │
                        └─────────────────────────────────────────┘
                                 Vuelve al inicio del bucle
```

---

## 4. Procedimiento

### 5.1 Habilitación de VNC en la Raspberry Pi

Con monitor, teclado y mouse conectados a la Raspberry Pi, se ejecutó en terminal:

```bash
sudo raspi-config
```

Se navegó a **Interface Options → VNC → Enable**, se guardaron los cambios y se reinició:

```bash
sudo reboot
```

Luego se obtuvo la IP asignada a la Raspberry Pi en la red local:

```bash
ifconfig
```

### 5.2 Instalación de dependencias

```bash
sudo apt update
sudo apt install python3-matplotlib -y
```

### 5.3 Conexión con VNC Viewer

Se instaló VNC Viewer en el computador desde `https://www.realvnc.com/en/connect/download/viewer/`, se ingresó la IP de la Raspberry Pi y las credenciales (`pi` / contraseña configurada). Esto permitió visualizar y operar el escritorio de la Raspberry Pi de forma completamente remota.

### 5.4 Código Python — Fragmento Principal

El núcleo del programa combina la lectura real del CPU con la simulación de sensor en un único bucle de graficación en tiempo real:

```python
while True:
    # Lectura real de temperatura del CPU
    salida = subprocess.check_output(["vcgencmd", "measure_temp"])
    temp = float(salida.decode().replace("temp=", "").replace("'C\n", ""))
    temp_cpu.append(temp)

    # Sensor simulado
    temp_sim.append(random.uniform(20, 80))

    # Actualizar gráfica
    plt.clf()
    plt.plot(temp_cpu, label="CPU Real (°C)", color="red")
    plt.plot(temp_sim, label="Sensor Simulado (°C)", color="blue")
    plt.legend()
    plt.title("Temperatura en Tiempo Real")
    plt.xlabel("Muestras")
    plt.ylabel("Temperatura (°C)")
    plt.draw()
    plt.pause(0.1)

    time.sleep(0.5)
```

**¿Por qué este fragmento es el más importante?**

| Línea / Sección | Explicación |
|---|---|
| `subprocess.check_output(["vcgencmd", "measure_temp"])` | Ejecuta un comando del sistema operativo desde Python para leer la temperatura real del chip de la Raspberry Pi |
| `.replace("temp=","").replace("'C\n","")` | Limpia el texto de salida del comando para obtener solo el número en °C |
| `random.uniform(20, 80)` | Genera un valor aleatorio entre 20 y 80°C que simula la lectura de un sensor externo |
| `plt.ion()` | Activa el modo interactivo de matplotlib, permitiendo actualizar la gráfica sin detener el programa |
| `plt.clf()` | Limpia la figura antes de cada redibujado para evitar que las líneas se acumulen |
| `plt.pause(0.1)` | Da tiempo a matplotlib para renderizar la gráfica actualizada |
| `time.sleep(0.5)` | Controla la frecuencia de muestreo: una lectura cada 0.5 segundos |

---

## 5. Resultados

El programa ejecutado desde el escritorio remoto de la Raspberry Pi (vía VNC Viewer) generó una gráfica en tiempo real con dos curvas:

- **Línea roja:** temperatura real del CPU de la Raspberry Pi, con variaciones suaves según la carga del procesador.
- **Línea azul:** valores simulados del sensor con variación aleatoria entre 20°C y 80°C.

La gráfica se actualizó correctamente cada 0.5 segundos sin bloquear la ejecución del programa gracias al modo interactivo de matplotlib (`plt.ion()`). La visualización remota mediante VNC funcionó de forma estable durante toda la sesión.

---

## 6. Desafíos Encontrados

- **Configuración inicial de VNC:** La habilitación del servidor VNC requiere conectar periféricos físicos a la Raspberry Pi por primera vez, lo que puede ser una limitación si no se dispone de monitor HDMI o adaptador adecuado.
- **Parseo de la salida del comando:** La salida de `vcgencmd measure_temp` devuelve texto con formato `temp=45.1'C`, por lo que fue necesario limpiar la cadena con `.replace()` para extraer únicamente el valor numérico y evitar errores al convertirlo a `float`.
- **Rendimiento gráfico en Raspberry Pi Zero W:** Al ser un hardware de recursos limitados, el redibujado continuo de la gráfica genera una carga considerable en el procesador, lo que se refleja directamente en un aumento de la temperatura del CPU visible en la propia gráfica.
- **Sincronización de la gráfica:** Encontrar los valores correctos de `plt.pause()` y `time.sleep()` para lograr una actualización fluida sin saturar el procesador requirió ajuste y prueba.

---

## 7. Conclusiones

- VNC Viewer es una herramienta eficaz para acceder al entorno gráfico de una Raspberry Pi de forma remota, eliminando la necesidad de periféricos físicos una vez configurado.
- `matplotlib` en modo interactivo (`plt.ion()`) permite construir sistemas de monitoreo en tiempo real con pocas líneas de código, siendo ideal para prototipado rápido en sistemas embebidos.
- La combinación de datos reales (CPU) y simulados (sensor aleatorio) en la misma gráfica permite validar el comportamiento del sistema de visualización independientemente del hardware de sensado disponible.
- La Raspberry Pi Zero W, a pesar de sus limitaciones de hardware, es capaz de ejecutar scripts de visualización en tiempo real, aunque con un impacto notable en la temperatura del procesador.

---

## 8. Archivos del repositorio

| Archivo | Descripción |
|---|---|
| `main.py` | Código Python con graficación en tiempo real |
| `README.md` | Este documento de documentación técnica |
