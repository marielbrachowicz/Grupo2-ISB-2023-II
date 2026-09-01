# Procesamiento Digital de Señales — Filtros digitales

## 1. Idea principal

Un **filtro digital** modifica selectivamente componentes de frecuencia de una señal. Su objetivo puede ser eliminar ruido, restaurar señales, separar espectros o extraer características.

Ejemplo de la clase:

- Señal original: componentes de 100 Hz + 350 Hz.
- Filtro **pasa bajas (low-pass)**.
- Señal procesada: queda principalmente la componente de 100 Hz.

> Un filtro no necesariamente elimina "toda" una frecuencia de forma perfecta: atenúa o deja pasar según su respuesta en frecuencia.

---

## 2. Tipos de filtros según su respuesta frecuencial

| Filtro | Qué deja pasar |
|---|---|
| **Pasa bajas (Low-pass)** | Frecuencias bajas |
| **Pasa altas (High-pass)** | Frecuencias altas |
| **Pasa banda (Band-pass)** | Una banda de frecuencias |
| **Rechaza banda / Notch** | Rechaza una banda específica |

---

## 3. Frecuencia de corte y respuesta en frecuencia

La **frecuencia de corte** indica aproximadamente dónde empieza la transición entre la banda que queremos conservar y la banda que queremos atenuar.

La respuesta en frecuencia se analiza principalmente mediante:

- **Magnitud** → cuánto se amplifica o atenúa cada frecuencia.
- **Fase** → cuánto cambia la fase de cada componente.

La magnitud suele expresarse en dB:

\[
|H(f)|_{dB}=20\log_{10}|H(f)|
\]

---

# 4. Señales: ejemplo importante

Este código genera dos senoidales:

- `x1` → 120 Hz
- `x2` → 20 Hz

Luego las suma:

\[
x_3=x_1+x_2
\]

Finalmente se observa su espectro de magnitud.

## Código completo

```python
import numpy as np
import matplotlib.pyplot as plt

# Señal 1: 120 Hz
f1 = 120
w1 = 2*np.pi*f1
A = 1
phi = 0

fs = 1e3
ts = 1/fs

n = np.arange(1000)
t = n*ts

x1 = A*np.sin(w1*t + phi)

plt.plot(x1)
plt.xlim(0,50)
plt.show()


# Señal 2: 20 Hz
f2 = 20
w2 = 2*np.pi*f2
A = 1
phi = 0

fs = 1e3
ts = 1/fs

n = np.arange(1000)
t = n*ts

x2 = A*np.sin(w2*t + phi)

plt.plot(x2)
plt.xlim(0,50)
plt.show()


# Suma de las señales
x3 = x1 + x2

plt.plot(x3)
plt.xlim(0,250)
plt.show()


# Espectro de magnitud
plt.magnitude_spectrum(x3, scale="dB", Fs=fs)
plt.show()
```

## ¿Qué debes observar?

### x1 — 120 Hz

![Señal de 120 Hz](x1_120Hz.png)

Es una señal sinusoidal de frecuencia 120 Hz.

### x2 — 20 Hz

![Señal de 20 Hz](x2_20Hz.png)

Tiene una frecuencia menor, por lo que oscila más lentamente.

### x3 = x1 + x2

![Suma de señales](x3_suma.png)

La señal resultante contiene **las dos componentes de frecuencia**.

### Espectro de x3

![Espectro de x3](espectro_x3.png)

En el espectro deben aparecer componentes alrededor de:

- **20 Hz**
- **120 Hz**

Esto es muy importante: en el dominio del tiempo vemos una señal combinada; en frecuencia podemos identificar qué componentes la forman.

---

# 5. Filtros analógicos vs digitales

| Analógicos | Digitales |
|---|---|
| Señales continuas | Señales discretas |
| RLC / Op-amps | DSP / MCU |
| Ecuaciones diferenciales | Ecuaciones en diferencias |
| Más sensibles a temperatura | Más reproducibles |
| Difíciles de reconfigurar | Reprogramables |

---

# 6. Convolución

La **convolución** es una operación fundamental del filtrado digital.

Para un sistema LTI:

\[
y[n]=x[n]*h[n]
\]

donde:

- `x[n]` = señal de entrada
- `h[n]` = respuesta al impulso
- `y[n]` = señal de salida

La respuesta al impulso es muy importante porque **caracteriza completamente un sistema LTI**.

---

# 7. Función de transferencia

La función de transferencia permite describir el comportamiento de un sistema en frecuencia.

Para un sistema digital se trabaja con:

\[
H(z)=\frac{Y(z)}{X(z)}
\]

