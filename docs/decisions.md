# Decisiones de diseño

Registro de decisiones técnicas importantes y su justificación.

---

## DDR-001 — Arduino UNO Q como computadora central

**Decisión:** Usar el Arduino UNO Q como único SBC del robot.

**Contexto:** El robot necesita cómputo suficiente para correr ROS2 y procesar video,
más control de motores en tiempo real.

**Justificación:** El UNO Q integra ambas necesidades en una sola placa:
el MPU Qualcomm corre Debian + ROS2 y el MCU STM32 maneja I/O de tiempo real.
Esto elimina la necesidad de una Raspberry Pi + Arduino separados,
reduciendo complejidad de cableado y comunicación.

---

## DDR-002 — Cámara USB en lugar de CSI

**Decisión:** Cámara USB UVC genérica.

**Contexto:** El UNO Q tiene conector CSI pero Arduino no documenta
soporte de drivers para sensores específicos.

**Justificación:** UVC es un estándar soportado nativamente por el kernel Linux
sin instalar drivers adicionales. Prioriza tiempo de desarrollo sobre rendimiento.
Migración a CSI evaluable en fases futuras.

---

## DDR-003 — Driver TB6612FNG en lugar de L298N

**Decisión:** TB6612FNG × 2 para controlar 4 motores.

**Contexto:** Se necesita controlar 4 motores DC con encoder.

**Justificación:** El L298N cae ~2V por canal por su diseño lineal,
reduciendo torque disponible. El TB6612FNG usa MOSFETs — eficiencia
>90%, menor disipación de calor, y footprint más pequeño.
Costo similar.

---

## DDR-004 — Cinemática diferencial con 4 ruedas

**Decisión:** 4 motores controlados como 2 pares (izquierda/derecha).

**Contexto:** Chasis de 4 ruedas con opciones: diferencial, skid steering, mecanum.

**Justificación:** El modelo diferencial es matemáticamente el más limpio
para aprender odometría y PID. Las ruedas del mismo lado giran sincronizadas.
Permite implementar Nav2 con el stack estándar sin configuración especial.
