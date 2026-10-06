# P1 · Aspiradora básica

## Funcionamiento
La aspiradora recorre la casa de forma pseudoaleatoria mediante un autómata de tres estados (AVANZAR, RETROCEDER y GIRAR) que se ejecuta dentro de un bucle infinito, sin ningún `sleep`, como exige el enunciado. Los tiempos los controlo con `time.time()` y un instante límite, de modo que el robot reacciona en todo momento.

La aspiradora avanza mientras el láser no detecte ningún obstáculo a menos de 0,5 m en un cono frontal de ±15º. Además, aunque el bumper está desactivado en Unibotics, el código también lo comprueba como respaldo: si se pulsa, actúa igual que si el obstáculo lo hubiera detectado el láser.

Al detectar un obstáculo, la aspiradora retrocede durante 0,6 s y después gira al azar hacia la izquierda o la derecha un ángulo aleatorio entre 20º y 170º, medido con la orientación (`yaw`). Así el giro es pseudoaleatorio y no pierde tiempo dando vueltas de más. Para que el robot no se quede girando sin fin, hay un timeout de seguridad del doble del tiempo teórico del giro.

En la mejor prueba, la aspiradora llegó a recorrer un 62 % de la casa.

## Estados
```
while True  (Frequency.tick)
├── AVANZAR      v = 0,3 m/s · w = 0
│   └── obstáculo a < 0,5 m        → RETROCEDER
├── RETROCEDER   v = -0,2 m/s · w = 0
│   └── pasan 0,6 s                → GIRAR  (sorteo de sentido y ángulo de 20º a 170º)
└── GIRAR        v = 0 · w = ±1 rad/s
    └── gira el ángulo o salta el timeout → AVANZAR
```

## Demostración
<img width="433" height="746" alt="cobertura_61" src="https://github.com/user-attachments/assets/8ea463dc-9344-40ff-8bc3-01296e3ff614" />
