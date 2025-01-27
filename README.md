# Semaphore
Projeto EmbarcaTech

Desenvolvimento de Controle de Sinal Semaforizado com Informações Visuais

Olá, estudantes! Bem-vindos à nossa tarefa prática sobre o Introdução ao Desenvolvimento de Software Embarcado com Microcontroladores​. Nesta atividade, você deverá implementar um sistema semafórico para pedestres utilizando a placa Raspberry Pi Pico W. A lógica do semáforo já foi parcialmente implementada no código fornecido, mas será necessário adicionar a funcionalidade de exibir mensagens em um display OLED conectado à placa. 

Para esta tarefa, segue as instruções conforme as etapas a seguir:

1. Objetivo

O sistema deverá exibir no OLED mensagens que informem o estado atual do semáforo. Por exemplo: 

"SINAL ABERTO - ATRAVESSAR COM CUIDADO" quando o LED verde estiver ativo. 
"SINAL DE ATENÇÃO - PREPARE-SE" quando o LED amarelo estiver ativo.
"SINAL FECHADO - AGUARDE" quando o LED vermelho estiver ativo. 

2. Orientações
Utilize o display OLED com o driver SSD1306. 
A comunicação com o display será feita via protocolo I2C. 
No código fornecido, insira chamadas para exibir mensagens no OLED dentro das funções SinalAberto, SinalAtencao e SinalFechado. 
As mensagens devem refletir claramente o estado atual do semáforo.

3. Código Inicial
Abaixo está o trecho do código que você deve complementar com a funcionalidade do OLED:

#include "pico/stdlib.h"

#define LED_R_PIN 13
#define LED_G_PIN 11
#define LED_B_PIN 12

#define BTN_A_PIN 5

int A_state = 0;    //Botao A está pressionado?

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
    for(int i = 0; i < timeMS; i = i+100){
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

    // INICIANDO BOTÄO
    gpio_init(BTN_A_PIN);
    gpio_set_dir(BTN_A_PIN, GPIO_IN);
    gpio_pull_up(BTN_A_PIN);
    

    while(true){

        SinalAberto();
        A_state = WaitWithRead(8000);   //espera com leitura do botäo
        //sleep_ms(8000);


        if(A_state){               //ALGUEM APERTOU O BOTAO - SAI DO SEMAFORO NORMAL
            //SINAL AMARELO PARA OS CARROS POR 5s
            SinalAtencao();
            sleep_ms(5000);

            //SINAL VERMELHO PARA OS CARROS POR 10s
            SinalFechado();
            sleep_ms(10000);

        }else{                          //NINGUEM APERTOU O BOTAO - CONTINUA NO SEMAFORO NORMAL
                                      
            SinalAtencao();
            sleep_ms(2000);

            //SINAL VERMELHO PARA OS CARROS POR 15s
            SinalFechado();
            sleep_ms(8000);
        }
                
    }

    return 0;

}

  
4. Observações
Certifique-se de instalar corretamente a biblioteca para o display OLED (como ssd1306). 
Realize os testes com o hardware para verificar o funcionamento do semáforo e a exibição das mensagens no OLED.

5. Instrução para submissão
Reúna todos os entregáveis (Código completo .c, arquivos CMAKELists.txt, demais arquivos e diretórios) em um único arquivo em formato ZIP.
Submeta o arquivo na plataforma Moodle até a data limite estipulada.
