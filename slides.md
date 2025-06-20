----
math: katex
header: Fahradar | Embedded-Systems | Busch, Kiriakou, Koscheck, Müssig, Zissis
paginate: false
style: |
  pre {
    font-size: 12px;
  }
  code {
    font-size: 14px;
  }
  h1 {
    font-size: 35px;
  }
  h2 {
    font-size: 25px;
  }
  li, p, td, th {
    font-size: 20px;
  }
  ul {
    padding-left: 1em;
  }
  .columns {
    display: flex;
    gap: 1rem;
  }
  .columns > div {
    flex: 1 1 0;
  }

----

# Fahradar
 
## *Sicher von A nach B*
 
Embedded Systems

Markus Busch, Chris Kiriakou, Michael Koscheck, Benedigt Müssig, Leonidas Zissis

<style scoped>
h1 {
    font-size: 80px;
    text-align: center;
    padding: 10px;
    margin: 10px;
}

h2 {
    font-size: 50px;
    text-align: center;
    padding: 10px;
    margin: 10px;
}

h3 {
    font-size: 30px;
    font-weight: normal;
    text-align: center;
    padding: 10px;
    margin: 10px;
}

section {
    text-align: center;
}

header {
    color: #FFFFFF00;
}
</style>

<!--
Noch offen
-->

----

# Gliederung

1. Einführung & Projektziel
2. Systemübersicht
3. Radar
4. Datenverarbeitung und Aufbereitung
5. Firmware
6. Visuelle Darstellung
7. Haptisches Feedback
8. Demo
9. Fazit & Ausblick

<!--
Noch offen
-->

----

# Einführung & Projektziel

<!--
Noch offen
-->

---- 
 
# Systemübersicht

<!--
Noch offen
-->

----

# Radar 

<!--
TI AWR6843ISK - Michael
-->

----

# Datenverarbeitung & Aufbereitung

<!--
Raspberry Pi Zero W - Benedigt
-->

----
 
# Firmware

<!--
Rasbperry Pi Pico 2 W - Leo
-->

----

# Visuelle Darstellung 

<!--
MemLCD - Markus
-->

----

# Haptisches Feedback

**DRV2605L Treiber**,
**Titan-Haptics Carlton**,
**Wellengenerierung**

![bg right:50% auto blur:5px](https://titanhaptics.com/wp-content/uploads/2024/01/LMR-1.gif)

----

# DRV2605L & Titan-Haptics Carlton

<div class="columns">
<div>

**DRV2605L**:
- Haptischer Feedback Treiber für LRA & ERM
- Implementierung einer Bibliothek für Abstraktion der Low-Level-Funktionen

```c
typedef struct {
    uint8_t addr;                    // I2C address
    i2c_inst_t *drv_i2c_instance;    // Pointer to the Pico SDK I2C instance
    enum DRV2605L_MODE mode;         // Current mode (rtp/wave sequence)
    enum CTRL_MODE ctrl;             // Control strategy (open/closed-loop)
} drv2605l_t;

bool drv2605l_init(drv2605l_t *drv);
void drv2605l_write(drv2605l_t *drv, uint8_t reg, uint8_t val);
void drv2605l_set_mode(drv2605l_t *drv, enum DRV2605L_MODE mode);
void drv2605l_stop(drv2605l_t *drv);
void drv2605l_rtp(drv2605l_t *drv, uint8_t rtp);
```

`bool drv2605l_init(drv2605l_t *drv)`:
- Kalibrierungsfunktion für Closed-Loop (Autocalibration-Routine schlägt fehl!)
- Frequenzbereiche **nicht** optimal! (150Hz - 300Hz)
- Setupfunktion für Open-Loop: $f_{min}$ = 80Hz

</div>
<div>

**Titan-Haptics Carlton**:
- LRA mit Beschleunigung von 5G bei 80Hz
- Betriebsspannung: 3.6 bis 10 Vp-p

| Modus       | Beschreibung                            |
|-------------|-----------------------------------------|
| Impact      | Mechanischer Schlag auf eine Oberfläche |
| Traditional | "Puls" haptisches Feedback, geräuschlos |

- **Haptisches Feedback**: (Rechteck $>$ Sinus)
- Mechanorezeptoren nehmen **starke** Veränderungen wahr

![height:200px](./assets/haptic/wave-effect-mechano.png)

</div>
</div>

----

# Generierung Wellenformen 

![bg vertical right:37% height:73%](./assets/haptic/wave-sim-slow-mid.png)
![bg right:37% height:73%](./assets/haptic/wave-sim-mid-mid.png)
![bg right:37% height:73%](./assets/haptic/wave-sim-high-mid.png)

```c
void generate_waveform(float x[2], float y, float speed, waveform_t *waveform_out);
```

**Abhängikeit der Eingabeparameter**:
  - Boundingbox $[m]$ `x` $\rightarrow$ Amplitudenfaktor jeweiliger Seite:
      - `x[0]`: Linke Grenze
      - `x[1]`: Rechte Grenze
  - Abstand $[m]$ `y` $\rightarrow$ Amplitude, Anzahl der Sample (20 - 40)
  - Geschwindigkeit $[km/h]$ `speed` $\rightarrow$ Steigung

**Stauchen und Strecken der Sigmoid Funktion**:
```c
float speed_norm = (speed_clamped - 1.0f) / (60.0f - 1.0f);
float k = 2.0f * powf(10.0f, speed_norm * 2.0f);            // steepness 2–200
```

```c
for (int i = 0; i < sample_count; ++i) {
    float time = (float)i / (sample_count - 1);             // normalize time [0, 1]
    float slope = 1.0f / (1.0f + expf(-k * (time - 0.5f))); // sigmoid function
    float left = left_factor * slope;
    float right = right_factor * slope;
    ...
}
```

<!--
DRV2605L & Titan-Haptics TacHommer-Carlton - Chris
-->

----

# Demo
 
<!--
Noch offen
-->

----

# Fazit & Ausblick

<!--
Noch offen
-->

----

# Quellen
- IEEE auswirkungen von Wellen 

<!--
Bitte fehlende Quellen hinzufügen
-->
 
<style scoped>
li {
    font-size: 14px;
}
</style>
