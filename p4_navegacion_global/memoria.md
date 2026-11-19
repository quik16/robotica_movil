# P4 · Navegación global con Gradient Path Planning (TeleTaxi)

## Funcionamiento

Cuando hago clic en el mapa, el taxi planifica una sola vez el camino hasta ese destino con Gradient Path Planning. Para ello calcula un campo con un valor en cada celda del mapa de 400×400 (cada celda mide 1,25 m). El campo es la suma de dos partes. La primera es un frente de onda que sale del destino, que vale 0: los vecinos suman 1 en recto y √2 en diagonal, y lo calculo con una cola de prioridad (`heapq`). La onda deja de expandirse un poco después de llegar al coche, para no recorrer la ciudad entera. La segunda parte es una penalización por cercanía a los edificios, que crece cuanto más cerca está la celda de una pared (`PESO · (RADIO + 1 − distancia)`), y así el camino va por el centro de la calle. Los edificios valen infinito. Al arrancar compruebo en qué orden devuelve `worldToGrid` las coordenadas, viendo en cuál de los dos órdenes posibles cae el coche sobre la carretera. Si el clic cae dentro de un edificio, el destino se mueve a la carretera más cercana.

Después, en cada vuelta del bucle, le pido consejo al campo: desde la celda del coche voy pasando al vecino con menor valor de entre los que acercan al destino. Exigir que cada paso acerque al destino evita que el coche se quede atascado en un mínimo local. De ese camino me quedo con dos puntos: uno cercano, a 6 celdas, y otro lejano, a 16. W es proporcional al ángulo hacia el punto cercano. V se reduce con el coseno del mayor de los dos ángulos, así que el coche frena antes de llegar a una curva y no cuando ya está en ella. En los últimos 15 m también frena de forma progresiva.

El programa es una máquina de estados: ESPERAR → PLANIFICAR → NAVEGAR → LLEGADO. Al llegar a menos de 3 m del destino se para y espera otro clic. Si hago clic en otro sitio mientras va de camino, vuelve a planificar hacia el nuevo destino. El campo se muestra con `showNumpy` (más oscuro cuanto más cerca del destino) y la ruta con `showPath`.

## Esquema

```
inicio
├── cargar mapa → carretera / edificio
├── calibrar orden de worldToGrid con la pose del coche
└── penalización por obstáculos (se calcula una vez)

while True  (Frequency.tick)
├── ESPERAR    → coche parado hasta que haya un destino nuevo
├── PLANIFICAR → campo = frente de onda (Dijkstra desde el destino) + penalización
│                └── showNumpy (campo) + showPath (camino)
├── NAVEGAR
│   ├── ¿destino nuevo? → PLANIFICAR
│   ├── ¿a menos de 3 m? → LLEGADO
│   ├── bajar gradiente → punto cercano (6) y punto lejano (16)
│   ├── W = KW · ángulo al punto cercano
│   └── V = V_MAX · cos(mayor ángulo), frenando al final
└── LLEGADO    → parar y volver a ESPERAR
```

## Parámetros

| Parámetro | Valor |
|---|---|
| `V_MAX` / `V_MIN` | 6 / 0,8 m/s |
| `KW` / `W_MAX` | 1,2 / 1,2 rad/s |
| `PASOS_CERCA` / `PASOS_LEJOS` | 6 / 16 celdas |
| `RADIO_OBSTACULO` / `PESO_OBSTACULO` | 5 celdas / 3 |
| `MARGEN_EXPANSION` | 80 |
| `DIST_FRENADA` | 15 m |
| `TOLERANCIA_DESTINO` | 3 m |
| `FRECUENCIA` | 20 Hz |

## Demostración

| Trayecto | Tiempo |
|---|---|
| Destino 1 | [X] s |
| Destino 2 | [X] s |
| Destino 3 | [X] s |

Vídeo: [enlace]
