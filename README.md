# Arquitetura de Computadores AP1
## Sketch do Projeto - Sistema de Detecção de Alvo
// Alunos: 
// Rai Lamper de Avila - 202402627279 - TA
// Gabriel Maia Sampaio - 202402627295 - TA
// André Luiz Mendes do Nascimento Ribeiro - 202401020631 - TA
// Vinícius Marinho Queiroz - 202407321976 - TA
// Victor Alvarenga Hwang - 202208766005 - TA

const int triggerPin = 2;
const int echoPin = 3;
const int buzzerPin = 4;

const int led1 = 5;
const int led2 = 6;
const int led3 = 7;
const int led4 = 8;
const int led5 = 9;

const int chave1Pin = 10;
const int chave2Pin = 11;

const int ledChave1 = 12;
const int ledChave2 = 13;

long duracao;
float distancia;

int freqBaixa = 500;
int freqAlta = 1000;

void setup() {
  Serial.begin(9600);

  pinMode(triggerPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(buzzerPin, OUTPUT);

  pinMode(led1, OUTPUT);
  pinMode(led2, OUTPUT);
  pinMode(led3, OUTPUT);
  pinMode(led4, OUTPUT);
  pinMode(led5, OUTPUT);

  pinMode(chave1Pin, INPUT_PULLUP);
  pinMode(chave2Pin, INPUT_PULLUP);

  pinMode(ledChave1, OUTPUT);
  pinMode(ledChave2, OUTPUT);
}

void loop() {
  int chave1 = digitalRead(chave1Pin);
  int chave2 = digitalRead(chave2Pin);

  digitalWrite(ledChave1, chave1);
  digitalWrite(ledChave2, chave2);

  bool sistemaLigado = !(chave1 == 1 && chave2 == 1);
  bool somHabilitado = (chave1 == 0 && chave2 == 0);

  if (!sistemaLigado) {
    desligarTudo();
    return;
  }

  distancia = medirDistancia();

  Serial.print("Distância: ");
  Serial.print(distancia);
  Serial.println(" cm");

  if (distancia > 400) {
    desligarTudo();
  } else if (distancia > 200) {
    modoAlertaVisualModerado(somHabilitado);
  } else if (distancia > 100) {
    modoAlertaVisualRapido(somHabilitado);
  } else {
    modoCritico(somHabilitado);
  }

  delay(100);
}

float medirDistancia() {
  digitalWrite(triggerPin, LOW);
  delayMicroseconds(2);
  digitalWrite(triggerPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(triggerPin, LOW);

  duracao = pulseIn(echoPin, HIGH);
  float d = duracao * 0.034 / 2;
  return d;
}

void desligarTudo() {
  for (int i = 5; i <= 9; i++) {
    digitalWrite(i, LOW);
  }
  noTone(buzzerPin);
}

void modoAlertaVisualModerado(bool som) {
  digitalWrite(led1, HIGH);
  digitalWrite(led2, LOW);
  digitalWrite(led3, HIGH);
  digitalWrite(led4, LOW);
  digitalWrite(led5, LOW);

  if (som) {
    tone(buzzerPin, freqBaixa);
  }

  delay(300);

  digitalWrite(led1, LOW);
  digitalWrite(led2, HIGH);
  digitalWrite(led3, LOW);
  digitalWrite(led4, HIGH);
  delay(300);

  noTone(buzzerPin);
}

void modoAlertaVisualRapido(bool som) {
  digitalWrite(led1, HIGH);
  digitalWrite(led2, HIGH);
  digitalWrite(led3, LOW);
  digitalWrite(led4, LOW);
  digitalWrite(led5, LOW);

  if (som) {
    tone(buzzerPin, freqAlta);
  }

  delay(150);

  digitalWrite(led1, LOW);
  digitalWrite(led2, LOW);
  digitalWrite(led3, HIGH);
  digitalWrite(led4, HIGH);

  delay(150);

  noTone(buzzerPin);
}

void modoCritico(bool som) {
  for (int i = 5; i <= 9; i++) {
    digitalWrite(i, HIGH);
  }

  if (som) {
    tone(buzzerPin, freqAlta);
  } else {
    noTone(buzzerPin);
  }
}

