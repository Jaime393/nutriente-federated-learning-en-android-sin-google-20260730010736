# Fuentes

Fuentes consultadas el 9 de noviembre de 2026, salvo fecha de publicación indicada. Cada fuente dice qué afirma; nada fuera de esta lista se usa como dato en el README.

## 1. El algoritmo: FedAvg

**McMahan, Moore, Ramage, Hampson y Agüera y Arcas — "Communication-Efficient Learning of Deep Networks from Decentralized Data", AISTATS 2017.**
- URL: https://arxiv.org/abs/1602.05629
- Fecha: enviado el 17 de febrero de 2016; última revisión 26 de enero de 2023 (v4). Publicado en AISTATS 2017.
- Afirma: el aprendizaje federado deja los datos en los móviles y aprende un modelo compartido agregando actualizaciones calculadas en local; FedAvg combina SGD local en cada cliente con promediado de modelos en el servidor; es robusto a datos desequilibrados y no IID; reduce las rondas de comunicación necesarias entre 10 y 100 veces frente al descenso de gradiente estocástico sincronizado; la evaluación cubre cinco arquitecturas de modelo y cuatro conjuntos de datos.

## 2. El framework principal: Flower

**Beutel, Topal, Mathur, Qiu, Fernandez-Marques, Gao, Sani, Li, Parcollet, Porto Buarque de Gusmão y Lane — "Flower: A Friendly Federated Learning Research Framework".**
- URL: https://arxiv.org/abs/2007.14390
- Fecha: enviado el 28 de julio de 2020; última revisión el 5 de marzo de 2022 (v5).
- Afirma: Flower es un framework para experimentos de aprendizaje federado a gran escala con dispositivos heterogéneos; sus experimentos llegan a 15 millones de clientes usando solo un par de GPUs de gama alta; los experimentos pueden migrarse a dispositivos reales.

**Documentación de Flower — "Quickstart Android".**
- URL: https://flower.ai/docs/framework/main/en/tutorial-quickstart-android.html
- Fecha: consultada el 9 de noviembre de 2026.
- Afirma: el SDK experimental de Flower para Android no es compatible con la versión actual de Flower; el soporte para Android se está rehaciendo; el tutorial se conserva por motivos históricos hasta que salga el nuevo SDK; la página promete el SDK nuevo para 2025.

**PyPI — `flwr`.**
- URL: https://pypi.org/project/flwr/
- Fecha: versión 1.43.0, 4 de noviembre de 2026.
- Afirma: Flower es agnóstico al framework de aprendizaje (PyTorch, TensorFlow, TFLite, JAX, scikit-learn y otros); el inicio rápido de Android con TFLite está entre sus ejemplos.

## 3. El motor en el dispositivo: LiteRT

**Google AI Edge — "On-Device Training with LiteRT".**
- URL: https://developers.google.com/edge/litert/conversion/tensorflow/build/ondevice_training?authuser=00
- Fecha: consultada el 9 de noviembre de 2026.
- Afirma: el entrenamiento en el dispositivo permite mejorar o personalizar un modelo sin que los datos salgan del dispositivo del usuario; el modelo debe exponer las firmas de entrenar, inferir, guardar y restaurar; una vez desplegado, el modelo se reentrena en el dispositivo con datos nuevos; en Android se hace con las APIs de Java o C++, con la clase `Interpreter`.

## 4. La referencia de Google: TensorFlow Federated

**TensorFlow — "Frequently Asked Questions | TensorFlow Federated".**
- URL: https://www.tensorflow.org/federated/faq
- Fecha: consultada el 9 de noviembre de 2026.
- Afirma: TensorFlow Federated no se puede usar hoy en producción, por ejemplo en móviles; la versión actual está pensada para experimentación, para expresar algoritmos nuevos o probar aprendizaje federado con los propios datos usando el motor de simulación incluido.

## 5. La vía oficial de Google en Android: Federated Compute

