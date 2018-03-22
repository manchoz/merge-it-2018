# Kiotlog

### Internet delle Cose Open Source

#### Una piattaforma per IoT LPWAN

---

## Why Kiotlog?

### Agricoltura di Precisione

- Cambiamenti climatici
- Necessari rilevamenti ubiquiti
- Stato dell'arte non adatto

---

### Opportunità

- Tecnologie di MCU a basso costo
- Nuove tecnologie di trasmissione

***

### Reti LPWAN

- **L**ow **P**ower
  - 1 Anno con due batterie AA
- **W**ide **A**rea **N**etwork
  - 2Km+

***

- LoRaWAN
  - Modulazione LoRa (proprietaria)
  - Architettura Aperta
- SigFox
  - Servizio commerciale Global
  - Operatori Nazionali

---

## Problemi - 1

### Low Bandwidth

- 10 byte/10 minuti
- No [JSON](https://json.org/), no Binary JSON, no [MsgPack](https://msgpack.org/), no [CBOR](http://cbor.io/)

***

Packed Structs

```
typedef struct __attribute__ ((packed)) payload {
    uint8_t status;
    int16_t dhtTemperature;
    uint16_t dhtHumidity;
    int16_t moduleTemperature;
    uint8_t lastMessageStatus;
} Payload;
````

***

Arduino

```
Payload payload;

void loop() {

  payload.status = client.status;
  payload.dhtTemperature = DHT.temperature * 100;
  payload.dhtHumidity = DHT.temperature * 10;
  payload.moduleTemperature = module.temperature * 100;
  payload.lastMessageStatus = client.response;

  client.write((byte *)&payload, sizeof(Payload));
}
```

***

Array di byte

```
  { 0x01, 0x0f, 0x8c, 0x01, 0xf1,
    0x09, 0x5e, 0x0c, 0x33, 0x00 }
```

***

Ma io volevo

```
{
  "Stato": true,
  "Temperatura DHT": 23.47,
  "Umidità DHT": 49.1,
  "Temperature M0": 26.23,
  "Risposta": false
}
```


***

Bitfields Structs

```
typedef struct __attribute__ ((packed)) payload_bf {
    uint32_t temperature_internal: 10;
    uint32_t temperature: 10;
    uint32_t pressure: 12;

    uint32_t humidity: 10;
    uint32_t temperature_soil: 10;       
    uint32_t battery_level: 7;
    uint32_t leaf_wetness: 4;
    uint32_t pad0: 1;

    uint32_t rainrate: 8;
    uint32_t pad1: 24;
} PayloadBF;
```

---

## Problemi - 2

### *Rigging* molto diversi

***

#### Connettività

| No IP Addressable     | IP Addressable     |
| --------------------- | ------------------ |
| LoRaWAN               | Cellular           |
| SigFox                | WiFi               |
|                       | nRF24              |

***

#### Sensori e misure

- Rappresentazione vs Precisione vs Range
- Ambientali, specifici, stato dei dispositivi, stato dei sensori

***

- Gestire le combinazioni
  - Protocolli
  - Dispositivi
  - Formati dei payload

---

![Scrivania](images/Scrivania.jpg) "La mia scrivania alla settimana 2"
