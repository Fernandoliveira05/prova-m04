# prova-m04

## Aluno: Fernando Soares de Oliveira

Código: 

´´´ cpp
// Incluindo as bibliotecas que utilizaremos
#include <WiFi.h>
#include <WiFiClientSecure.h>
#include <WebServer.h>
#include <HTTPClient.h>

#define led_verde 2 // Pino utilizado para controle do led verda
#define led_vermelho 40 // Pino utilizado para controle do led vermelho
#define led_amarelo 9 // Pino utilizado para controle do led azul
#define ldr 18     // Pino utilizado para o LDR, sensor de luminosidade

// Configuração de internet
const char* ssid = "Galaxy S21 de Fernando";
const char* password = "Fesah2023";

const int botao = 14;  // Pino utilizado para controle do botão
int estadoBotao = 0;  // Variável que armazena o estado do botão, nesse caso ele começa em baixo nível
bool vermelho = false;

int ldr_inicial=600; // Valor utilizado para futuramente definirmos se está de dia ou noite, como efeito de comparação

void setup() {

  // Configuração inicial dos pinos para controle dos leds como OUTPUTs (saídas) do ESP32
  pinMode(led_amarelo,OUTPUT);
  pinMode(led_verde,OUTPUT);
  pinMode(led_vermelho,OUTPUT); 
  pinMode(ldr, INPUT);//Define ldr como saída

  // Inicialização das entradas
  pinMode(botao, INPUT); // Inicializamos o botão como um sensor de entrada, ou seja, manda informações ao ESP32

// Definimos nossos LEDs para iniciarem em baixo nível
  digitalWrite(led_amarelo, LOW);
  digitalWrite(led_verde, LOW);
  digitalWrite(led_vermelho, LOW);

  Serial.begin(9600); // Configuração para debug por interface serial entre ESP e computador com baud rate de 9600

  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.print(".");
  }
  Serial.println("\nWi-Fi conectado!");
  Serial.print("IP do servidor: ");
  Serial.println(WiFi.localIP());

// Debug de eventuais falhas
  while (WiFi.status() != WL_CONNECT_FAILED) {
    delay(100);
    // Para sabermos que ele ainda está tentando conectar
    Serial.print(".");
  }
  // // Saída de sucesso
  Serial.println("Conectado ao WiFi com sucesso!"); // Considerando que saiu do loop acima, o ESP32 agora está conectado ao WiFi (outra opção é colocar este comando dentro do if abaixo)

  // Verifica estado do botão
  estadoBotao = digitalRead(botao);
  if (estadoBotao == HIGH) {
    Serial.println("Botão pressionado!");
  } else {
    Serial.println("Botão não pressionado!");
  }

  if(WiFi.status() == WL_CONNECTED){ // Se o ESP32 estiver conectado à Internet
    HTTPClient http;

    String serverPath = "http://www.google.com.br/"; // Endpoint da requisição HTTP

    http.begin(serverPath.c_str());

    int httpResponseCode = http.GET(); // Código do Resultado da Requisição HTTP

    if (httpResponseCode>0) {
      Serial.print("HTTP Response code: ");
      Serial.println(httpResponseCode);
      String payload = http.getString();
      Serial.println(payload);
      }
    else {
      Serial.print("Error code: ");
      Serial.println(httpResponseCode);
      }
      http.end();
    }

  else {
    Serial.println("WiFi Disconnected");
  }
}


void loop() {
  int ldrstatus=analogRead(ldr);
  Serial.println('ldrstatus');

  if(ldrstatus<=ldr_inicial){
    Serial.print("Ainda está escuro");
    Serial.println(ldrstatus);
    digitalWrite(led_amarelo, HIGH);
    delay(1000); 
    digitalWrite(led_amarelo, LOW);
    delay(1000); 
  }else if(ldrstatus>=ldr_inicial){
    Serial.print("Está de dia!");
    Serial.println(ldrstatus);
    digitalWrite(led_verde, HIGH);
    delay(3000); 
    digitalWrite(led_verde, LOW);
    delay(500);
    digitalWrite(led_amarelo, HIGH);
    delay(2000); 
    digitalWrite(led_amarelo, LOW);
    delay(500);
    digitalWrite(led_vermelho, HIGH);
    delay(5000);
    digitalWrite(led_vermelho, LOW);
    delay(500);

    if (led_vermelho == HIGH){
      vermelho = true; 
    }
    
    Serial.println(vermelho);
    }
  }
´´´


