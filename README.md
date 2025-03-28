# Practica6: BUSES DE COMUNICACIÓN

## Introducció:
En la práctica anterior, exploramos los buses de comunicación. Ahora, en esta práctica, continuaremos con ese estudio, concentrándonos especialmente en el bus SPI.

La práctica constará de: 
 - Lectura i escritura de la memoria SD
 - Lectura de la etiqueta RFID


## EJERCICIO 1 
```c++
#include <SPI.h>
#include <SD.h>
#include <Arduino.h>
File myFile;
void setup()
{
 Serial.begin(9600);
 Serial.print("Iniciando SD ...");
 if (!SD.begin(10)) {
 Serial.println("No se pudo inicializar");
 return;
 }
 Serial.println("inicializacion exitosa");
 myFile = SD.open("/archivo.txt");//abrimos el archivo
 if (myFile) {
 Serial.println("archivo.txt:");
 while (myFile.available()) {
 Serial.write(myFile.read());
 }
 myFile.close(); //cerramos el archivo
 } else {
 Serial.println("Error al abrir el archivo");
 }
}
void loop()
{

}
```
### Funcionamiento del código:

Este código inicializa una tarjeta SD, luego abre un archivo llamado "archivo.txt". Una vez abierto, lee el contenido del archivo y lo envía a través del puerto serie, y finalmente cierra el archivo.

### **El "void setup"**: 

Este subprograma configura la comunicación serial, intenta inicializar la tarjeta SD en el pin 4, abre el archivo "archivo.txt" si la inicialización tiene éxito, y lee su contenido para enviarlo a través de la comunicación serial.

**En este caso el *"void loop()"* no contiene instrucciones**

### Salida por el puerto serie:
La salida por el puerto serie será:

```
Iniciando SD ... inicializacion exitosa
archivo.txt:
Soy el mejor del mundo
```

## EJERCICIO 2

```c++
#include <SPI.h>
#include <MFRC522.h>
#define RST_PIN 0 //Pin 9 para el reset del RC522
#define SS_PIN 10 //Pin 10 para el SS (SDA) del RC522
MFRC522 mfrc522(SS_PIN, RST_PIN); //Creamos el objeto para el RC522
void setup() {
Serial.begin(9600); //Iniciamos la comunicación serial
SPI.begin(); //Iniciamos el Bus SPI
mfrc522.PCD_Init(); // Iniciamos el MFRC522
Serial.println("Lectura del UID");
}
void loop() {
// Revisamos si hay nuevas tarjetas presentes
if ( mfrc522.PICC_IsNewCardPresent())
 {
 //Seleccionamos una tarjeta
 if ( mfrc522.PICC_ReadCardSerial())
 {
 // Enviamos serialemente su UID
 Serial.print("Card UID:");
 for (byte i = 0; i < mfrc522.uid.size; i++) {
  Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0"
    : " ");
     Serial.print(mfrc522.uid.uidByte[i], HEX);
     }
     Serial.println();
     // Terminamos la lectura de la tarjeta actual
     mfrc522.PICC_HaltA();
     }
    }
    }
```
### Funcionamiento del código:

Este código establece la comunicación con el módulo lector de tarjetas RFID MFRC522. Su función es escanear en busca de tarjetas RFID y, al detectar una, leer su UID y mostrarlo en el monitor serial. Esto permite identificar de manera única cada tarjeta dentro del área de alcance del lector RFID.


### Ejemplo de salida por el puerto serie:
Suponiendo que tengamos una tarjeta con el siguiente UID: 27 12 65 25

La salida por el puerto serie será:

```
Lectura del UID
Card UID: 27 12 65 25
```



