# Cubetto 🤖

 
## Montagem do arduino
![image](https://github.com/user-attachments/assets/9a615668-cbff-4605-bbc0-f5ea874ace50)
![image](https://github.com/user-attachments/assets/6056decb-53ad-4f05-86f7-192a844a66d3)
## Softweres necessarios
  - [Arduino IDE](https://www.arduino.cc/en/software).
  - Serial Bluetooth Terminal **Utilizar no celular, baixe na playstore**
## Configurações do Arduino IDE
- Selecione o Arduino e Porta
![image](https://github.com/user-attachments/assets/a384b525-e998-411a-8187-69109b4ef4ec)
- coloque essas configurações no monitor serial.
![image](https://github.com/user-attachments/assets/79371662-345a-4caa-8e9c-a80d34b732cb)
## Entrar no Modo AT (para HC-05)
Para o módulo HC-05, siga estes passos para entrar no modo AT:
  1. Desconecte o pino VCC do HC-05 (desligue o módulo).
  2. Pressione e segure o botão no módulo Bluetooth (se o seu modelo tiver um botão).
  3. Conecte o pino VCC novamente enquanto ainda estiver pressionando o botão.
  4. A luz do módulo Bluetooth começará a piscar lentamente (1 vez a cada 2 segundos), indicando que está no modo AT.
  5. Solte o botão. Agora o módulo deve estar pronto para receber comandos AT.
## Codigo
```
  #include "SoftwareSerial.h"
  
  // Pinos TX e RX para comunicação com o Bluetooth
  SoftwareSerial bluetooth(10, 11); // TX, RX (Bluetooth)
  
  void setup() {
    // Inicializa a comunicação com o PC via serial
    Serial.begin(38400); 
    // Inicializa a comunicação com o Bluetooth (AT mode)
    bluetooth.begin(38400); // Configura para 9600 bps para comandos AT
  }
  
  void loop() {
    // Recebe dados do Monitor Serial e envia ao Bluetooth
    if (Serial.available()) {
      char r = Serial.read();
      bluetooth.write(r);  // Envia ao HC-05
      Serial.print(r);  // Echo
    }
    // Recebe dados do Bluetooth e envia ao Monitor Serial
    if (bluetooth.available()) {
      char r = bluetooth.read();
      Serial.print(r);  // Exibe a resposta do HC-05
    }
  }
```
Após a Montagem cole este codigo no Arduino IDE.
Segue Abaixo os comandos a serem usados no monitor serial:

1. Configurações do Monitor Serial
Antes de enviar comandos AT ao módulo HC-05, configure corretamente o **Monitor Serial** do Arduino:
- **Taxa de transmissão (baud rate)**: Ajuste conforme a configuração atual do módulo (geralmente 38400 bps).
- **Both NL & CR**: Selecione "Both NL & CR" (Nova Linha & Retorno de Carro) no canto inferior direito.

---

2. Comandos AT Básicos

### Teste de Conexão
- **Testar conexão com o módulo AT**  
  Comando: `AT`  
  Resposta esperada: `OK`

- **Verificar a velocidade de transmissão (baud rate)**  
  Comando: `AT+UART`  
  Resposta esperada: `+UART:9600,1,0` (Exemplo para 9600 bps)

- **Alterar a velocidade de transmissão**  
  Comando: `AT+UART=38400,0,0` (Substitue `9600` por `38400`)  
  Resposta esperada: `OK`

### Configurações de Nome e PIN
- **Verificar o nome atual do módulo**  
  Comando: `AT+NAME`  
  Resposta esperada: `+NAME:HC-05`

- **Alterar o nome do módulo Bluetooth**  
  Comando: `AT+NAME=NomeDesejado`  
  Resposta esperada: `OK`

- **Verificar o PIN de emparelhamento**  
  Comando: `AT+PSWD`  
  Resposta esperada: `+PSWD:1234` (Exemplo de PIN padrão)

- **Alterar o PIN de emparelhamento (Não é necessario ser alterado de a senha for 1234)**  
  Comando: `AT+PSWD=5678` (Substitua `5678` pelo novo PIN desejado)
  Resposta esperada: `OK`

---

3. Configurações de Modo Mestre/Escravo

- **Verificar o modo atual (Mestre ou Escravo)**  
  Comando: `AT+ROLE`  
  Resposta esperada: `+ROLE:0` (Escravo) ou `+ROLE:1` (Mestre)

- **Configurar o módulo para o modo Escravo**  
  Comando: `AT+ROLE=0`  
  Resposta esperada: `OK`

- **Configurar o módulo para o modo Mestre**  
  Comando: `AT+ROLE=1`  
  Resposta esperada: `OK`

---

4. Configuração de Modo de Conexão (CMODE)

O comando **`AT+CMODE`** define se o HC-05 pode se conectar a qualquer dispositivo ou apenas a dispositivos emparelhados.

- **Verificar o valor atual do CMODE**:  
  Comando: `AT+CMODE`  
  Resposta esperada: `+CMODE:1` (Exemplo para CMODE 1)

- **Configurar para se conectar a qualquer dispositivo**:  
  Comando: `AT+CMODE=1`  
  Resposta esperada: `OK`

- **Configurar para se conectar apenas ao dispositivo emparelhado**:  
  Comando: `AT+CMODE=0`  
  Resposta esperada: `OK`

- **Configurar para se conectar apenas a dispositivos previamente emparelhados**:  
  Comando: `AT+CMODE=2`  
  Resposta esperada: `OK`

---

5. Emparelhamento com Outro Dispositivo Bluetooth

- **Iniciar pesquisa por dispositivos Bluetooth próximos (modo Mestre)**  
  Comando: `AT+INQ`  
  Resposta esperada: Lista de endereços de dispositivos detectados.

- **Conectar a um dispositivo Bluetooth (modo Mestre)**  
  Comando: `AT+LINK=<endereço>`  
  Substitua `<endereço>` pelo endereço do dispositivo detectado na pesquisa.

---

6. Resolvendo Problemas Comuns

- **Sem resposta do módulo**:
  - Verifique se o módulo está em **modo AT** (luz piscando lentamente).
  - A **taxa de baud** no código e no Monitor Serial estão corretas.
  - Verifique o uso correto de `bluetooth.begin()` no código.

---

7. **Exemplo de Teste Completo**
Aqui está um exemplo de um ciclo típico de testes:
1. Envie `AT` — você deve receber `OK`.
2. Envie `AT+UART` para verificar a taxa de baud.
3. Envie `AT+NAME=SLAVE` para renomear o dispositivo como escravo.
4. Envie `AT+ROLE=0` para configurar como Escravo.
5. Envie `AT+CMODE=1` para se conectar a qualquer dispositivo.

Após todo processo cole o codigo cubbeto.ino que esta no repositorio no arduino IDE e faça upload pro arduino.