Los **polos y ceros** ayudan a entender el comportamiento del filtro.

### Polos y ceros

- **Ceros** → atenúan determinadas frecuencias.
- **Polos** → pueden producir amplificación o resonancia.
- **Estabilidad** → todos los polos deben estar dentro del círculo unidad.

---

# 8. Filtros FIR

**FIR = Finite Impulse Response**

Características:

- No recursivos.
- Solo ceros.
- Pueden tener fase lineal.
- Son siempre estables.
- Pueden interpretarse como un promedio ponderado de muestras pasadas.

### Estructura

Un FIR utiliza:

- Delays.
- Multiplicadores.
- Sumadores.

### Ventajas

- Estabilidad garantizada.
- Fase lineal.
- Robustez numérica.
- Implementación sencilla.

### Desventajas

- Pueden necesitar un orden alto.
- Mayor uso de memoria.
- Mayor costo computacional.

Son especialmente relevantes en sistemas embebidos y equipos biomédicos.

---

# 9. Método de ventanas para FIR

Cuando se trunca una respuesta ideal aparecen oscilaciones alrededor de la frecuencia de corte. Este fenómeno se relaciona con el **fenómeno de Gibbs**.

Consecuencias:

- Ripple.
- Distorsión espectral.

Las ventanas permiten controlar estas oscilaciones.

### Ventanas importantes

| Ventana | Ripple | Atenuación |
|---|---|---|
| Rectangular | Alto | Baja |
| Hann | Medio | Media |
| Hamming | Bajo | Alta |
| Blackman | Muy bajo | Muy alta |

### Idea para recordar

**Rectangular → transición más estrecha pero peor rechazo lateral.**

**Hann → suavizado intermedio.**

**Hamming → buen compromiso y muy usada.**

**Blackman → mejor atenuación, pero transición más ancha.**

---

## Código básico FIR con ventanas

```python
from scipy import signal

b = signal.firwin(
    numtaps,
    cutoff,
    window='hamming',
    pass_zero='lowpass',
    fs=fs
)

y = signal.lfilter(b, [1], x)
```

`b` contiene los coeficientes del filtro FIR.

`signal.lfilter()` aplica el filtro a la señal `x`.

---

# 10. Filtros IIR

**IIR = Infinite Impulse Response**

Características:

- Recursivos.
- Utilizan feedback.
- Tienen polos y ceros.
- Normalmente requieren menor orden que un FIR equivalente.

### Ventajas

- Menor orden.
- Más eficientes.
- Menor memoria.
- Se pueden diseñar a partir de filtros analógicos.

### Desventajas

- Pueden presentar problemas de estabilidad.
- Fase no lineal.
- Mayor sensibilidad numérica.

---

# 11. Familias principales de filtros IIR

## Butterworth

Características:

- Respuesta máximamente plana.
- Sin ripple en la banda pasante.
- Respuesta suave.

```python
b, a = signal.butter(
    N,
    Wn,
    btype='low',
    output='ba',
    fs=fs
)

y = signal.lfilter(b, a, x)
```

---

## Chebyshev Tipo I

Características:

- Ripple en la banda pasante.
- Transición más abrupta.
- Puede conseguir menor orden.

```python
b, a = signal.cheby1(
    N,
    rp,
    Wn,
    btype='low',
    output='ba',
    fs=fs
)
```

---

## Chebyshev Tipo II

Características:

- Banda pasante plana.
- Ripple en la banda de rechazo.

```python
b, a = signal.cheby2(
    N,
    rs,
    Wn,
    btype='low',
    output='ba',
    fs=fs
)
```

---

## Elíptico

Características:

- Ripple en ambas bandas.
- Máxima selectividad.
- Puede utilizar el menor orden.

```python
b, a = signal.ellip(
    N,
    rp,
    rs,
    Wn,
    btype='low',
    output='ba',
    fs=fs
)
```

---

# 12. Comparación rápida de IIR

| Filtro | Ripple pasante | Ripple rechazo | Selectividad |
|---|---|---|---|
| Butterworth | No | No | Buena |
| Chebyshev I | Sí | No | Alta |
| Chebyshev II | No | Sí | Alta |
| Elíptico | Sí | Sí | Muy alta |

---

# 13. Diseño de filtros IIR — ejemplo de la clase

```python
# Butterworth High-pass @ 200 Hz
b_hp, a_hp = butter(
    order_iir,
    200/nyq,
    btype='highpass',
    output='ba'
)

# Chebyshev I Band-pass 100-300 Hz
b_bp, a_bp = cheby1(
    order_iir,
    1,
    [100/nyq, 300/nyq],
    btype='bandpass',
    output='ba'
)

# Elíptico Band-stop 100-300 Hz
b_bs, a_bs = ellip(
    order_iir,
    1,
    40,
    [100/nyq, 300/nyq],
    btype='bandstop',
    output='ba'
)
```