**Privacy Sandbox — "On-Device Personalization Federated Compute Server".**
- URL: https://privacysandbox.google.com/protections/on-device-personalization/federated-compute-server
- Fecha: consultada el 9 de noviembre de 2026.
- Afirma: el cliente de cómputo federado es un módulo central de Android que entrena modelos en el dispositivo; el servidor federado agrega los resultados en un entorno de ejecución de confianza (TEE); la implementación por defecto usa Google Cloud (Spanner, Cloud Storage, Confidential Space).

## 6. Federación entre instituciones: OpenFL

**Foley et al. — "OpenFL: the open federated learning library", Physics in Medicine & Biology 67 (2022) 214001.**
- URL: https://pmc.ncbi.nlm.nih.gov/articles/PMC9715347/
- Fecha: recibido el 4 de mayo de 2022; publicado el 7 de noviembre de 2022. DOI: 10.1088/1361-6560/ac97d9.
- Afirma: OpenFL es una biblioteca abierta en Python para entrenar modelos con TensorFlow y PyTorch sin compartir los datos; la federación se define en un plan en YAML; las conexiones usan mTLS; nació en salud y se desarrolla para aplicaciones generales; su código está en github.com/intel/openfl.

**Open Source Initiative — "OpenFL: Train machine learning models without sharing data".**
- URL: https://opensource.net/openfl-train-machine-learning-models-without-sharing-data/
- Fecha: consultada el 9 de noviembre de 2026.
- Afirma: en marzo de 2023 la Linux Foundation, a través de LF AI & Data, aceptó OpenFL como proyecto en incubación.

## 7. La prueba en teléfonos reales: FEDKIT

**FEDKIT: Enabling Cross-Platform Federated Learning for Android and iOS.**
- URL: https://arxiv.org/pdf/2402.10464
- Fecha: arXiv:2402.10464.
- Afirma: FEDKIT es un sistema de aprendizaje federado para Android e iOS, con entrenadores para TFLite y Core ML y aceleración por GPU y NPU; permite entregar modelos de forma continua sin actualizar la app; es de código abierto; se desplegó en un caso real de análisis de datos de salud en campus universitarios.

## 8. La advertencia: FedOps Mobile

**"A Platform of Federated Learning Management for Enhanced Mobile Collaboration", Electronics (MDPI) 2024, 13(20):4104.**
- URL: https://www.mdpi.com/2079-9292/13/20/4104/pdf
- Fecha: publicado el 18 de octubre de 2024.
- Afirma: FedOps Mobile usa TFLite y CoreML para entrenar en el dispositivo en Android e iOS, con selección de clientes según batería y red; su gestión de energía y control remoto funcionan vía Firebase.

## 9. Lo que es solo inferencia: ExecuTorch

**"ExecuTorch — A Unified PyTorch Solution to Run AI Models On-Device", MLSys 2026 (Industry Track).**
- URL: https://arxiv.org/pdf/2605.08195
- Fecha: arXiv:2605.08195; actas de MLSys 2026.
- Afirma: ExecuTorch es un framework de PyTorch para desplegar modelos en el dispositivo, desde microcontroladores a teléfonos, con backends para Android (XNNPACK, Vulkan, Qualcomm, MediaTek). Se cita solo para lo que ExecuTorch es — despliegue e inferencia —; de esta fuente no se desprende soporte de entrenamiento en el dispositivo para aprendizaje federado.

## Visto y no usado (por falta de fuente primaria verificable)

- **El ejemplo de personalización en el dispositivo de TensorFlow (blog, 12 de diciembre de 2019, Pavel Senchanka).** Se vio citado en la documentación y en la literatura, pero no se obtuvo su URL completa por vía verificable en esta sesión; su contenido — transferencia de aprendizaje para personalizar en el dispositivo — queda cubierto por la documentación actual de LiteRT (fuente 3). No se cita por separado.
- **La afirmación del equipo de TensorFlow de que TFLite no soporta entrenamiento general ni federado.** Se vio citada en un sitio secundario, no en una fuente primaria; no se usa como dato.
- **Un trabajo sobre un framework federado en Kotlin Multiplatform para clasificación de ECG.** Se vio resumido en un sitio secundario con cifras de exactitud y privacidad; no se verificó su publicación primaria, así que no se cita ni se usan sus números. La integración de privacidad diferencial y agregación segura se sostiene con FEDKIT (fuente 7).
