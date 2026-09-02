# Laboratorio de filtros digitales


En el laboratorio se abordaron los temas vistos en clase referentes a muestreo, señales sinusoidales, espectro, filtros, respuesta en frecuencia, FIR, IIR y SOS.

A continuación, se analizará el código propuesto en clase por el profesor para el entendimiento del tema tratado.

---

# 1. Código base

```python
import numpy as np
import matplotlib.pyplot as plt

f1 = 120
f2 = 20
fs = 1000

t = np.arange(0, 2, 1/fs)

x1 = np.sin(2*np.pi*f1*t)
x2 = np.sin(2*np.pi*f2*t)

x3 = x1 + x2

plt.figure()
plt.plot(t, x3)
plt.xlabel("Tiempo (s)")
plt.ylabel("Amplitud")
plt.title("Señal x3(t)")
plt.grid()
plt.show()

plt.figure()
plt.magnitude_spectrum(x3, scale="dB", Fs=fs)
plt.title("Espectro de magnitud de x3")
plt.show()
```

La idea es: crear una señal con componentes frecuenciales, observarla en el tiempo, observar su contenido en frecuencia y luego usar un filtro para seleccionar qué frecuencias pasan o se atenúan.

---

# 2. Librerías

```python
import numpy as np
import matplotlib.pyplot as plt
```

`numpy` permite trabajar con arreglos y operaciones matemáticas:

```python
np.sin()
np.pi
np.arange()
```

`matplotlib.pyplot` permite crear las gráficas:

```python
plt.plot()
plt.xlabel()
plt.ylabel()
plt.title()
plt.grid()
plt.show()
```

---

# 3. Frecuencias

```python
f1 = 120
f2 = 20
fs = 1000
```

Tenemos:

- `f1 = 120 Hz`: frecuencia de la primera señal.
- `f2 = 20 Hz`: frecuencia de la segunda señal.
- `fs = 1000 Hz`: frecuencia de muestreo.

La frecuencia de muestreo indica cuántas muestras tomamos por segundo.

En el ejemplo:

f_s=1000 Hz

La frecuencia de Nyquist es:

f_N= 500 Hz

Como 20 Hz y 120 Hz están por debajo de 500 Hz, ambas componentes pueden representarse con este muestreo.

El PDF utiliza igualmente `fs = 1e3` en el ejemplo de conversión de un filtro analógico a uno discreto. 

---

# 4. Crear el eje de tiempo

```python
t = np.arange(0, 2, 1/fs)
```

`np.arange(inicio, final, paso)` crea valores separados por el paso indicado.

Aquí:

```text
inicio = 0 s
final  = 2 s
paso   = 1/fs
```

Como:

\[
f_s=1000
\]

entonces:

\[
\Delta t=\frac{1}{f_s}=0.001\ s
\]

Por tanto, las muestras están separadas por 1 ms.

### Parte importante para recordar:

```python
t = np.arange(0, duración, 1/fs)
```

---

# 5. Crear una sinusoidal

La ecuación es:

\[
x(t)=\sin(2\pi ft)
\]

En Python:

```python
x = np.sin(2*np.pi*f*t)
```

## Primera señal

```python
x1 = np.sin(2*np.pi*f1*t)
```

Como `f1 = 120`:

\[
x_1(t)=\sin(2\pi120t)
\]

## Segunda señal

```python
x2 = np.sin(2*np.pi*f2*t)
```

Como `f2 = 20`:

\[
x_2(t)=\sin(2\pi20t)
\]

---

# 6. Sumar las señales

```python
x3 = x1 + x2
```

Matemáticamente:

\[
x_3(t)=x_1(t)+x_2(t)
\]

Por tanto:

\[
x_3(t)=
\sin(2\pi120t)+
\sin(2\pi20t)
\]

La señal `x3` contiene dos componentes:

```text
20 Hz
120 Hz
```

Importante: una señal puede contener varias frecuencias y el filtro puede modificar selectivamente esas componentes.

---

# 7. Graficar en el dominio del tiempo

```python
plt.figure()
plt.plot(t, x3)
plt.xlabel("Tiempo (s)")
plt.ylabel("Amplitud")
plt.title("Señal x3(t)")
plt.grid()
plt.show()
```

### ¿Qué hace cada línea?

```python
plt.figure()
```

Crea una figura nueva.

```python
plt.plot(t, x3)
```

Grafica:

- eje x → tiempo
- eje y → amplitud

```python
plt.xlabel("Tiempo (s)")
```

Etiqueta del eje x.

```python
plt.ylabel("Amplitud")
```

Etiqueta del eje y.

```python
plt.title("Señal x3(t)")
```

Título.

```python
plt.grid()
```

Agrega cuadrícula.

```python
plt.show()
```

Muestra la gráfica.

### Idea de la clase

Esta gráfica responde:

> ¿Cómo cambia la señal con el tiempo?

