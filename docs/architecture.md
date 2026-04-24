# Arquitectura del sistema

## Visión general

minibot-ros2 es un robot autónomo de 4 ruedas con arquitectura de dos capas de cómputo.

## Hardware principal

| Capa | Componente | Rol |
|------|-----------|-----|
| Cómputo alto nivel | Arduino UNO Q — MPU Qualcomm QRB2210 | ROS2, navegación, percepción |
| Cómputo tiempo real | Arduino UNO Q — MCU STM32U585 | PWM motores, lectura encoders, PID |
| Percepción | Cámara USB UVC + IMU MPU-6050 | Visión, orientación |
| Actuación | 4× TT Motor + encoder · 2× TB6612FNG | Tracción diferencial |
| Alimentación | LiPo 2S 2200mAh · Buck LM2596 | Potencia motores y lógica |

## Diagrama de capas
```mermaid
graph TD
    subgraph UNO_Q["Arduino UNO Q"]
        MPU["MPU — Qualcomm QRB2210\nDebian Linux · ROS2 Humble\nNav2 · OpenCV · TF2"]
        MCU["MCU — STM32U585\nZephyr · Arduino\nPID · Encoders · PWM"]
        MPU <-->|UART Bridge| MCU
    end

    CAM["Cámara USB UVC"] --> MPU
    IMU["IMU MPU-6050\nI²C"] --> MCU

    MCU --> DRV1["Driver TB6612FNG #1"]
    MCU --> DRV2["Driver TB6612FNG #2"]

    DRV1 --> M1["Motor + encoder\nIzq. delantero"]
    DRV1 --> M2["Motor + encoder\nIzq. trasero"]
    DRV2 --> M3["Motor + encoder\nDer. delantero"]
    DRV2 --> M4["Motor + encoder\nDer. trasero"]

    BAT["LiPo 2S\n7.4V · 2200mAh"] --> BUCK["Buck LM2596\n→ 5V lógica"]
    BAT --> DRV1
    BAT --> DRV2
    BUCK --> UNO_Q
```

## Stack de software

- **OS:** Debian Linux (MPU) · Zephyr RTOS (MCU)
- **Framework:** ROS2 Humble
- **Navegación:** Nav2
- **Visión:** OpenCV
- **Control:** PID implementado en STM32
- **Comunicación MPU↔MCU:** UART bridge (Arduino RouterBridge)
