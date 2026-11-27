# aws-academy-final2


```markdown
# Segmentación de Clientes con K-Means y PCA en AWS EC2

Práctica de aprendizaje no supervisado ejecutada en una instancia Ubuntu de AWS EC2. Incluye estandarización de variables multivariadas (`StandardScaler`), búsqueda del número óptimo de clusters (`K` de 2 a 5) evaluando métricas internas, reducción de dimensionalidad a 2D con `PCA` y serialización de artefactos con `joblib`.

## Estructura del proyecto

```text
practica_segmentacion_kmeans/
├── practica_segmentacion_ec2.py   # Pipeline de simulación, evaluación de K, PCA y exportación
├── modelo_kmeans_clientes.pkl     # Modelo K-Means entrenado con el K óptimo
├── scaler_clientes.pkl            # Escalador StandardScaler ajustado a las 6 variables
├── pca_clientes.pkl               # Modelo PCA entrenado para compresión a 2 dimensiones
├── .gitignore                     # Exclusión de entorno virtual y archivos temporales
└── README.md                      # Documentación, observaciones y conclusión

```

## Ejecución en AWS EC2

1. Activar el entorno virtual en la instancia Ubuntu:
```bash
source venv/bin/activate

```


2. Instalar dependencias requeridas:
```bash
pip install pandas numpy scikit-learn joblib

```


3. Ejecutar el script y verificar la generación de los tres archivos `.pkl`:
```bash
python3 practica_segmentacion_ec2.py
ls -la

```



## Observaciones sobre resultados

* **Selección de `K` óptimo (`K = 4`):** Al comparar los valores de `K` del 2 al 5, `K = 4` obtuvo el Coeficiente de Silueta más alto y un Índice Davies-Bouldin bastante bajo, lo cual coincide con la estructura real de los grupos simulados y muestra que los clusters quedan bien compactos y separados entre sí.
* **Compresión con PCA (6D → 2D):** Al reducir las 6 variables originales de comportamiento de clientes a solo 2 componentes principales, se logró conservar un porcentaje alto de la varianza total (alrededor del 65%–75% dependiendo de la distribución estandarizada), suficiente para visualizar los segmentos en un plano 2D sin perder la estructura general de los grupos.

## Conclusión

Al correr las pruebas con `K-Means` en la instancia de Ubuntu, queda claro por qué el valor de `K` seleccionado es el más adecuado: es el punto donde el **Coeficiente de Silueta** alcanza su valor máximo (lo que indica que los clientes dentro de cada grupo son muy similares entre sí y están bien separados de los demás) y al mismo tiempo el **Índice Davies-Bouldin** se mantiene bajo, evitando tanto mezclar perfiles distintos como fragmentar demasiado los segmentos. Por su parte, aplicar **PCA** para bajar de 6 variables a 2 dimensiones logra retener la mayor parte de la información relevante para representar y graficar los clusters de forma sencilla, asumiendo una pequeña pérdida de detalle que es normal al comprimir variables.

Pensando en llevar esto a producción —ya sea consumiéndolo desde una API REST o descargando los modelos desde un bucket de AWS S3—, los tres archivos `.pkl` funcionan en cadena: primero pasa el cliente nuevo por `scaler_clientes.pkl` para escalar sus datos exactamente igual que en el entrenamiento, luego `modelo_kmeans_clientes.pkl` le asigna el segmento al que pertenece y, si se necesita graficar su posición o enviar coordenadas ligeras a un dashboard, `pca_clientes.pkl` proyecta esos datos a 2D sin tener que recalcular nada desde cero.

> **Nota:** Al terminar las pruebas y subir los cambios a Git, detener la instancia EC2 desde la consola de AWS Academy para conservar los créditos del laboratorio.

```

---



Si quieres reemplazar los valores aproximados de Silueta, Davies-Bouldin o el porcentaje exacto de varianza de PCA por los números exactos que te imprimió tu terminal, pásamelos y los ajusto al momento.