Pero no muestra directamente qué frecuencias contiene. Para eso usamos el espectro.

---

# 8. Tiempo vs frecuencia

## Dominio del tiempo

```python
plt.plot(t, x3)
```

Representa:

\[
x(t)
\]

Muestra cómo cambia la amplitud con el tiempo.

## Dominio de la frecuencia

```python
plt.magnitude_spectrum(x3, scale="dB", Fs=fs)
```


---

# 9. `magnitude_spectrum()`

La línea:

```python
plt.magnitude_spectrum(x3, scale="dB", Fs=fs)
```

muestra el espectro de magnitud de `x3`.

## `Fs=fs`

```python
Fs=fs
```

Le indica a la función la frecuencia de muestreo:

```python
fs = 1000
```

Así el eje de frecuencia se interpreta en Hz.

## `scale="dB"`

```python
scale="dB"
```

Muestra la magnitud en decibeles.

El PDF también convierte magnitudes a dB mediante:

```python
20*np.log10(mag)
```

en el análisis de respuesta en frecuencia. fileciteturn1file1L86-L100

---

# 10. ¿Qué debe aparecer en el espectro?

Tenemos:

```python
x1 = sin(2*pi*120*t)
x2 = sin(2*pi*20*t)
x3 = x1 + x2
```

Por eso:

```text
x3
│
├── 20 Hz
└── 120 Hz
```

En el espectro aparecen picos asociados con esas componentes.

La conexión con filtros es:

```text
Señal
20 Hz + 120 Hz
       ↓
     FILTRO
       ↓
frecuencias seleccionadas
```

Por ejemplo, un low-pass puede conservar la componente de baja frecuencia y atenuar la alta.


---

# 11. Tipos de filtros

El PDF clasifica los filtros según su respuesta frecuencial. 

### Low-pass / pasa bajas

Deja pasar bajas frecuencias y atenúa altas.

```text
20 Hz   → pasa
120 Hz  → atenúa
```

### High-pass / pasa altas

Deja pasar altas y atenúa bajas.

```text
20 Hz   → atenúa
120 Hz  → pasa
```

El PDF describe el high-pass como un filtro que elimina bajas frecuencias y deja pasar altas. 

### Band-pass / pasa banda

Deja pasar una banda determinada.

### Notch / rechaza banda

Rechaza una banda específica.

---

# 12. Conexión con respuesta en frecuencia

El PDF introduce la respuesta en frecuencia mediante:

- magnitud
- fase 

Para un filtro analógico del PDF:

```python
from scipy import signal
import numpy as np
import matplotlib.pyplot as plt

fc = 50
wc = 2*np.pi*fc

num = wc
den = [1, wc]

h_s = signal.TransferFunction(num, den)
```

Aquí:

```python
fc = 50
```

es la frecuencia de corte.

```python
wc = 2*np.pi*fc
```

convierte Hz a frecuencia angular:

\[
\omega_c=2\pi f_c
\]

```python
num = wc
den = [1, wc]
```

representan los coeficientes del numerador y denominador de la función de transferencia.

```python
h_s = signal.TransferFunction(num, den)
```

crea el sistema.

---

# 13. Calcular la respuesta en frecuencia

El PDF usa:

```python
w, H = signal.freqresp(h_s)

mag = np.abs(H)
phase = np.angle(H)
```

`signal.freqresp()` obtiene la respuesta del sistema.

```text
w   → frecuencias
H   → respuesta compleja
mag → magnitud
phase → fase
```

Después se grafica:

```python
plt.semilogx(
    w/(2*np.pi),
    20*np.log10(mag)
)
```

### ¿Por qué?

```python
w/(2*np.pi)
```

convierte frecuencia angular a Hz.

```python
20*np.log10(mag)
```

convierte magnitud a dB.

```python
plt.semilogx(...)
```

usa una escala logarítmica para la frecuencia.



# 14. Transformación bilineal

En la clase se mostró cómo convertir un filtro analógico en uno digital.

```python
fs = 1e3
ts = 1/fs

h_d = h_s.to_discrete(ts, method="bilinear")

a = -h_d.den[1:]
b = h_d.num
```

Primero:

\[
T_s=\frac{1}{f_s}
\]

Luego:

```python
h_s.to_discrete(ts, method="bilinear")
```

obtiene una representación discreta mediante la transformación bilineal. 

Los coeficientes `a` y `b` permiten implementar la ecuación en diferencias del filtro.

---

# 15. Convolución

El PDF presenta la convolución como base del filtrado.

\[
y[n]=x[n]*h[n]
\]

donde:

```text
x[n] → entrada
h[n] → respuesta al impulso
y[n] → salida
```

La respuesta al impulso caracteriza completamente al sistema LTI.

Conceptualmente:

```text
x[n]
 ↓
convolución con h[n]
 ↓
y[n]
```

---

# 16. FIR

FIR = Finite Impulse Response.

Según la clase:

