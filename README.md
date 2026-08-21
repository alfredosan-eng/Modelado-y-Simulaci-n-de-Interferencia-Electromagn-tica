# 🚀 Simulador EMI (UTP vs FTP)

[![Ver Aplicación en Vivo]([[https://alfredosan-eng.github.io/Modelado-y-Simulaci-n-de-Interferencia-Electromagn-tica/index.html]

> **Instrucciones:** Haz clic en el botón superior para ejecutar la simulación interactiva en tu navegador.

# Evaluación de Coexistencia Electromagnética (EMC) y Reglas de Separación para Cableado de Datos TPU (UTP vs. FTP)

Este repositorio alberga un entorno interactivo de simulación para el análisis cuantitativo de la **interferencia por diafonía (Crosstalk) e inducción electromagnética (EMI)** generada por líneas de potencia y señales de conmutación sobre cables industriales de **Poliuretano Termoplástico (TPU)** sin blindaje (**UTP**) frente a versiones apantalladas con lámina de Aluminio/Mylar (**FTP**).

La herramienta evalúa la eficacia de la **separación física ($d$)** y el **apantallamiento metálico** bajo normativas internacionales como la **EN 50174-2** y la **IEC 61000-4-4**.---

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

* ## 📐 Fundamentos Técnicos y Normativa de Instalación

### 1. La Regla de Separación Física (Norma EN 50174-2)
En canaletas compartidas y bandejas industriales, la intensidad del acoplamiento inductivo y capacitivo entre cables de energía y cables de datos es inversamente proporcional a la distancia que los separa ($d$) y directamente proporcional a la longitud de recorrido paralelo ($L$).

$$V_{\text{inducido}} \propto \frac{L \cdot \frac{di}{dt}}{d^\gamma}$$

* **Recomendación Estándar:** Para canaletas de aluminio/acero sin tabique separador, la norma establece una distancia mínima de **$20\text{ cm}$** entre cables de potencia unifilares ($>220\text{V}$) y arneses de datos de par trenzado.

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

## 🏭 Escenarios Industriales Evaluados en la Simulación

### Escenario A: Bandeja Industrial Compartida (380V/440V + Contactores)
* **Mecanismo de Interferencia:** Acoplamiento magnético a $50/60\text{ Hz}$ e **impulsos transitorios rápidos (EFT/Burst)** según la norma **IEC 61000-4-4**, generados en los momentos de apertura y cierre de contactores y arrancadores de motores.
* **Comportamiento UTP:** Produce picos agudos de voltaje fuera de la tolerancia diferencial, desencadenando pérdidas masivas de tramas Ethernet/Fieldbus.

### Escenario B: Canaletas de Oficina (Potencia + Iluminación LED y Fuentes SMPS)
* **Mecanismo de Interferencia:** Ruido conducido y radiado derivado de los armónicos de alta frecuencia producidos por los rectificadores de fuentes conmutadas (SMPS) y balastos electrónicos.
* **Comportamiento UTP:** Genera un piso de ruido continuo que degrada la relación señal-ruido (**SNR**), reduciendo la tasa efectiva de transferencia de datos (*throughput*).

### Escenario C: Cadena Portacables Robótica (Servodrives PWM + Encoders TPU)
* **Mecanismo de Interferencia:** Variaciones severas de voltaje ($dv/dt$) generadas por la modulación por ancho de pulsos (**PWM** a $8\text{--}16\text{ kHz}$) de los servodrives conduciendo junto a los cables de encoders dentro de la misma manguera flexible de TPU.
* **Comportamiento UTP vs FTP:** El cable **TPU UTP** experimenta falsos pulsos en la lectura de posición del encoder. El **TPU FTP** refleja y deriva a tierra los componentes de alta frecuencia mediante la lámina de aluminio y el hilo de drenaje (*drain wire*).
---
