## José Elías Sanhueza Pérez

[English](README.md) · **Español**

Ingeniero en Física (Universidad Andrés Bello, mención en modelamiento
matemático), trabajando en machine learning en Santiago de Chile. Antes fui ML
Engineer en Banco BCI y Product Analyst en AFP Cuprum.

La física me enseñó que un número sin barra de error no es una medición. Casi
todo lo que construyo es un intento de aplicar eso a machine learning: calcular
el techo antes de decir que me acerco a él, ponerle intervalo de confianza a cada
comparación, y publicar el resultado aunque contradiga lo que salí a demostrar.

Los cinco repositorios de abajo hacen eso. En todos ellos hay un hallazgo que
va en contra de mi propia hipótesis.

---

### Proyectos

**[spanish-ner-benchmark](https://github.com/JosElias23/spanish-ner-benchmark)** — ¿Un encoder específico de español gana en NER en español frente a uno multilingüe?

Cinco modelos sobre CoNLL-2002, desde un gazetteer hasta XLM-R, más un servicio
FastAPI con latencia y costo medidos.

*La hipótesis no se sostuvo.* mBERT obtuvo 0,8720 de F1 y BETO 0,8705: una
diferencia de 0,0015, IC 95 % [−0,0086, +0,0113], p = 0,75. Estadísticamente
indistinguibles. Escribir "gana mBERT" habría sido una afirmación sobre ruido de
muestreo. El mejor modelo en desarrollo resultó ser el peor en test, que es
sobreajuste en la selección de modelo capturado en vivo — aunque una vez que las
diez comparaciones pareadas llevan corrección de Holm, ningún par de transformers
se separa en el conjunto de test. En servicio: agrupar 32 documentos por lote da
**4,0× el throughput** de procesarlos de a uno en GPU, y 1,4× en CPU.

**[battery-dispatch-optimizer](https://github.com/JosElias23/battery-dispatch-optimizer)** — ¿Cuánto del valor teórico de una batería de red se puede capturar sin conocer los precios de mañana?

Optimización entera mixta (PuLP/HiGHS) sobre precios pronosticados del mercado
día-adelante, evaluada contra una referencia de previsión perfecta.

**85,7 % del óptimo alcanzable**, frente a 39,5 % de un horario fijo de carga y
descarga. *La sorpresa:* un pronóstico 12 % mejor en MAE compró apenas 2 puntos
de tasa de captura. El despacho no necesita precios exactos, necesita el *orden*
de las horas baratas y caras — una conclusión a la que llegaron por separado dos
experimentos independientes. Correr el mismo optimizador rodante sobre los
precios realizados es lo que permite atribuir el 14 % restante al pronóstico y no
al horizonte: −0,2 % de la brecha es la ventana de 48 horas y el 100,2 % es error
de pronóstico.

**[rl-from-scratch](https://github.com/JosElias23/rl-from-scratch)** — Q-learning y SARSA implementados desde cero, medidos contra un óptimo calculado exactamente.

*El óptimo resultó ser tres números distintos.* La cifra que todo el mundo cita
para FrozenLake, 0,8235, supone tiempo ilimitado; bajo el límite de 100 pasos que
el entorno realmente impone, el techo es 0,7442. Los agentes llegan a 0,7378:
**99,1 % del techo alcanzable, con una política que coincide con la óptima en los
16 estados**.

*Y un resultado que tuve que corregir.* Con una semilla, SARSA alcanzó el tope de
500 pasos de CartPole con varianza cero y ganaba limpio. Con ocho semillas el
orden se invierte (Q-learning 432 contra SARSA 302) y *sigue* sin ser
significativo. Las dos tablas están en el repositorio. En RL tabular la varianza
que importa es entre semillas, no dentro de una evaluación.

**[licitaciones-unspsc](https://github.com/JosElias23/licitaciones-unspsc)** — ¿Un LLM local clasifica licitaciones públicas chilenas mejor que un modelo lineal?

Datos reales de la API OCDS de ChileCompra (CC0), cargados en DuckDB.

*No.* TF-IDF + SVM lineal llega a 0,5578 de accuracy; el LLM en zero-shot obtiene
0,2794: **27,8 puntos menos y 624 veces más lento**. Con ocho ejemplos
*recuperados* cierra buena parte de la brecha; con ocho *aleatorios* no cambia
nada. Pero un voto mayoritario sobre esos mismos ocho ejemplos recuperados, sin
ningún modelo de lenguaje, saca lo mismo a un quinceavo del costo — así que el
resultado lo carga la recuperación, no la generación. Un BETO afinado tampoco le
ganó a TF-IDF, y pierde significativamente cuando su número de épocas se elige en
un mes de validación en vez del mes reservado.

*El hallazgo que más me interesa:* cuantizar a INT8 cuesta 0,53 % de accuracy, lo
que se lee como gratis. No lo es: **el 6,83 % de las predicciones individuales
cambia** mientras toda métrica agregada dice que no pasó nada, y casi la mitad de
esos cambios va de una respuesta incorrecta a otra distinta también incorrecta.

**[noaa-gsod-climate](https://github.com/JosElias23/noaa-gsod-climate)** — ¿Una tabla de resultados que publiqué en 2025 sobrevive a ser recalculada?

Cinco años de datos meteorológicos de NOAA, 20.110.620 registros estación-día,
consultados con SQL.

*No sobrevive.* La tabla del README de ese proyecto reportaba la niebla como el
evento más común con 10,5 %; recalcular desde otra fuente da **lluvia con
25,02 %, unas 4,5 veces más frecuente que la niebla**. El orden estaba invertido
y las magnitudes erradas por cuatro órdenes de magnitud. La salida del notebook
había estado bien siempre — lo que estaba mal era el texto publicado, que es la
falla más incómoda, porque el código es la parte que la gente sí revisa.

Dos caminos independientes coinciden dentro de **1,74 %**, con todas las
diferencias del mismo signo, así que la corrección queda corroborada y no
simplemente afirmada. Contar en SQL en vez de traer el año a pandas corre
**38,7× más rápido**, y un panel balanceado de las 11.475 estaciones que
reportaron los cinco años muestra que **cerca del 14 % del calentamiento
aparente es la red de estaciones cambiando, no el clima**.

---

### De qué son evidencia estos repositorios

| | |
|---|---|
| **Estadística** | Bootstrap pareado para significancia, intervalos de Wilson, Monte Carlo con bootstrap por bloques, varianza entre semillas |
| **NLP** | Fine-tuning con HuggingFace, alineación de etiquetas a sub-tokens, CRF, few-shot por recuperación, DSPy |
| **Optimización** | Formulación MILP, restricciones de complementariedad, horizonte rodante, referencias de previsión perfecta |
| **RL** | Q-learning y SARSA tabulares desde cero, iteración de valor, inducción hacia atrás en horizonte finito |
| **Serving** | FastAPI, Docker, exportación a ONNX, cuantización INT8, medición de latencia, throughput y costo |
| **Datos** | DuckDB, SQL, warehouse en Parquet sobre 20 M de filas, ingesta desde APIs públicas y archivos masivos, detección de fugas, particiones temporales |
| **Ingeniería** | CI en GitHub Actions, 336 tests entre los cinco repositorios, semillas fijas, pipelines reproducibles |

Cada número publicado en estos repositorios lo produce un script y queda
guardado como JSON en `reports/`. Cuando un resultado es flojo, va escrito como
limitación en vez de omitido: cada repositorio tiene un `docs/DECISIONS.md` que
explica qué se decidió, por qué, y qué quedó sin hacer.

---

### Contacto

[LinkedIn](https://www.linkedin.com/in/jose-sanhueza-perez) · Santiago, Chile
