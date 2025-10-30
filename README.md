# Ponderada de Programação

## Atividade de farol offline

Para a simulação do farol utilizamos a esp32, protoboard, leds, display lcd, buzzer, resistor e jumpers. Primeiro, o sinal verde acende por alguns segundos e aparece no display “Sinal Verde”, indicando que os carros podem seguir. Depois disso, o LED verde começa a piscar mostrando uma contagem regressiva no display. Em seguida, o sinal muda para amarelo, o display mostra “Sinal Amarelo” e o LED amarelo acende por alguns segundos antes de piscar também com uma contagem regressiva. Depois o sinal muda para vermelho, o display mostra “Sinal Vermelho”, o buzzer liga e o pedestre pode atravessar. Durante o vermelho, os LEDs piscam e o display mostra a contagem regressiva até o ciclo recomeçar.

Imagem do projeto
![/assets/imagem.jpeg](/assets/imagem.jpeg)

Abaixo esta o link para o video do projeto feito na mão: [https://drive.google.com/file/d/1bczuLShUH18Ir7g6rWjlQQREcl8dIuAi/view?usp=sharing](https://drive.google.com/file/d/1bczuLShUH18Ir7g6rWjlQQREcl8dIuAi/view?usp=sharing)

Código utilizado na simulação:

```
#include <Wire.h>
#include <LiquidCrystal_I2C.h>


LiquidCrystal_I2C lcd(0x27, 16, 2);

void setup() {
  Serial.begin(115200);
  pinMode(12, OUTPUT); // LED vermelho do pedestre (não atravessar)
  pinMode(14, OUTPUT); // LED verde do pedestre (pode atravessar)
  pinMode(18, OUTPUT); // LED verde do semáforo
  pinMode(2, OUTPUT);  // LED amarelo do semáforo
  pinMode(23, OUTPUT); // LED vermelho do semáforo
  pinMode(25, OUTPUT); // Buzzer sonoro
  lcd.init();
  lcd.backlight();
}

void loop() {
  // Fase verde: carros seguem, pedestres aguardam
  digitalWrite(14, 0);
  digitalWrite(23, 0);
  digitalWrite(18, 1);
  digitalWrite(12, 1);
  Serial.println("Nao atravesse!!");
  lcd.setCursor(0, 0);
  lcd.print("Sinal Verde");
  delay(4000);

  // Contagem regressiva piscando o LED verde
  int cont1 = 7;
  for (int i = 0; i < 6; i++) {
    digitalWrite(18, 0);
    delay(500);
    digitalWrite(18, 1);
    delay(500);
    cont1--;
    lcd.setCursor(0, 1);
    lcd.print(cont1);
  }

  // Fase amarela: atenção, mudança de sinal
  delay(500);
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Sinal Amarelo");
  digitalWrite(18, 0);
  digitalWrite(2, 1);
  lcd.setCursor(0, 0);
  delay(2000);

  // Contagem regressiva piscando o LED amarelo
  int cont2 = 7;
  for (int i = 0; i < 6; i++) {
    digitalWrite(2, 0);
    digitalWrite(12, 0);
    delay(500);
    digitalWrite(2, 1);
    digitalWrite(12, 1);
    delay(500);
    cont2--;
    lcd.setCursor(0, 1);
    lcd.print(cont2);
  }

  // Fase vermelha: carros param, pedestres atravessam
  delay(500);
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Sinal Vermelho");
  digitalWrite(2, 0);
  digitalWrite(12, 0);
  digitalWrite(23, 1);
  digitalWrite(14, 1);
  tone(25, 1000); // Liga o buzzer durante o sinal vermelho
  delay(6000);

  // Contagem regressiva piscando o LED vermelho e pedestre
  int cont3 = 7;
  for (int i = 0; i < 6; i++) {
    digitalWrite(23, 0);
    digitalWrite(14, 0);
    delay(500);
    digitalWrite(23, 1);
    digitalWrite(14, 1);
    delay(500);
    cont3--;
    lcd.setCursor(0, 1);
    lcd.print(cont3);
  }

  // Final do ciclo, limpa o display e desliga o buzzer
  delay(500);
  lcd.clear();
  noTone(25);
}

```
