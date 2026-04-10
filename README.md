IoT Milk Preservation System Documentation
1.	Wiring Diagram (Textual Description)
ESP32 milk preservation — wiring diagram
DHT22 sensor — use a 10kΩ resistor between DATA and VCC (pull-up):
•	Pin 1 (VCC) → ESP32 3V3
•	Pin 2 (DATA) → GPIO4 (with 10kΩ pull-up to 3V3)
•	Pin 4 (GND) → GND
LCD 16×2 with I²C backpack — only 4 wires needed:
•	VCC → ESP32 VIN (5V)
•	GND → GND
•	SDA → GPIO21
•	SCL → GPIO22
Relay module:
•	VCC → VIN (5V)
•	GND → GND
•	IN → GPIO26
•	COM/NO → your cooling unit (fridge/Peltier)
Buzzer & LED — both share GPIO27:
•	Buzzer (+) → GPIO27, Buzzer (–) → GND
•	LED (+) → GPIO27 via a 220Ω resistor, LED (–) → GND
Check summary if correctly connected
•	ESP32 → DHT22 Sensor: Pin 4 → Data, 3.3V → VCC, GND → GND
•	ESP32 → LCD (I²C): SDA (GPIO 21) → SDA, SCL (GPIO 22) → SCL, 5V → VCC, GND → GND
•	ESP32 → RGB LED: GPIO 26 → Red, GPIO 27 → Green, GPIO 14 → Blue, common cathode → GND
•	ESP32 → Relay: GPIO 33 → IN, 5V → VCC, GND → GND
•	ESP32 → Buzzer: GPIO 25 → +, GND → -

 
2. Flow Chart (Logic Overview)
	  


                  │
          ┌───────▼────────┐
          │ Read Temp & Hum│
          └───────┬────────┘
                  │
        ┌─────────▼─────────┐
        │ Sensor Error?     │
        └───────┬───────────┘
                │Yes
                ▼
        Display "Sensor Error"
        Turn OFF all outputs
                │
                └───────────────► Loop
                │No
                ▼
   ┌───────────────────────────────┐
   │ Check Temperature Thresholds  │
   └───────────────────────────────┘
   
	Blue ≤2°C → Too Cold
   	Red 5–8°C → Too Hot
   Flash Red >8°C → Critical Temp

                │
                ▼
   ┌───────────────────────────────┐
   │ Check Humidity Thresholds     │
   └───────────────────────────────┘
   	Yellow <70% → Low Humidity
   	Green 70–79% → Moderate
   	Cyan 80–90% → Optimal
   	Purple 91–95% → High Humidity
   Flash Purple/Red >95% → Critical Humidity
                │
                ▼
          ┌─────────────-──┐
          │ Update LCD     │
          │ (Alternate T/H)│
          └───────┬────-───┘
                  │
                  ▼
                Loop
3. Milk Test Criteria Documentation
Test ID	Temp Range (°C)	Humidity Range (%)	System Status	LED Color	Relay	Buzzer	Meaning for Milk Preservation / Risks
TEST 1	≤ 2	80–90	Too Cold	Blue	OFF	ON	Milk may freeze; ice crystals disrupt proteins. Overcooling risks poor texture. In cold‑room collection centers, improper chilling can mask adulteration or contamination.
TEST 2	3–5	80–90	Safe Range	Cyan	OFF	OFF	Ideal preservation. Milk remains stable for pooling/processing. Automated monitoring ensures adulterated or contaminated milk is detected before mixing.
TEST 3	> 5	80–90	Too Hot	Red	ON	ON	Spoilage risk begins. Warm conditions accelerate bacterial growth. Unscrupulous farmers may exploit warm storage to dilute milk with water, compromising quality.
TEST 4	> 8	80–90	Critical Heat	Red Flash	ON	ON	High contamination danger. Milk should be isolated before mixing. At this stage, bacterial load is unsafe for infants and poses serious health risks.
TEST 5	Any	< 70	Low Humidity Warning	Yellow	OFF	OFF	Environment too dry; excessive ventilation reduces cooling efficiency. Poor storage conditions increase risk of adulteration detection failures.
TEST 6	Any	70–79	Moderate Humidity	Green	OFF	OFF	Acceptable but below ideal. Continuous monitoring needed to prevent unnoticed contamination from sick cows or poor hygiene.
TEST 7	Any	80–90	Optimal Humidity	Cyan	OFF	OFF	Best humidity for dairy preservation. Stable conditions support rapid, automated quality testing and accountability in supply chains.
TEST 8	Any	> 90	High Humidity Warning	Purple	OFF	ON	Excess moisture causes condensation, mold, and bacterial contamination. Mixing contaminated milk with good milk compromises entire batches, highlighting the need for automated rejection systems.

