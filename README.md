# nutriente-federated-learning-en-android-sin-google-20260730010736
Cazado en India. IA exótica 2026

Aprendizaje federado en Android sin servicios de Google: qué es, cómo se entrena en el dispositivo, qué herramientas reales existen y qué límites tienen. Todo lo que aquí es dato lleva su fuente en `FUENTES.md`. Lo que es definición o razonamiento de esta pieza se marca como tal. Lo que no tiene fuente se marca **NO SÉ** y no se rellena. Fuentes consultadas el 9 de noviembre de 2026; el detalle está en `FUENTES.md`.

## Qué significa "sin Google" en esta pieza

Definición adoptada aquí, no dato: "sin Google" significa sin servicios de Google — sin Google Play Services, sin Firebase, sin Google Cloud —, no sin ninguna línea de código escrita alguna vez en Google. Esa distinción importa: el motor de entrenamiento en el dispositivo que se usa en Android, LiteRT, es código abierto de Google que se lleva dentro de la propia app y no llama a ningún servicio de Google. Usar esa biblioteca no es usar los servicios de Google. La pieza que sí exige servicios de Google es la vía oficial de Google para esto, y se explica abajo para marcar el contraste.

El teléfono sin servicios de Google es, en la práctica, Android AOSP sin Google Mobile Services (GMS). En ese teléfono no hay Play Services que orquesten nada, así que la federación la tiene que orquestar quien la despliega: su propio servidor, su propio transporte, su propio control de qué dispositivos participan y cuándo.

## 1. Qué es el aprendizaje federado

El aprendizaje federado deja los datos de entrenamiento en el dispositivo y aprende un modelo compartido agregando las actualizaciones calculadas en local. Lo definió así el artículo que fundó el campo: los datos quedan distribuidos en los móviles y se aprende un modelo compartido agregando actualizaciones calculadas localmente (McMahan et al., 2017).

El algoritmo de referencia es FedAvg, del mismo artículo: cada cliente entrena con descenso de gradiente estocástico (SGD) sobre sus propios datos durante varias épocas locales y el servidor promedia los pesos de los modelos. El artículo muestra dos cosas que siguen siendo la base de todo lo demás:

- El coste de comunicación es la restricción principal, y FedAvg reduce las rondas de comunicación necesarias entre 10 y 100 veces frente al descenso de gradiente estocástico sincronizado (McMahan et al., 2017).
- El método es robusto a datos desequilibrados y no IID — es decir, a que cada teléfono tenga datos distintos en cantidad y en clase, que es el caso real (McMahan et al., 2017).

La ronda típica es siempre la misma: el servidor envía el modelo actual; cada teléfono entrena en local con sus datos; cada teléfono devuelve solo la actualización (los pesos o los gradientes); el servidor promedia las actualizaciones y envía el modelo nuevo. Los datos en crudo no salen del teléfono. Eso es lo que el aprendizaje federado garantiza. Lo que no garantiza, por sí solo, es la privacidad: las actualizaciones pueden filtrar información sobre los datos locales. La privacidad diferencial y la agregación segura son mecanismos adicionales, no parte de FedAvg. El sistema académico FEDKIT, por ejemplo, los integra como piezas propias — privacidad diferencial en el dispositivo para limitar la pérdida de privacidad y agregación segura para ocultar las actualizaciones individuales al servidor (FEDKIT, 2024).

## 2. Cómo se entrena en el dispositivo

El entrenamiento en el teléfono no es entrenar un modelo cualquiera desde cero. Es afinar un modelo construido para ello. El mecanismo documentado es el de LiteRT (antes TensorFlow Lite), el motor de inferencia y entrenamiento en el dispositivo de Google AI Edge:

- El modelo se construye con varias funciones expuestas como firmas: entrenar, inferir, guardar y restaurar. Un modelo LiteRT típico solo expone inferencia; para entrenarlo en el dispositivo hay que extenderlo con esas firmas antes de convertirlo (documentación de LiteRT, Google AI Edge).
- Una vez desplegado en la app, el modelo se reentrena en el dispositivo con datos nuevos usando el método `train` de su firma. En Android se hace con las APIs de Java o C++, con la clase `Interpreter` para cargar el modelo y dirigir el entrenamiento (documentación de LiteRT, Google AI Edge).
- El punto de partida es la personalización en el dispositivo: transferencia de aprendizaje para afinar un modelo ya entrenado, no entrenamiento general desde cero (documentación de LiteRT, Google AI Edge).

Tres límites que conviene tener claros desde el principio:

- **No es entrenamiento general.** LiteRT no entrena cualquier modelo ni cualquier arquitectura: el modelo tiene que estar construido para el entrenamiento en el dispositivo, con las operaciones que el motor soporta. Lo que se afina en el teléfono es, en la práctica, un modelo ya entrenado fuera, con sus últimas capas o sus parámetros ajustados con datos locales.
- **El teléfono manda.** La batería, la memoria y el calor limitan cuánto se puede entrenar, y el teléfono solo debería participar cuando está cargando y en Wi-Fi sin contador. Eso ya lo decía el artículo de McMahan en 2017 como supuesto de funcionamiento — clientes que solo participan cargando y en Wi-Fi sin contador — y sigue siendo la regla práctica.
- **Los datos no IID mandan.** Cada teléfono ve un mundo distinto. Un modelo federado que ignora eso converge peor o se sesga hacia los teléfonos que más participan. La selección de clientes — quién entrena en cada ronda — es parte del sistema, no un detalle.

## 3. Las herramientas reales

No hay muchas. Y la honestidad es que la vía para Android sin Google es hoy más estrecha de lo que parece. Estas son las que existen, con su estado real.

### Flower: el camino principal

Flower es un framework abierto para construir sistemas de aprendizaje federado. Es agnóstico al framework de aprendizaje: sirve con PyTorch, TensorFlow, TFLite, JAX, scikit-learn y otros (PyPI, `flwr`). Su artículo de presentación (Beutel et al., 2020) lo diseñó para experimentos a gran escala con dispositivos heterogéneos: sus experimentos llegan a 15 millones de clientes usando solo un par de GPUs de gama alta, y los experimentos pueden migrarse después a dispositivos reales.

Para Android, Flower tiene un inicio rápido con TFLite. Pero el estado real, a fecha de esta pieza, es incómodo y hay que decirlo tal cual: la página oficial del inicio rápido de Android advierte que el SDK experimental de Flower para Android no es compatible con la versión actual de Flower, que el soporte para Android se está rehaciendo y que el tutorial se conserva por motivos históricos hasta que salga el nuevo SDK (documentación de Flower, consultada en noviembre de 2026). La misma página promete el SDK nuevo para 2025; en noviembre de 2026 la advertencia sigue puesta, así que el rehacer lleva al menos un año de retraso.

Qué significa eso en la práctica:

- Flower es hoy la única opción realista para una federación en Android sin servicios de Google: framework abierto, cliente TFLite, servidor que opera quien despliega la federación.
- Pero el SDK de Android está en rehacer. Quien lo use hoy trabaja sobre el tutorial histórico o sobre el cliente que construya a mano contra el protocolo de Flower.
- El framework en sí está vivo: la versión 1.43.0 se publicó en PyPI el 4 de noviembre de 2026 (PyPI, `flwr`).

### FEDKIT: la prueba de que se ha hecho en teléfonos reales

FEDKIT es un sistema académico de aprendizaje federado para Android e iOS. Da entrenadores para TFLite y Core ML, usa aceleración por GPU y NPU, y permite entregar modelos de forma continua sin actualizar la app. Es de código abierto y se desplegó en un caso real de análisis de datos de salud en campus universitarios (FEDKIT, 2024).

Es la referencia más cercana a "esto ya funcionó en teléfonos reales": el sistema coordina clientes Android e iOS desde un único servidor, y cada cliente entrena su modelo local con sus datos privados.

### TensorFlow Federated: la referencia de Google, solo para simular

TensorFlow Federated (TFF) es el framework de Google para expresar cómputos federados. Su propia página de preguntas frecuentes dice que hoy no se puede usar en producción, por ejemplo en móviles: la versión actual está pensada para experimentación, para expresar algoritmos nuevos o probar aprendizaje federado con los propios datos usando el motor de simulación incluido (documentación de TensorFlow Federated).

Para esta pieza, TFF es una referencia para simular y probar algoritmos, no una vía de despliegue en Android sin Google. Es de Google y, además, no despliega en teléfonos reales.

### OpenFL: federación entre instituciones, no entre teléfonos

OpenFL es un framework abierto de Intel, en Python, que funciona con TensorFlow y PyTorch. La federación se define en un plan en YAML, las conexiones usan mTLS y el diseño está pensado para aprendizaje entre instituciones que no comparten sus datos — su caso de origen es la salud (Foley et al., 2022). Desde marzo de 2023 es proyecto en incubación de LF AI & Data, de la Linux Foundation.

Para Android, OpenFL no es el camino: en las fuentes consultadas no hay cliente para Android ni soporte para móviles. Es federación entre silos — hospitales, empresas, centros de datos —, no entre teléfonos. Se cita aquí para no confundirla con una opción móvil.

### La vía oficial de Google, para marcar el contraste

Google tiene su propia vía en Android: el Servidor de Cómputo Federado de On-Device Personalization, dentro de Privacy Sandbox. El cliente es un módulo central de Android que entrena en el dispositivo y el servidor agrega los resultados en un entorno de ejecución de confianza (TEE). Su implementación por defecto usa Google Cloud: Spanner, Cloud Storage y Confidential Space (Privacy Sandbox, Google).

Es justo lo que esta pieza evita: funciona, pero depende de los servicios de Google de principio a fin. Se cita para que quede claro qué se está dejando fuera y por qué.

