# P2 · Sigue líneas

## Funcionamiento

El coche sigue la línea roja del circuito con un control PID reactivo, dentro de un bucle infinito. En cada vuelta del bucle lee la imagen de la cámara, localiza la línea y decide cuánto girar y a qué velocidad avanzar, sin mapa ni planificación.

Para localizar la línea paso la imagen a HSV y filtro el rojo con dos rangos (el rojo está en los dos extremos del tono). De la máscara me quedo con dos franjas horizontales y calculo el centro de la línea en cada una con los momentos de la imagen. La franja cercana (55 %-65 % del alto) da el error que usa el PID para girar: la diferencia entre el centro de la imagen y el centro de la línea. La franja lejana (48 %-53 %) ve la carretera que viene y sirve para decidir la velocidad.

El giro lo calcula un PID (KP = 0,009, KI = 0,00001, KD = 0,05) sobre el error suavizado con un filtro (ALFA = 0,5), con la integral acotada y la velocidad angular limitada a ±2,5 rad/s para evitar volantazos. La velocidad es adaptativa: si la línea lejana está centrada (recta) va a 20 y, cuanto más desplazada está (curva), baja hasta 5, de modo que frena antes de entrar en la curva. Si el coche pierde la línea, avanza despacio y gira hacia donde la ve (franja lejana) o, si no la ve, hacia el lado por el que se fue (signo del último error) hasta recuperarla, y reinicia la integral para evitar volantazos.

En la mejor prueba, el coche dio la vuelta al Simple Circuit en **70 s**.

## Esquema

```
while True  (Frequency.tick)
├── imagen → máscara roja (HSV)
├── franja cercana → error → filtro → PID (w limitada) → w
├── franja lejana  → error → velocidad adaptativa → v
└── si no se ve la línea cercana → BUSCAR (v = 1 · w = ±1, hacia la línea o el último error)
```

## Parámetros

| Parámetro | Valor |
|---|---|
| `V_MAX` / `V_MIN` | 20 / 5 |
| `ERROR_CURVA` | 80 px |
| `KP` / `KI` / `KD` | 0,009 / 0,00001 / 0,05 |
| `W_MAX` | 2,5 |
| `ALFA` | 0,5 |
| `V_BUSQUEDA` / `W_BUSQUEDA` | 1,0 / 1,0 |

## Demostración

| Circuito | Tiempo por vuelta |
|---|---|
| Simple Circuit | 70 s |
| [otro circuito] | [X] s |

Vídeo: [enlace]
