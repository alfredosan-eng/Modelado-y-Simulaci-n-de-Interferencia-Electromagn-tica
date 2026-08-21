# 🚀 Simulador EMI (UTP vs FTP)

[![Ver Aplicación en Vivo]([[https://alfredosan-eng.github.io/Modelado-y-Simulaci-n-de-Interferencia-Electromagn-tica/index.html]

> **Instrucciones:** Haz clic en el botón superior para ejecutar la simulación interactiva en tu navegador.

# Modelado y Simulación de Interferencia Electromagnética (EMI) en Cables Industriales TPU (UTP vs. FTP)

Este repositorio contiene un entorno de simulación computacional interactivo desarrollado en **HTML5 Canvas, CSS3 y JavaScript** enfocado en el análisis de Compatibilidad Electromagnética (EMC). Muestra de forma cuantitativa y cualitativa cómo el campo electromagnético radiado por un motor industrial gobernado por un Variador de Frecuencia (VFD) se acopla a un cable de **Poliuretano Termoplástico (TPU)** sin blindaje (**UTP**) frente a uno con pantalla de aluminio/mylar (**FTP**).

---

## 📐 Fundamentos Teóricos e Ingeniería de EMC

### 1. Permeabilidad Magnética del TPU vs. Apantallamiento Metálico
El TPU (*Thermoplastic Polyurethane*) es ampliamente seleccionado en arneses robóticos debido a su alta resistencia mecánica a la flexión dinámica, abrasión e hidrocarburos. Sin embargo, su **permeabilidad magnética relativa es cercana a la del vacío** ($\mu_r \approx 1$). 

* **Consecuencia:** El TPU exterior es transparente al flujo magnético variable ($\Phi_B$). Las líneas de campo penetran hacia los conductores internos sin sufrir atenuación electromagnética.

### 2. Mecanismos de Acoplamiento e Interferencia
La distorsión inducida por el motor sobre los conductores internos del cable responde a dos fenómenos principales:

1. **Acoplamiento Inductivo (Campo Magnético Cercano):**
   Generado por las variaciones de corriente $di/dt$ en los devanados del motor. Por la **Ley de Faraday-Lenz**, el voltaje de ruido inducido ($V_{ruido}$) en la espira formada por los conductores del cable es:
   $$V_{ruido} = -M \frac{di}{dt}$$
   *Donde $M$ es la inductancia mutua entre el motor y el arnés de cables.*

2. **Acoplamiento Capacitivo (Campo Eléctrico):**
   Producido por los altos picos de voltaje $dv/dt$ generados por la modulación por ancho de pulsos (PWM) del VFD.

### 3. Ley del Cuadrado Inverso para la Intensidad de Radiación
En la zona de campo cercano/transición, la densidad de potencia del campo electromagnético radiado disminuye de manera no lineal respecto a la distancia ($d$) entre la fuente de ruido y el cable sensible:

$$S_{EMI} \propto \frac{P_{motor}}{d^2}$$

* **Comportamiento en Simulación:** Al reducir la distancia de $4.0\text{ m}$ a $0.5\text{ m}$, el ruido $V_{ruido}$ experimentado por el cable UTP no aumenta proporcionalmente, sino que se incrementa en un **factor de 64x**, deformando totalmente la onda sinusoidal.

---

## 🛡️ Análisis del Blindaje FTP (*Foil Shielded Twisted Pair*)

El cable **TPU FTP** implementa una lámina de Aluminio/Mylar envuelta en $360^\circ$ sobre los pares trenzados internos, acompañada de un hilo de drenaje (*drain wire*) de cobre estañado.

### Efectividad del Blindaje (Shielding Effectiveness - SE)
La efectividad del apantallamiento se mide en decibelios (dB) y se define como la suma de pérdidas por reflexión ($R$), absorción ($A$) y reflexiones múltiples ($M$):

$$SE_{\text{dB}} = R_{\text{dB}} + A_{\text{dB}} + M_{\text{dB}} = 20 \log_{10} \left( \frac{E_{\text{incidente}}}{E_{\text{transmitido}}} \right)$$

* **Respuesta del Cable FTP:** La lámina de aluminio proporciona alta reflexión ($R$) para ruidos de alta frecuencia radiados por la conmutación PWM.
* **Atenuación Demostrada:** En el canal 3 del osciloscopio se observa una atenuación nominal superior a **$-24\text{ dB}$ a $-32\text{ dB}$**, reduciendo el ruido residual en más de un $94\%$ con respecto al cable UTP.

---

## 🖥️ Arquitectura del Simulador

El software está compuesto por dos módulos de renderizado continuo a $60\text{ FPS}$:

1. **Osciloscopio Digital Tri-Canal:**
   * **CH1 (Verde):** Señal nominal limpia $V(t) = A \sin(\omega t)$.
   * **CH2 (Rojo):** Señal TPU UTP distorsionada $V_{UTP}(t) = A \sin(\omega t) + e_{EMI}(d, \text{RPM})$.
   * **CH3 (Azul):** Señal TPU FTP atenuada $V_{FTP}(t) = A \sin(\omega t) + (e_{EMI} \cdot 10^{-SE/20})$.
2. **Representación de Escena y Campo Vectorial:**
   * Motor cinemático interactivo con detección de eventos mouse (`mousedown`, `mousemove`).
   * Visualización de frentes de onda circulares concéntricos representativos de la densidad de flujo $B(t)$.
   * Renderizado estructural interno de la cubierta TPU, lámina de foil de aluminio, hilo de drenaje y conductores de cobre.

---