- no recursivo
- solo ceros
- fase lineal
- siempre estable
- puede interpretarse como un promedio ponderado del pasado fileciteturn1file3L601-L610

---

# 17. FIR con `firwin()`

Código del PDF:

```python
b = signal.firwin(
    numtaps,
    cutoff,
    window='rect',
    pass_zero="lowpass",
    fs=fs
)

y = signal.lfilter(b, [1], x)
```

La lógica es:

```text
firwin()
   ↓
calcula coeficientes b
   ↓
lfilter()
   ↓
aplica el filtro
   ↓
y = señal filtrada
```

---

# 18. IIR

IIR = Infinite Impulse Response.

Características:

- recursivo
- feedback
- polos y ceros
- menor orden

Ventajas:

- menor orden
- menos memoria
- menor costo computacional
- diseño a partir de filtros analógicos

Desventajas:

- posible inestabilidad
- fase no lineal
- mayor sensibilidad numérica


---

# 19. Familias IIR

Debes reconocer:

```text
Butterworth
Chebyshev I
Chebyshev II
Elliptic
Bessel
```


### Butterworth

```text
Sin ripple
Respuesta suave
Máximamente plano
```

### Chebyshev I

```text
Ripple en banda pasante
Transición abrupta
Menor orden
```

Se requiere el uso de `butter()` y `cheby1()` para diseñar estos filtros. 

---

# 20. Código IIR básico

## Butterworth

```python
b, a = signal.butter(
    4,
    50,
    btype="low",
    fs=fs
)

y = signal.lfilter(b, a, x)
```

Interpretación:

```text
4        → orden
50       → frecuencia de corte
"low"    → pasa bajas
fs       → frecuencia de muestreo
b, a     → coeficientes
y        → señal filtrada
```

---

# 21. Polos y ceros

El PDF usa:

```python
z, p, k = signal.tf2zpk(b, a)
```

donde:

```text
z → ceros
p → polos
k → ganancia
```


Condición:

\[
|p_i|<1
\]

---

# 22. SOS / Biquads

Para filtros IIR de orden alto se pueden usar secciones de segundo orden.

Ejemplo:

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

La idea:

```text
IIR de orden alto
       ↓
secciones de segundo orden
       ↓
SOS
       ↓
señal filtrada
```

Esta estructura se presenta como una forma robusta para implementar filtros IIR, especialmente útil en DSP y sistemas embebidos.

---

# 23. Plantilla mental para resolver ejercicios

Cuando se tiene una señal:

### 1. Definir las frecuencias

```python
f1 = ...
f2 = ...
fs = ...
```

### 2. Crear el tiempo

```python
t = np.arange(0, 2, 1/fs)
```

### 3. Crear las señales

```python
x1 = np.sin(2*np.pi*f1*t)
x2 = np.sin(2*np.pi*f2*t)
```

### 4. Sumar

```python
x = x1 + x2
```

### 5. Graficar en tiempo

```python
plt.plot(t, x)
plt.show()
```

### 6. Obtener el espectro

```python
plt.magnitude_spectrum(
    x,
    scale="dB",
    Fs=fs
)
plt.show()
```

### 7. Decidir el filtro

```text
Eliminar altas       → low-pass
Eliminar bajas       → high-pass
Conservar una banda  → band-pass
Eliminar una banda   → notch
```

### 8. Diseñar/aplicar

FIR:

```python
b = signal.firwin(...)
y = signal.lfilter(b, [1], x)
```

IIR:

```python
b, a = signal.butter(...)
y = signal.lfilter(b, a, x)
```

SOS:

```python
sos = signal.butter(..., output="sos")
y = signal.sosfilt(sos, x)
```

### 9. Comparar original y filtrada

```python
plt.plot(t, x, label="Original")
plt.plot(t, y, label="Filtrada")
plt.legend()
plt.grid()
plt.show()
```

---

# 24. Resumen

```text
import numpy as np
→ matemáticas y arreglos

import matplotlib.pyplot as plt
→ gráficas

fs
→ frecuencia de muestreo

1/fs
→ período de muestreo

np.arange()
→ crea muestras de tiempo

np.sin(2*pi*f*t)
→ crea una sinusoidal

x1 + x2
→ señal compuesta

plt.plot(t,x)
→ señal en el tiempo

magnitude_spectrum(x, scale="dB", Fs=fs)
→ espectro de magnitud

LOW-PASS
→ deja bajas

HIGH-PASS
→ deja altas

BAND-PASS
→ deja una banda

NOTCH
→ rechaza una banda

FIR
→ no recursivo, estable, fase lineal

IIR
→ recursivo, polos/ceros

butter()
→ Butterworth

cheby1()
→ Chebyshev I

firwin()
→ coeficientes FIR

lfilter()
→ aplica filtro

sosfilt()
→ aplica filtro SOS

tf2zpk()
→ polos, ceros y ganancia.
```

---
