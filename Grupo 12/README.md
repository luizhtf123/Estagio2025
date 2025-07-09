# Estagio2025
Repositório para o estágio do processo seletivo 2025
//Abaixar a rampa.

//Depois disso, andar para frente o tempo todo, até que:

//Veja branco atrás (sensor de cor traseiro): gira 180° para não sair da arena.

//Veja o inimigo à frente por 3 segundos: ataca com a rampa (sobe e desce).

//E repete esse comportamento enquanto se move para frente sem parar.




#define MOTOR_LEFT OUT_A
#define MOTOR_RIGHT OUT_C
#define MOTOR_RAMPA OUT_B

#define SENSOR_COR_ESQUERDO IN_1
#define SENSOR_COR_DIREITO  IN_2
#define SENSOR_US           IN_4

#define DISTANCIA_PERTO     25   // centímetros
#define COR_BRANCA          6    // valor para branco (ajustar conforme teste)

task main() {
    // Configuração dos sensores
    SetSensorLight(SENSOR_COR_ESQUERDO);
    SetSensorLight(SENSOR_COR_DIREITO);
    SetSensorUltrasonic(SENSOR_US);

    // 1. Rampa sobe
    OnFwd(MOTOR_RAMPA, 75);
    Wait(1000); // 1 segundo para subir
    Off(MOTOR_RAMPA);

    // 2. Espera 5 segundos
    Wait(5000);

    // 3. Rampa abaixa
    OnRev(MOTOR_RAMPA, 75);
    Wait(1000); // 1 segundo para descer
    Off(MOTOR_RAMPA);

    // 4. Inicia movimentação constante
    OnFwd(MOTOR_LEFT, 75);
    OnFwd(MOTOR_RIGHT, 75);

    // 5. Loop principal
    while (true) {
        // Verifica se algum sensor de cor traseiro vê a borda (branca)
        if (Sensor(SENSOR_COR_ESQUERDO) == COR_BRANCA || Sensor(SENSOR_COR_DIREITO) == COR_BRANCA) {
            // Gira 180° (escape da borda)
            OnFwd(MOTOR_LEFT, 75);
            OnRev(MOTOR_RIGHT, 75);
            Wait(1800); // tempo para girar 180° (ajustável)
            OnFwd(MOTOR_LEFT, 75);  // volta a andar para frente
            OnFwd(MOTOR_RIGHT, 75);
        }

        // Verifica inimigo próximo com o sensor ultrassônico
        if (SensorUS(SENSOR_US) < DISTANCIA_PERTO) {
            Wait(3000); // espera 3s e verifica de novo
            if (SensorUS(SENSOR_US) < DISTANCIA_PERTO) {
                // Ataca com a rampa
                OnFwd(MOTOR_RAMPA, 75); // sobe
                Wait(1000);
                Off(MOTOR_RAMPA);

                OnRev(MOTOR_RAMPA, 75); // desce
                Wait(1000);
                Off(MOTOR_RAMPA);

                // Mantém movimento para frente após ataque
                OnFwd(MOTOR_LEFT, 75);
                OnFwd(MOTOR_RIGHT, 75);
            }
        }

        Wait(50); // pequena pausa para evitar sobrecarga da CPU
    }
}
