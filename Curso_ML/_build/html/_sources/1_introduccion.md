# Conceptos básicos

He aquí algunas definiciones de **Machine Learning** por varios autores.


> *Machine learning is the science (and art) of programming computers so they can learn from data.* 
> - Aurelien Geron

> *Machine learning is the field of study that gives computers the ability to learn without being explicitly programmed.*
> - Arthur Samuel

> *A computer program is said to learn from experience $E$ with respect to some task $T$ and some performance measure $P$, if its performance on $T$, as measured by $P$, improves with experience $E$.*
> - Tom Mitchell

Las virtudes de estos sistemas, y su diferencia respecto al paradigma de programación tradicional, radica en el *aprendizaje de reglas complejas* que no tengan que ser programadas de forma manual. Usualmente queremos sistemas que puedan cambiar con facilidad y de forma automatizada; hacer las cosas manualmente da lugar a sistemas difíciles de mantener.

## Aplicaciones

Entre las aplicaciones más comunes de esta clase de sistemas encontramos:

- *Análisis de imágenes de productos en líneas de producción:*
    
    Redes Neuronales Convolucionales (CNNs).
- *Detección de tumores:*
    
    Se emplean técnicas de segmentación de imágenes (CNNs).
- *Clasificación de artículos nuevos:*
    
    Técnicas del Procesamiento del Lenguaje Natural (NLP) con Redes Neuronales Recurrentes.
- *Detección de comentarios ofensivos.*
    
    Mismas técnicas que en el caso anterior.
- *Predicción de retornos esperados para una compañía:*
    
    Se emplean modelos de regresión.

## Tipos de sistemas

````{margin}
```{admonition} Target *vs* Label
:class: info
En el contexto del *aprendizaje supervisado*, se suele emplear **target** en el caso de *regresión*, y **label** para los problemas de clasificación.
```
````

Podemos clasificar los sistemas entres grandes categorías basándonos en los siguientes criterios:

1. Cómo son *supervisados* durante el entrenamiento:
    - **Supervisado:** se da la etiqueta del resultado deseado (numéricas para tareas de regresión, ordinales o no ordinales en el caso de regresión).
    - **No supervisado:** los sistemas aprenden sin etiquetas; se emplean para tareas de *clustering*, *reducción de dimensiones*, *visualización*, *detección de anomalías*, *detección de novedad*, *reglas de asociación*.
    - **Semi-supervisado:** sólo se tiene etiquetada una parte del conjunto de datos; por ejemplo, cuando Google Photos nos pregunta quienes son las personas que aparecen en nuestras fotos, los algoritmos son capaces de predecir la presencia de esas mismas personas en el resto de la galería, es decir, hay una propagación de etiquetas.
    - **Auto-supervisado:** genera un conjunto de datos completamente etiquetado a partir de uno que no lo está; un ejemplo de esto son los modelos de reconstrucción de imágenes.
    - **Aprendizaje por refuerzo:** un *agente* explora un *ambiente*, experimenta y obtiene una *recompensa en función de las acciones que siga*; a partir de lo anterior, el agente es capaz de crear una *política* que le permite tomar las mejores acciones dependiendo de cada situación.

```{figure} ./images/mls3_0112.png
---
height: 200 px
name: gatito
---
Ejemplo de aprendizaje auto-supervisado para completar imágenes.
```

````{margin}
```{tip}
Cuando se tienen conjuntos de datos muy grandes y deben ser entrenados, muy probablemente los datos no quepan en memoria, emplear *online learning* puede ayudar a manejar la situación. Simplemente se divide el conjunto de datos en partes más pequeñas (mini-batches) para entrenar el modelo parcialmente. Esta ténica se conoce como **out-of-core learning**.
```
````

2. Cómo son *entrenados* los modelos:
    - **Batch learning:** tiene un *aprendizaje no incremental* (usualmente llamado *offline learning*) ya que requiere el conjunto de datos en su totalidad para ser entrenado *todo de un tirón*, en una *época*; una vez entrenado, no se puede reentrenar si llegara una *instancia* nueva.
    - **Online learning:** son sistemas que aprenden de *forma incremental* o en grupos de datos pequeños llamados *mini-batches*; estos modelos pueden ser reentrenados en caso de que llegue una nueva instancia. 

```{important}
Los modelos entrenados mediante *batch learning* no son viables en computadoras o servidores con escasos recursos.
```

3. Cómo se hacen las generalizaciones:
    - **Basadas en instancia:** emplea medidas de similitud entre instancias; la similitud se puede medir, por ejemplo, utilizando la *similitud conseno*

    ```{math}
    :label: sim_cos
    sim(i, j) = \cos(\hat{i}, \hat{j}) = \frac{\hat{i} \cdot \hat{j}}{||\hat{i}||_2 * ||\hat{j}||_2}
    ```

    Esto significa que cuando se tienen dos instancias de nuestro conjunto de datos, y el ángulo entre ellas es pequeño, entonces tienen gran similitud; si el ángulo formado es mayor, se tiene una similitud menor. En esencia, los modelos *basados en instancias* hacen comparaciones con el resto de registros en el conjunto de datos

    - **Basadas en modelos:** se emplean modelos matemáticos que se ajustan durante el entrenamiento; por el ejemplo, el ajuste de los parámetros $\theta _i$, $i \in \{ 1, 2 \}$ en el modelo de regresión lineal simple

    ```{math}
    :label: lin_reg_simple
    y = \theta _0 + \theta _1 x
    ```

    Las elecciones tomadas por el algortimo serán con base en el modelo entrenado, no tomando como referencia el resto de instancias.

## Evaluación de modelos

Comúnmente, se suele llevar a cabo una partición del conjunto de datos original, cada conjunto llamado: **train**, **test** y **validation**. Bajo el contexto de la **validación cruzada**, entendemos estos conjuntos de la siguiente forma:

1. Tenemos el conjunto de datos original con $n$ instancias.
2. Hacemos la primera partición entre el conjunto **train** y **validation**, donde este último sólo se utiliza en la evaluación final de un modelo.
3. Establecemos un número $k$ de iteraciones o *folds*, número de veces que pondremos a prueba el modelo candidato.
4. Dividimos el modelo en $k$ particiones, cada una de ellas será el conjunto **test** de cada iteración.

Al final se obtienen $k$ evaluaciones del modelo propuesto entrenado sobre todo el conjunto *train*, así que se evitan sesgos.

```{figure} images/cv.png
:height: 200px
:name: cross-val

Proceso en validación cruzada. Obtenido de [ResearchGate](https://www.researchgate.net/profile/Mingchao-Li/publication/331209203/figure/fig2/AS:728070977748994@1550597056956/K-fold-cross-validation-method.png).
```

```{important}
No existe una tasa adecuada entre el conjunto de entrenamiento y prueba, siendo común utilizar una tasa del 80% para entrenamiento y 20% para prueba. El procentaje del conjunto de prueba suele se mayor en *datasets* más pequeños, o más pequeño en *datasets* más grandes.
```