---

# 14. Respuesta en frecuencia de un filtro digital

Para observar la respuesta:

```python
w, h = signal.freqz(
    b,
    a,
    worN=2048,
    fs=fs
)

plt.plot(
    w,
    20*np.log10(np.abs(h))
)

plt.xlabel('Frecuencia [Hz]')
plt.ylabel('Magnitud [dB]')
plt.grid(True)
plt.show()
```

La gráfica permite identificar:

- Banda pasante.
- Banda de rechazo.
- Frecuencia de corte.
- Atenuación.
- Ripple.
- Banda de transición.

---

# 15. Estabilidad

Para un filtro IIR:

> **Todos los polos deben estar dentro del círculo unidad.**

En un diagrama de polos y ceros:

- Círculo unidad → límite de estabilidad.
- Polos dentro → sistema estable.
- Polos fuera → sistema inestable.

---

# 16. Biquads

Un **biquad** es una sección de segundo orden.

Dos estructuras importantes:

- Direct Form I → utiliza más memoria.
- Direct Form II → utiliza menos memoria.

### ¿Por qué usar biquads?

- Mejor estabilidad numérica.
- Implementación robusta.
- Uso industrial estándar.
- Muy adecuados para DSP embebido.

---

## Código con SOS / biquads

```python
sos = signal.butter(
    10,
    15,
    'hp',
    fs=1000,
    output='sos'
)

filtered = signal.sosfilt(sos, sig)
```

`output='sos'` genera **Second-Order Sections**, que permiten implementar el filtro mediante secciones de segundo orden y mejorar la estabilidad numérica.

---

# 17. Código que debes saber reconocer

## Crear una señal

```python
f = 120
fs = 1000

t = np.arange(1000) / fs

x = np.sin(2*np.pi*f*t)
```

## Sumar señales

```python
x3 = x1 + x2
```

## Graficar

```python
plt.plot(x)
plt.show()
```

## Limitar el eje x

```python
plt.xlim(0, 50)
```

## Espectro

```python
plt.magnitude_spectrum(
    x,
    scale="dB",
    Fs=fs
)

plt.show()
```

## Aplicar un FIR

```python
y = signal.lfilter(b, [1], x)
```

## Aplicar un IIR

```python
y = signal.lfilter(b, a, x)
```

## Aplicar un filtro SOS

```python
y = signal.sosfilt(sos, x)
```

---

# 18. Lo más importante para estudiar

### Conceptos

1. **Filtro** → modifica selectivamente componentes de frecuencia.
2. **Low-pass** → deja pasar bajas.
3. **High-pass** → deja pasar altas.
4. **Band-pass** → deja pasar una banda.
5. **Band-stop / Notch** → elimina una banda.
6. **FIR** → no recursivo, estable, puede tener fase lineal.
7. **IIR** → recursivo, usa feedback, menor orden.
8. **Convolución** → base del filtrado LTI.
9. **Respuesta al impulso** → caracteriza completamente un sistema LTI.
10. **Polos y ceros** → determinan gran parte del comportamiento del filtro.
11. **Estabilidad IIR** → polos dentro del círculo unidad.
12. **Biquads/SOS** → mejoran la estabilidad numérica.

### Comparaciones que debes memorizar

**FIR**
- Más estable.
- Fase lineal.
- Mayor orden.
- Más memoria.

**IIR**
- Menor orden.
- Más eficiente.
- Puede ser inestable.
- Fase generalmente no lineal.

**IIR clásicos**

```text
Butterworth → plano, sin ripple
Chebyshev I → ripple en pasante
Chebyshev II → ripple en rechazo
Elíptico → ripple en ambas, máxima selectividad
```

---

# 19. Flujo mental para resolver ejercicios

```text
1. ¿Qué frecuencias quiero conservar?
             ↓
2. ¿Qué frecuencias quiero eliminar?
             ↓
3. Elijo tipo de filtro
   LP / HP / BP / BS
             ↓
4. Elijo FIR o IIR
             ↓
5. Defino frecuencia de corte
             ↓
6. Diseño el filtro
             ↓
7. Obtengo coeficientes
             ↓
8. Aplico el filtro a la señal
             ↓
9. Grafico señal antes/después
             ↓
10. Analizo la respuesta en frecuencia
```

> **Regla clave:** antes de escribir código, identifica qué frecuencias tiene la señal y qué frecuencias debe dejar pasar el filtro.
