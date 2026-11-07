# Automatic Lamp with LDR and Ultrasonic Sensor

> **Nome do repositório:** `Automatic-lamp-with-LDR-and-ultrasonic-sensor`  
> **Descrição:** Lâmpada automática com acionamento por sensor de presença ultrassônico e controle de luminosidade por sensor LDR, desenvolvida em C++ para a plataforma Arduino com conceitos de Programação Orientada a Objetos (POO).

---

## 📌 Visão Geral

Este projeto consiste em um sistema de automação residencial/industrial simples responsável pelo acionamento automático de uma lâmpada (ou módulo relé). O acionamento é acionado por dois fatores independentes:

1. **Sensor Ultrassônico (Presença):** Ativa a saída digital se a distância detectada estiver entre **10 cm** e **100 cm**.
2. **Sensor LDR (Luminosidade):** Ativa a saída digital caso a luz ambiente caia abaixo do limite (leitura analógica `< 20`).

O projeto implementa uma abstração de pinos digitais através da classe customizada `gp_io`, demonstrando a separação de código em arquivos de cabeçalho (`.h`) e implementação (`.cpp`).

---

## 🛠️ Hardware Necessário

* 1x Placa Arduino (Uno, Nano ou Mega)
* 1x Sensor Ultrassônico HC-SR04
* 1x Fotoresistor LDR
* 1x Resistor de 10kΩ (para o divisor de tensão do LDR)
* 1x Módulo Relé (ou LED indicador)
* Jumpers e Protoboard

---

## 🔌 Mapeamento de Pinos (Pinout)

| Componente | Pino do Arduino | Função |
| :--- | :--- | :--- |
| **HC-SR04 (Trigger)** | GPIO 9 | Disparo do pulso ultrassônico |
| **HC-SR04 (Echo)** | GPIO 10 | Recebimento do eco |
| **Sensor LDR** | A3 | Leitura analógica de luminosidade |
| **Saída (Relé / Lâmpada)** | GPIO 8 | Sinal de controle (HIGH / LOW) |

---

## 📁 Estrutura de Arquivos

```text
├── gp_io.h        # Guardas de inclusão e protótipo da classe gp_io
├── gp_io.cpp      # Definição e métodos da classe gp_io
└── main.cpp       # Código principal (setup, loop e lógica de controle)
```

---

## 💻 Estrutura do Código

### 1. Cabeçalho da Classe (`gp_io.h`)
Define a interface e utiliza *include guards* para evitar redefinições durante a compilação:

```cpp
#ifndef GP_IO_H
#define GP_IO_H

class gp_io; // Protótipo da classe

#endif
```

### 2. Implementação da Classe (`gp_io.cpp`)
Encapsula o gerenciamento do pino de saída usando os métodos `setgp_io` e `getgp_io`:

```cpp
class gp_io {
    private:
        int gp_io_di; // Número da GPIO

    public:
        void setgp_io(int gpio) {
            gp_io_di = gpio;
        }

        int getgp_io() {
            return gp_io_di;
        }

        uint8_t nivel_alt = HIGH; // Nível lógico alto (1)
        uint8_t nivel_bai = LOW;  // Nível lógico baixo (0)
};
```

---

## ⚙️ Regras de Negócio e Lógica de Funcionamento

No loop principal (`loop()`), o sistema efetua uma leitura a cada **1.250 ms**:

```text
               +-----------------------+
               | Início do Loop (1.25s)|
               +-----------+-----------+
                           |
            +--------------v--------------+
            | Ler Distância (HC-SR04)      |
            | Ler Luminosidade (LDR A3)   |
            +--------------+--------------+
                           |
             /-------------v-------------\
            /                             \
           < 10cm < Distância < 100cm ?    > --- SIM ---> [ Ligar Lâmpada ]
            \                             /
             \-------------+-------------/
                           | NÃO
                           |
             /-------------v-------------\
            /                             \
           <       Luminosidade < 20 ?    > --- SIM ---> [ Ligar Lâmpada ]
            \                             /
             \-------------+-------------/
                           | NÃO
                           v
                   [ Desligar Lâmpada ]
```

---

## 📦 Bibliotecas Utilizadas

* **[Arduino Core](https://github.com/arduino/ArduinoCore-avr):** Biblioteca padrão para suporte de E/S do Arduino.
* **[NewPing](https://bitbucket.org/teckel12/arduino-new-ping/wiki/Home):** Biblioteca otimizada para controle do sensor ultrassônico.

---

## 🚀 Como Compilar e Executar

1. Abra a IDE do Arduino ou VS Code com PlatformIO.
2. Certifique-se de instalar a biblioteca **NewPing** através do gerenciador de bibliotecas.
3. Adicione os arquivos `gp_io.h`, `gp_io.cpp` e o sketch principal ao seu projeto.
4. Conecte o Arduino à porta USB e selecione a placa/porta correspondente.
5. Faça o upload do código.
6. Abra o **Monitor Serial** configurado para **9600 baud** para visualizar os logs de distância e luminosidade.