### FedOps Mobile: lo que parece una opción y no lo es

FedOps Mobile es un framework académico para móviles con TFLite y CoreML, con selección inteligente de clientes según batería y red (MDPI Electronics, 2024). Pero su gestión de energía y control remoto funcionan vía Firebase. Firebase es un servicio de Google. Un sistema que depende de Firebase para operar no es un sistema sin Google, aunque el entrenamiento ocurra en el teléfono. Se cita como advertencia: muchos trabajos de aprendizaje federado en móvil se apoyan en Firebase sin decirlo en el título.

### ExecuTorch y ONNX Runtime: inferencia, no entrenamiento

ExecuTorch es la solución de PyTorch para llevar modelos al dispositivo, y ONNX Runtime Mobile es el motor de inferencia para modelos ONNX en Android e iOS. Los dos resuelven la inferencia en el teléfono. Sobre si sirven para entrenar en el dispositivo dentro de una federación, **NO SÉ**: en las fuentes consultadas no aparece soporte de entrenamiento en el dispositivo para aprendizaje federado en ninguno de los dos. El artículo de ExecuTorch lo presenta como framework de despliegue e inferencia (ExecuTorch, MLSys 2026). No se afirma aquí que no puedan hacerlo; se afirma que no se encontró fuente que lo muestre.

## 4. Cómo se arma, en la práctica

Con lo que hay, la pila realista para Android sin Google es esta:

1. **Modelo:** se entrena fuera del teléfono (en un servidor propio) y se convierte a LiteRT con las firmas de entrenamiento expuestas. El teléfono afina, no entrena desde cero.
2. **Cliente en el teléfono:** una app Android que carga el modelo con LiteRT, entrena en local con los datos del usuario cuando el teléfono está cargando y en Wi-Fi, y devuelve solo la actualización.
3. **Orquestación:** Flower, con su servidor operado por quien despliega la federación. El cliente es TFLite; el tutorial histórico de Flower para Android es el punto de partida, con la advertencia de que el SDK está en rehacer.
4. **Agregación:** FedAvg en el servidor. Privacidad diferencial y agregación segura, si se quieren garantías reales, como piezas adicionales — no vienen dadas por FedAvg.

Lo que esta pila no resuelve por sí sola: la selección de clientes, el control de versiones del modelo, la medida de cuánto cuesta cada ronda en batería y datos, y la defensa contra clientes que envían actualizaciones malas o envenenadas. Todo eso es trabajo del que despliega, no del framework.

## 5. Límites honestos

- **El SDK de Android de Flower está en rehacer.** La documentación oficial lo dice. Quien construya hoy sobre el tutorial histórico construye sobre algo que el propio proyecto marca como histórico.
- **TFF no despliega en móviles.** Es simulación. No hay herramienta de producción para teléfonos.
- **OpenFL no es para teléfonos.** Es federación entre instituciones.
- **La vía oficial de Google exige Google.** Privacy Sandbox y su servidor federado corren sobre Google Cloud por defecto.
- **Muchos trabajos académicos dependen de Firebase.** FedOps Mobile es el ejemplo visto: el entrenamiento es local, pero el control es Firebase.
- **El entrenamiento en el dispositivo es afinado, no entrenamiento general.** LiteRT entrena el modelo que fue construido para ello.
- **La privacidad no es automática.** Que los datos no salgan del teléfono no significa que las actualizaciones no filtren información. Sin privacidad diferencial y agregación segura no hay garantía formal, solo una barrera práctica.
- **Los datos no IID y la heterogeneidad de dispositivos son el problema central.** Lo dice el artículo fundacional y lo repite cada sistema posterior. Un teléfono viejo con pocos datos y un teléfono nuevo con muchos datos no aportan lo mismo, y el promedio simple lo nota.

## 6. Qué queda sin saber

- **NO SÉ** si el SDK de Android de Flower rehecho ya se publicó: la página oficial consultada en noviembre de 2026 sigue mostrando la advertencia de incompatibilidad y el aviso de rehacer.
- **NO SÉ** si ExecuTorch u ONNX Runtime soportan entrenamiento en el dispositivo utilizable para aprendizaje federado: no se encontró fuente que lo muestre.
- **NO SÉ** cuánto cuesta una ronda federada real sin servicios de Google en batería y datos móviles: no se encontró una medida publicada para esta pila concreta.
- **NO SÉ** si existe hoy alguna app en producción que haga aprendizaje federado en AOSP sin GMS: no se encontró fuente que lo afirme.
- **NO SÉ** qué significa "Cazado en India. IA exótica 2026", la línea original de este repositorio: se conserva tal cual, sin interpretar.

## Fuentes

Todas las afirmaciones con dato remiten a `FUENTES.md`, con URL y fecha. Nada de lo escrito aquí es una promesa de que el sistema funcione: es lo que las fuentes dicen que existe, con sus límites.
