# Detección de fraude en tarjetas de crédito

[English version](README.md)

Modelo de detección de fraude sobre el dataset de tarjetas de crédito de Kaggle, hecho con mentalidad de operación de fraude: el objetivo no es solo un buen score, sino elegir un umbral de alertas con el que un equipo de revisión pueda trabajar de verdad y mostrar cuánto dinero detiene.

Trabajo en prevención de fraude, así que la mayor parte del esfuerzo se fue a lo que importa en la práctica: manejar una tasa de fraude de 0.17%, no filtrar información del test y traducir el resultado a dinero y volumen de alertas.

## Qué hay en el notebook

El análisis está en [`deteccion_fraude.ipynb`](deteccion_fraude.ipynb).

1. Limpieza de datos (el dataset tiene 1,081 filas duplicadas que si no se quitan se cuelan entre train y test).
2. Separación en train / validación / test (60/20/20, estratificado).
3. Comparación de regresión logística, random forest y XGBoost con PR-AUC en validación.
4. Elección del umbral en validación.
5. Una sola evaluación final en test, más el monto de fraude detenido contra el perdido y el número de alertas.

## Resultados

En test, evaluado una sola vez con el umbral elegido en validación.

| Métrica | Valor |
| --- | --- |
| Mejor modelo | Random forest (empatado con XGBoost en validación) |
| Umbral | 0.35 |
| PR-AUC | 0.857 |
| Recall (fraude detectado) | 77% (73 de 95) |
| Precision (alertas que son fraude real) | 95% |
| Falsas alarmas | 4 de 56,651 transacciones legítimas |
| Monto de fraude detenido | $9,638 de $12,973 (74%) |

En la práctica: 77 alertas para el equipo de revisión, 73 de ellas fraude real.

## Cómo correrlo

El dataset es demasiado pesado para GitHub, así que descarga `creditcard.csv` de [Kaggle](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud) y ponlo junto al notebook.

```
pip install -r requirements.txt
jupyter notebook deteccion_fraude.ipynb
```

También corre en Google Colab: sube el notebook y el CSV y ejecuta todas las celdas.

## Limitaciones

- Solo 95 casos de fraude en el test, así que las métricas se mueven varios puntos con unas cuantas transacciones.
- La separación es aleatoria y no por tiempo, lo cual es más optimista que un uso real en producción.
- V1 a V28 son componentes de PCA anonimizados, así que no se pueden crear variables ni diseñar reglas con lógica de negocio. Lo siguiente que quiero hacer es comparar reglas contra modelo en un dataset con campos de transacción en crudo.
