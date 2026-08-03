# Wireshark-Nmap-PortScan-Detection
Análisis defensivo con Wireshark para la detección de escaneos de puertos con Nmap y análisis de flags TCP.

# Detección y Análisis de un Escaneo de Puertos con Nmap (SYN Scan)

## Descripción

En este proyecto utilicé **Nmap** para realizar un escaneo de puertos mediante la técnica **TCP SYN Scan** y analicé el tráfico generado utilizando **Wireshark**.

El objetivo fue identificar el comportamiento característico de este tipo de reconocimiento, interpretar las respuestas del equipo objetivo y comprender cómo un analista SOC puede detectar este tipo de actividad a partir de una captura de red.

---

## Información del proyecto

| Herramienta | Nmap / Wireshark |
|-------------|------------------|
| Tipo de escaneo | TCP SYN Scan (`-sS`) |
| Objetivo | Detectar e interpretar un escaneo de puertos |
| Tipo de análisis | Captura e inspección de paquetes |
| Nivel | Principiante |

---

# 1. Detección del escaneo SYN

Como primera prueba ejecuté un **TCP SYN Scan** utilizando Nmap y capturé todo el tráfico generado con Wireshark.

Para visualizar únicamente los paquetes SYN enviados por el escáner utilicé el siguiente filtro:

```text
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

Este filtro permitió identificar rápidamente los intentos de conexión enviados hacia distintos puertos del equipo objetivo.

En la captura puede observarse una gran cantidad de paquetes SYN enviados en pocos milisegundos, un comportamiento característico de un escaneo automatizado.

![Escaneo SYN detectado](evidencia/foto1_syn_scan.png)

---

# 2. Análisis de las respuestas del servidor

Después analicé las respuestas enviadas por el equipo objetivo utilizando el siguiente filtro:

```text
tcp.flags.reset == 1
```

Los paquetes **RST** indican que los puertos consultados estaban cerrados y que el sistema rechazó inmediatamente los intentos de conexión iniciados por Nmap.

Este comportamiento permite identificar con rapidez cuáles son los puertos que no aceptan conexiones TCP.

![Respuestas TCP RST](evidencia/foto2_rst_responses.jpg)

---

# 3. Reconstrucción del flujo TCP

Además de inspeccionar los paquetes individuales, utilicé la herramienta **Follow TCP Stream** para reconstruir la comunicación entre ambos equipos.

La reconstrucción permite observar cómo Nmap inicia la conexión enviando un paquete **SYN**, recibe la respuesta correspondiente y finaliza inmediatamente la comunicación sin completar el **Three-Way Handshake**.

Este comportamiento es precisamente el que da origen al nombre **Half-Open Scan**, ya que la conexión nunca llega a establecerse completamente.

![Reconstrucción del flujo TCP](evidencia/TCP%20Stream.jpg)

---

# 4. Análisis del volumen de tráfico

Por último utilicé la herramienta **I/O Graph** de Wireshark para representar gráficamente la cantidad de paquetes capturados durante el escaneo.

La gráfica muestra un incremento repentino del tráfico mientras Nmap realiza el reconocimiento de puertos. Este tipo de patrón resulta útil para detectar actividades automatizadas dentro de una red y puede servir como indicador de un posible reconocimiento previo a un ataque.

![Gráfico de tráfico generado por el escaneo](evidencia/foto3_io_graph.png)

---

# Conclusiones

Este proyecto permitió comprender cómo funciona un **TCP SYN Scan** y cuáles son los indicadores que deja registrados en una captura de red.

Durante el análisis fue posible observar que:

- Nmap envía múltiples paquetes SYN hacia distintos puertos en un intervalo muy corto de tiempo.
- Los puertos cerrados responden con paquetes TCP RST.
- La comunicación nunca completa el Three-Way Handshake, característica propia del Half-Open Scan.
- El incremento repentino en el volumen de tráfico facilita la detección de este tipo de actividad mediante herramientas de monitoreo.
- Wireshark permite identificar este comportamiento utilizando filtros específicos y herramientas como **Follow TCP Stream** e **I/O Graph**.

Este tipo de análisis ayuda a comprender cómo un analista SOC puede detectar actividades de reconocimiento antes de que un atacante intente explotar una vulnerabilidad.

---

# Preguntas frecuentes

## ¿Por qué el escaneo SYN se conoce como "Half-Open Scan"?

Porque Nmap no completa la conexión TCP.

El escáner envía un paquete **SYN** y espera la respuesta del equipo objetivo.

- Si recibe un **SYN-ACK**, interpreta que el puerto está abierto.
- Si recibe un **RST**, interpreta que el puerto está cerrado.

Después de recibir la respuesta, Nmap envía un paquete **RST**, finalizando la comunicación antes de completar el **Three-Way Handshake**.

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
- Follow TCP Stream
- I/O Graph
- Wireshark Display Filters

---

**Proyecto realizado con fines educativos para practicar la detección y el análisis de escaneos de puertos utilizando Nmap y Wireshark.**
