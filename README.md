# 🛡️ Detección y Análisis de un Escaneo de Puertos con Nmap (TCP SYN Scan)

## 📌 Descripción

En este proyecto utilicé **Nmap** para realizar un escaneo de puertos mediante la técnica **TCP SYN Scan (-sS)** y analicé el tráfico generado utilizando **Wireshark**.

El objetivo fue identificar el comportamiento característico de este tipo de reconocimiento, analizar las respuestas del equipo objetivo y comprender cómo un analista SOC puede detectar esta actividad a partir del análisis del tráfico de red.

---

## ℹ️ Información del proyecto

| Herramienta | Nmap / Wireshark |
|-------------|------------------|
| Tipo de escaneo | TCP SYN Scan (`-sS`) |
| Objetivo | Detectar e interpretar un escaneo de puertos |
| Tipo de análisis | Captura e inspección de paquetes |
| Nivel | Principiante |

---

## 1. Detección del escaneo SYN

Como primera prueba ejecuté un **TCP SYN Scan** utilizando Nmap y capturé el tráfico generado con Wireshark.

Para visualizar únicamente los paquetes SYN enviados por el escáner apliqué el siguiente filtro:

```text
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

Este filtro permitió identificar rápidamente los intentos de conexión enviados hacia distintos puertos del equipo objetivo.

En la captura puede observarse una gran cantidad de paquetes SYN enviados en un corto período de tiempo, un comportamiento característico de un escaneo automatizado.

![Detección del escaneo SYN](evidencia/foto1_syn_scan.png)

---

## 2. Análisis de las respuestas TCP (RST)

Una vez identificados los paquetes SYN, analicé las respuestas enviadas por el equipo objetivo utilizando el siguiente filtro:

```text
tcp.flags.reset == 1
```

Las respuestas **RST** indican que los puertos consultados se encontraban cerrados, ya que el sistema rechazó inmediatamente el intento de conexión.

Este comportamiento permite identificar con facilidad los puertos que no aceptan conexiones TCP.

![Respuestas TCP RST](evidencia/foto2_rst_responses.png)

---

## 3. Análisis del volumen de tráfico (I/O Graph)

Además del análisis individual de paquetes, utilicé la herramienta **I/O Graph** de Wireshark para visualizar el comportamiento general del tráfico durante la ejecución del escaneo.

En la gráfica puede observarse un incremento significativo en la cantidad de paquetes por segundo mientras Nmap realizaba el reconocimiento.

Este tipo de comportamiento constituye un indicador útil para detectar actividades de reconocimiento dentro de una red, incluso cuando no se inspeccionan los paquetes de forma individual.

![Análisis del volumen de tráfico](evidencia/foto3_io_graph.png)

---

## 📂 Evidencias del laboratorio

| Archivo | Descripción |
|---------|-------------|
| **foto1_syn_scan.png** | Captura del filtro TCP SYN mostrando los intentos de conexión realizados por Nmap. |
| **foto2_rst_responses.png** | Respuestas TCP RST enviadas por el equipo objetivo indicando puertos cerrados. |
| **foto3_io_graph.png** | Gráfico I/O que muestra el incremento del tráfico durante la ejecución del escaneo. |

---

## 📚 Conclusiones

Este laboratorio permitió comprender el funcionamiento de un **TCP SYN Scan** y reconocer los principales indicadores que deja este tipo de actividad en una captura de red.

Durante el análisis fue posible observar que:

- Nmap envía paquetes SYN a múltiples puertos en un corto período de tiempo.
- Los puertos cerrados responden mediante paquetes TCP RST.
- El **I/O Graph** facilita la identificación de incrementos anómalos en el volumen de tráfico.
- Wireshark proporciona herramientas muy útiles para detectar actividades de reconocimiento desde una perspectiva defensiva.

Este tipo de análisis resulta útil para comprender cómo un analista SOC puede identificar una fase de reconocimiento previa a un posible intento de explotación.

---

## ❓ Preguntas frecuentes

### ¿Por qué el escaneo SYN se conoce como "Half-Open Scan"?

Porque Nmap no completa la conexión TCP.

El escáner envía un paquete **SYN** y espera la respuesta del equipo objetivo:

- Si recibe un **SYN-ACK**, interpreta que el puerto está abierto.
- Si recibe un **RST**, interpreta que el puerto está cerrado.

Posteriormente finaliza la comunicación sin completar el **Three-Way Handshake**, reduciendo la probabilidad de generar registros en la aplicación.

---

### ¿Qué significa recibir un paquete SYN-ACK?

Cuando un puerto responde con un **SYN-ACK**, significa que el servicio está escuchando conexiones TCP y que el puerto se encuentra **abierto**.

---

## 🛠️ Tecnologías utilizadas

- Nmap
- Wireshark
- TCP/IP
- TCP SYN Scan (`-sS`)
- Wireshark Display Filters
- I/O Graph

---

**Proyecto realizado con fines educativos para practicar la detección y el análisis de escaneos de puertos utilizando Nmap y Wireshark.**
