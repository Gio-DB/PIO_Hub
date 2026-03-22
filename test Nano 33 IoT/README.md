# Original source:

https://dronebotworkshop.com/arduino-nano-33-iot/

https://www.youtube.com/watch?v=JmvMvIphMnY&t=940s

## 2D-Plot (from AI)
Ja, du kannst die Werte `x`, `y` und `z` in einer Echtzeit-Grafik anzeigen lassen. PlatformIO bietet dafür den **Serial Plotter**, der Daten in Echtzeit als Zeitserien-Diagramme darstellt (mit der Zeit auf der X-Achse und den Werten auf der Y-Achse). Das ist ideal für kontinuierliche Sensordaten wie deine Beschleunigungswerte.

Allerdings ist der Serial Plotter standardmäßig für **Zeitserien** gedacht (z. B. `x`, `y` und `z` über Zeit), nicht für einen echten **XY-Plot** (wo `x` auf der X-Achse und `y` auf der Y-Achse dargestellt wird, wie in einem 2D-Koordinatensystem). Wenn du speziell einen XY-Plot meinst (z. B. um die Bewegung als Punktwolke zu visualisieren), ist das mit dem eingebauten Serial Plotter nicht direkt möglich – dafür brauchst du externe Tools wie Processing, Python (mit matplotlib) oder ein anderes Visualisierungstool.

Hier erkläre ich beide Optionen Schritt für Schritt. Dein aktueller Code gibt die Daten bereits in einem Format aus, das der Serial Plotter versteht (tab-getrennt).

### 1. **Echtzeit-Grafik mit dem Serial Plotter (Zeitserien-Darstellung)**
   Dies zeigt `x`, `y` und `z` als separate Linien über Zeit an. Die X-Achse ist die Zeit, die Y-Achse die Werte.

   - **Serial Plotter öffnen:**
     - In VS Code: Klicke auf das PlatformIO-Icon in der Statusleiste (unten) und wähle **Serial Plotter**. Alternativ: Befehlspalette (Strg+Shift+P) > "PlatformIO: Serial Plotter".
     - Über Terminal: `pio device monitor --plot` (im Projektordner).

   - **Baudrate einstellen:** Stelle sicher, dass die Baudrate auf 9600 gesetzt ist (wie in deinem Code).

   - **Ergebnis:** Du siehst drei Linien für `x`, `y` und `z`, die sich in Echtzeit aktualisieren. Bewege das Board, um Änderungen zu sehen (z. B. Neigung führt zu Schwankungen in den Werten).

   - **Anpassungen im Code (falls nötig):**
     Dein Code ist bereits kompatibel, da er tab-getrennte Werte ausgibt (`Serial.print(x); Serial.print('\t'); ...`). Der Plotter erkennt das automatisch.
     - Wenn du die Skalierung anpassen möchtest (z. B. mehr Dezimalstellen), ändere `Serial.print(x)` zu `Serial.print(x, 4)` für 4 Dezimalstellen.

### 2. **Echtzeit-Grafik als 2D-XY-Darstellung (XY-Plot)**
   Wenn du `x` auf der X-Achse und `y` auf der Y-Achse darstellen möchtest (z. B. als Punktwolke oder Linie, die die Bewegung zeigt), musst du externe Software verwenden. Der Serial Plotter kann das nicht nativ.

   - **Einfache Option: Processing (kostenlos, Arduino-freundlich)**
     - **Installation:** Lade Processing von [processing.org](https://processing.org/download/) herunter und installiere es.
     - **Code-Beispiel für Processing:** Erstelle eine neue Skizze in Processing und füge diesen Code ein. Er liest die seriellen Daten und plottet `x` vs. `y` als Punkte in Echtzeit.
       ```
       import processing.serial.*;

       Serial myPort;
       float x, y, z;

       void setup() {
         size(800, 600);  // Fenstergröße
         background(255);
         myPort = new Serial(this, Serial.list()[0], 9600);  // Passe den Port an (z. B. "/dev/ttyACM0" auf macOS)
         myPort.bufferUntil('\n');
       }

       void draw() {
         // Optional: Hintergrund leeren für Animation
         // background(255);
       }

       void serialEvent(Serial myPort) {
         String inString = myPort.readStringUntil('\n');
         if (inString != null) {
           inString = trim(inString);
           String[] values = split(inString, '\t');  // Tab-getrennt
           if (values.length >= 2) {
             x = float(values[0]);
             y = float(values[1]);
             // Plotte x vs y als Punkt
             stroke(0);
             point(map(x, -2, 2, 0, width), map(y, -2, 2, height, 0));  // Skaliere auf Fenster (anpassen an deine Wertebereiche)
           }
         }
       }
       ```
       - **Anpassungen:** 
         - Passe den Serial-Port in `Serial.list()[0]` an (überprüfe in Processing > Tools > Serial Monitor, welcher Port dein Board ist).
         - Die `map()`-Funktion skaliert die Werte auf das Fenster (z. B. von -2 bis 2 G auf 0 bis 800 Pixel). Passe die Bereiche an deine Daten an.
         - Starte die Skizze in Processing, während dein Board läuft.
       - **Ergebnis:** Eine 2D-Punktwolke, die sich in Echtzeit aktualisiert.

   - **Alternative: Python mit matplotlib (für fortgeschrittenere Visualisierungen)**
     - Installiere Python und matplotlib (`pip install matplotlib pyserial`).
     - Verwende ein Skript wie dieses, um Daten zu lesen und zu plotten:
       ```python
       import serial
       import matplotlib.pyplot as plt
       import matplotlib.animation as animation

       ser = serial.Serial('/dev/ttyACM0', 9600)  # Passe den Port an
       fig, ax = plt.subplots()
       x_data, y_data = [], []

       def update(frame):
           line = ser.readline().decode('utf-8').strip()
           values = line.split('\t')
           if len(values) >= 2:
               x, y = float(values[0]), float(values[1])
               x_data.append(x)
               y_data.append(y)
               ax.clear()
               ax.scatter(x_data, y_data)  # Oder ax.plot für Linie
               ax.set_xlim(-2, 2)  # Anpassen
               ax.set_ylim(-2, 2)

       ani = animation.FuncAnimation(fig, update, interval=100)
       plt.show()
       ```
     - Dies erstellt einen animierten Scatter-Plot.

### Hinweise
- **Leistung:** Echtzeit-Grafiken können das Board belasten, wenn die Loop zu schnell läuft. Füge `delay(100)` in `loop()` hinzu, um die Rate zu drosseln.
- **Fehlerbehebung:** Wenn der Plotter nicht funktioniert, überprüfe den Serial-Port und die Baudrate. Stelle sicher, dass kein anderer Monitor offen ist.
- **Z als dritte Dimension:** Für 3D-Visualisierungen (x, y, z) brauchst du spezialisierte Tools wie Unity oder Three.js.

Falls du mehr Details zu einer Option brauchst oder Hilfe bei der Implementierung, lass es mich wissen!
