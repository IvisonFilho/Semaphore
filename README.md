# Semaphore


# Desenvolvimento de Controle de Sinal Semaforizado com Informações Visuais

## Introdução
Olá, estudantes! Bem-vindos à nossa tarefa prática sobre **Introdução ao Desenvolvimento de Software Embarcado com Microcontroladores**. Nesta atividade, você deverá implementar um sistema semafórico para pedestres utilizando a placa **Raspberry Pi Pico W**. A lógica do semáforo já foi parcialmente implementada no código fornecido, mas será necessário adicionar a funcionalidade de exibir mensagens em um display OLED conectado à placa.

## Objetivo
O sistema deve exibir mensagens no **OLED** que informem o estado atual do semáforo. As mensagens a serem exibidas são as seguintes:

- **"SINAL ABERTO - ATRAVESSAR COM CUIDADO"** quando o LED verde estiver ativo.
- **"SINAL DE ATENÇÃO - PREPARE-SE"** quando o LED amarelo estiver ativo.
- **"SINAL FECHADO - AGUARDE"** quando o LED vermelho estiver ativo.

## Orientações
- Utilize o **display OLED** com o driver **SSD1306**.
- A comunicação com o display será feita via protocolo **I2C**.
- No código fornecido, insira chamadas para exibir mensagens no OLED dentro das funções **SinalAberto**, **SinalAtencao** e **SinalFechado**.
- As mensagens devem refletir claramente o estado atual do semáforo.

## Código Inicial
Abaixo está o trecho do código que você deve complementar com a funcionalidade do OLED:

```c
#include "pico/stdlib.h"

#define LED_R_PIN 13
#define LED_G_PIN 11
#define LED_B_PIN 12

#define BTN_A_PIN 5

int A_state = 0;    // Botão A está pressionado?

void SinalAberto(){
    gpio_put(LED_R_PIN, 0);
    gpio_put(LED_G_PIN, 1);
    gpio_put(LED_B_PIN, 0);   
}

void SinalAtencao(){
    gpio_put(LED_R_PIN, 1);
    gpio_put(LED_G_PIN, 1);
    gpio_put(LED_B_PIN, 0);
}

void SinalFechado(){
    gpio_put(LED_R_PIN, 1);
    gpio_put(LED_G_PIN, 0);
    gpio_put(LED_B_PIN, 0);
}

int WaitWithRead(int timeMS){
    for(int i = 0; i < timeMS; i = i + 100){
        A_state = !gpio_get(BTN_A_PIN);
        if(A_state == 1){
            return 1;
        }
        sleep_ms(100);
    }
    return 0;
}

int main(){
    
    // INICIANDO LEDS
    gpio_init(LED_R_PIN);
    gpio_set_dir(LED_R_PIN, GPIO_OUT);
    gpio_init(LED_G_PIN);
    gpio_set_dir(LED_G_PIN, GPIO_OUT);
    gpio_init(LED_B_PIN);
    gpio_set_dir(LED_B_PIN, GPIO_OUT);

    // INICIANDO BOTÃO
    gpio_init(BTN_A_PIN);
    gpio_set_dir(BTN_A_PIN, GPIO_IN);
    gpio_pull_up(BTN_A_PIN);
    
    while(true){
        SinalAberto();
        A_state = WaitWithRead(8000);   // Espera com leitura do botão

        if(A_state){ // Alguém apertou o botão - sai do semáforo normal
            // SINAL AMARELO PARA OS CARROS POR 5s
            SinalAtencao();
            sleep_ms(5000);

            // SINAL VERMELHO PARA OS CARROS POR 10s
            SinalFechado();
            sleep_ms(10000);

        } else { // Ninguém apertou o botão - continua no semáforo normal
            SinalAtencao();
            sleep_ms(2000);

            // SINAL VERMELHO PARA OS CARROS POR 15s
            SinalFechado();
            sleep_ms(8000);
        }
    }

    return 0;
}
```

## Instruções para a Implementação
1. **Display OLED (SSD1306)**: Certifique-se de que o display OLED está corretamente conectado à placa **Raspberry Pi Pico W**.
2. **Biblioteca SSD1306**: Instale corretamente a biblioteca necessária para o controle do display OLED (como **ssd1306**).
3. **Modificação do Código**: Complete as funções **SinalAberto**, **SinalAtencao** e **SinalFechado** para incluir a exibição das mensagens no OLED.

## Observações
- Realize os testes com o hardware para verificar se o semáforo está funcionando corretamente e as mensagens são exibidas conforme esperado.
- Certifique-se de que a comunicação **I2C** esteja funcionando corretamente para o display OLED.

## Instrução para Submissão
1. Reúna todos os entregáveis (Código completo `.c`, arquivos **CMakeLists.txt**, demais arquivos e diretórios) em um único arquivo **ZIP**.
2. Submeta o arquivo na plataforma **Moodle** até a data limite estipulada.
