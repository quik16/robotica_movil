# P3 · Navegación local con VFF

## Funcionamiento

El coche recorre el circuito con navegación local VFF (Virtual Force Field) dentro de un bucle infinito. En cada vuelta del bucle lee el láser, la posición y el siguiente subobjetivo (`WebGUI.getNextTarget()`) y calcula la velocidad lineal V y la angular W. Cuando está a menos de 3 m del subobjetivo lo marca como alcanzado con `setReached(True)` y pide el siguiente.

El subobjetivo se pasa a coordenadas relativas al coche (x hacia delante, y hacia la izquierda), y su dirección es la fuerza atractiva. Las vallas generan una fuerza repulsiva: cada rayo del láser empuja al coche en sentido contrario con una fuerza 1/distancia, así que lo que está cerca pesa más. Como el coche siempre avanza hacia delante, de la repulsión solo uso la componente lateral (Y), que lo mantiene en el centro de la pista. Los rayos `-inf` del láser (algo pegado al coche) los limito a 0,1 m para que no rompan los cálculos.

Para esquivar los coches amarillos miro el pasillo (0,8 m a cada lado) que va del coche hacia el subobjetivo. Si hay algo dentro a menos de 6 m, esquivo hacia el lado contrario al que queda el obstáculo, y si está justo en el centro elijo el lado con más espacio. El lado se mantiene unos instantes después de pasar el obstáculo para no hacer eses. El giro final es W = K_GIRO · (ángulo al subobjetivo + desvío de esquive + repulsión de las vallas), limitado a ±2,5 rad/s.

La velocidad es variable: V_MAX = 4 m/s con el camino libre y, al acercarse a un obstáculo, baja hasta V_MIN = 2,5 m/s en proporción a la distancia. Así va rápido en las rectas y reduce al esquivar.

## Esquema

```
while True  (Frequency.tick)
├── láser → distancias de los 180 rayos
├── subobjetivo → fuerza atractiva (ángulo hacia él)
├── vallas → fuerza repulsiva lateral (1/d por rayo)
├── pasillo hacia el subobjetivo → ¿obstáculo delante? → lado de esquive → desvío
├── W = K_GIRO · (atractiva + esquive + repulsiva)
└── distancia al obstáculo → V (de V_MAX a V_MIN)
```

## Parámetros

| Parámetro | Valor |
|---|---|
| `V_MAX` / `V_MIN` | 4 / 2,5 m/s |
| `D_FRENO` | 3 m |
| `K_GIRO` / `W_MAX` | 1,5 / 2,5 rad/s |
| `K_REP` | 0,01 |
| `D_ESQUIVE` / `ANCHO` | 6 m / 0,8 m |
| `A_AV` | 0,7 rad |
| `FRAMES_HOLD` | 60 vueltas |
| `UMBRAL` | 3 m |

## Vídeo

https://youtu.be/zMB2UllvvHM?si=1IHk3pbUZ_46QUaU
