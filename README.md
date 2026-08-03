# Detección y Análisis de un Escaneo de Puertos con Nmap (TCP SYN Scan)

## Descripción

En este proyecto utilicé **Nmap** para realizar un escaneo de puertos mediante la técnica **TCP SYN Scan** y analicé el tráfico generado utilizando **Wireshark**.

El objetivo fue identificar el comportamiento característico de este tipo de reconocimiento, observar las respuestas del equipo objetivo y comprender cómo un analista SOC puede detectar esta actividad a partir del análisis del tráfico de red.

---

## Información del proyecto

| Herramienta | Nmap / Wireshark |
|-------------|------------------|
| Tipo de escaneo | TCP SYN Scan (`-sS`) |
| Objetivo | Detectar e interpretar un escaneo de puertos |
| Tipo de análisis | Captura e inspección de paquetes |
| Nivel | Principiante |

---

## 1. Detección del escaneo SYN

Como primera prueba ejecuté un **TCP SYN Scan** utilizando Nmap y capturé todo el tráfico generado con Wireshark.

Para visualizar únicamente los paquetes utilizados durante el reconocimiento apliqué el siguiente filtro:

```text
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

Este filtro permitió identificar los paquetes **SYN** enviados por el equipo atacante hacia distintos puertos del equipo objetivo.

Al revisar la captura fue posible observar una gran cantidad de solicitudes enviadas en un intervalo muy corto de tiempo, un comportamiento característico de un escaneo automatizado.

![Detección del escaneo SYN](evidencia/foto1_syn_scan.png)

---

## 2. Análisis de las respuestas TCP (RST)

Después analicé las respuestas enviadas por el equipo objetivo utilizando el siguiente filtro:

```text
tcp.flags.reset == 1
```

Las respuestas **RST** indican que los puertos consultados se encontraban cerrados, ya que el sistema rechazó inmediatamente los intentos de conexión iniciados por Nmap.

Este comportamiento permite identificar rápidamente cuáles son los puertos que no aceptan conexiones TCP.

![Respuestas TCP RST](evidencia/foto2_rst_responses.png)

---

## 3. Reconstrucción del flujo TCP

Para complementar el análisis, utilicé la herramienta **Follow TCP Stream** de Wireshark con el objetivo de reconstruir la comunicación entre el equipo que ejecutó el escaneo y el host analizado.

Aunque un **TCP SYN Scan** no completa el **Three-Way Handshake**, esta vista permite observar la secuencia de paquetes intercambiados y comprender cómo responde el sistema durante el proceso de reconocimiento.

Esta herramienta resulta especialmente útil para seguir una conversación específica y analizar el comportamiento de una conexión desde una perspectiva más detallada.

![Reconstrucción del flujo TCP](evidencia/foto3_tcp_stream.png)

---

## 4. Análisis del volumen de tráfico

Además de inspeccionar los paquetes individuales, utilicé la herramienta **I/O Graph** de Wireshark para visualizar el comportamiento general del tráfico durante la ejecución del escaneo.

En la gráfica puede observarse un incremento significativo en la cantidad de paquetes por segundo mientras Nmap realizaba el reconocimiento.

Este tipo de comportamiento constituye un indicador útil para detectar actividades de escaneo dentro de una red, incluso cuando no se inspeccionan los paquetes de forma individual.

![Análisis del volumen de tráfico](evidencia/foto4_io_graph.png)

---

# Conclusiones

Este proyecto permitió comprender cómo funciona un **TCP SYN Scan** y reconocer los principales indicadores que deja este tipo de actividad en una captura de red.

Durante el análisis fue posible observar que:

- Nmap envía paquetes SYN a múltiples puertos en un corto período de tiempo.
- Los puertos cerrados responden mediante paquetes TCP RST.
- **Follow TCP Stream** facilita el análisis de la comunicación entre los equipos involucrados.
- **I/O Graph** permite detectar incrementos anómalos en el volumen de tráfico durante un escaneo.
- Wireshark proporciona herramientas muy útiles para identificar este tipo de actividad desde una perspectiva defensiva.

Este tipo de análisis resulta útil para comprender cómo un analista SOC puede detectar actividades de reconocimiento antes de que un atacante intente explotar una vulnerabilidad.

---

# Preguntas frecuentes

## ¿Por qué el escaneo SYN se conoce como "Half-Open Scan"?

Porque Nmap no completa la conexión TCP.

El escáner envía un paquete **SYN** y espera la respuesta del equipo objetivo.

- Si recibe un **SYN-ACK**, interpreta que el puerto está abierto.
- Si recibe un **RST**, interpreta que el puerto está cerrado.

Después de recibir la respuesta, Nmap envía un paquete **RST**, finalizando la comunicación sin completar el **Three-Way Handshake**.

---

## ¿Qué significa recibir un paquete SYN-ACK?

Cuando un puerto responde con un **SYN-ACK**, significa que el servicio está escuchando conexiones TCP y que el puerto se encuentra **abierto**.

---

## Tecnologías utilizadas

- Nmap
- Wireshark
- TCP
- TCP SYN Scan
- TCP RST
- Wireshark Display Filters
- Follow TCP Stream
- I/O Graph

---

**Proyecto realizado con fines educativos para practicar la detección y el análisis de escaneos de puertos utilizando Nmap y Wireshark.**
