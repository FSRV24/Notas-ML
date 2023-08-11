# Proyecto end-to-end

Emplearemos el conjunto de datos [California Housing Prices](https://www.kaggle.com/datasets/camnugent/california-housing-prices) como base da datos de ejemplo que permitirá la ejemplificación de las herramientas utilizadas.

## Visualizaciones con Plotly

Plotly funciona instanciando un objeto `fig = go.Figure()` al cuál podemos agregar trazos y actualizar ciertas configuraciones a través de sus métodos.

Generalmente, utilizaremos la siguiente configuración:


````{admonition} Code
:class: info, dropdown
```python
# datos simulados
x = np.arange(-5, 5, 0.25)
y = x ** 2 + np.random.normal(0, 1, 40)
```

```python
# gráfica
fig = go.Figure()

fig.add_trace(go.Scatter(
    x=x,
    y=y,
    mode='markers',
    marker_size=7,
    marker_color=palette[0]
))

fig.update_layout(
    title='Title',
    xaxis_title='x',
    yaxis_title='y',
    width=700,
    height=500,
    template='ggplot2',
    margin=dict(l=10, r=10, b=10, t=50)
)

fig.show()
```
````

Resultado.

```{figure} /images/cuadratic.svg
---
height: 400 px
name: cuadratica
---
Función cuadrática con ruido blanco $y=x^2 + \varepsilon$.
```

Podemos agregar otros trazos sobre la misma figura.

````{admonition} Code
:class: info, dropdown
```python
fig.add_traces(go.Scatter(
    x=x,
    y=x ** 2,
    mode='lines',
    line_width=3,
    marker_color=palette[2]   
))

fig.show()
```
````

Resultado.

```{figure} /images/cuadratic_2.svg
---
height: 400 px
name: cuadratica-tendencia
---
Agregando línea de tendencia.
```

```{tip}
Podemos **guardar gráficos** en distintos formatos mediante el comando `fig.write_figure('name.format')`. En el caso de guardar directamente las gráficas interactivas, se tiene la opción `fig.write_html('name.html')`. 
```

## Primeros pasos

Al comenzar un proyecto es útil utilizar visualizaciones como apoyo para conocer la naturaleza del conjunto de datos. En el caso de tener datos geográficos, podemos hacer **mapas interactivos** controlando el color y tamaño de cada instancia en función, por ejemplo, de la variable objetivo (`median_house_value`) u otra variable de interés.

````{admonition} Code
:class: info, dropdown
```python
fig = go.Figure()

fig.add_trace(go.Scattergeo(
    lon=df_sample['longitude'],
    lat=df_sample['latitude'],
    marker_colorbar_title='',
    marker_colorscale='RdBu_r',
    marker_size=df_sample['median_house_value']/30_000,
    marker_color=df_sample['median_house_value'],
    text=df_sample['ocean_proximity'] + '<br>' + 'Median house value: ' + df_sample['median_house_value'].map(str)
))

fig.update_layout(
    title='Valor medio de las casas por vecindario',
    geo_scope='usa',
    height=600,
    width=900,
    template='ggplot2',
    margin=dict(l=10, r=10, b=10, t=50)
)

fig.update_geos(fitbounds='locations')

fig.show()
```
````

Resultado.

```{figure} /images/mapa.svg
---
height: 500 px
name: mapa
---
Mapa con colores y tamaño en función de la variable objetivo.
```

Las gráficas **gráficas de coordenadas paralelas** también resultan útiles para determinar tendencias entre variables. De nuevo, empleando la variable objetivo, se tiene lo siguiente.

````{admonition} Code
:class: info, dropdown
```python
fig = px.parallel_coordinates(
    df_sample,
    color='median_house_value',
    dimensions=df_sample.columns[:-2],
    color_continuous_scale=px.colors.diverging.Tealrose
)

fig.update_layout(
    title='',
    xaxis_title='',
    yaxis_title='',
    height=600,
    template='ggplot2',
    margin=dict(l=40, r=10, b=10, t=50)
)

fig.show()
```
````

Resultado.

```{figure} /images/paralelas.svg
---
height: 400 px
name: paralelas
---
Comparación de atributos tomando como referencia la variable objetivo.
```

Claramente se observan separaciones en puntos específicos de la *latitud* y *longitud*, lo cuál es confirmado a través del mapa anterior; los *ingresos medios* (`median_income`) parecen tener una buena correlación. El resto de variables no muestran buenas separaciones, aunque quizá exista otro tipo de relaciones más complejas entre variables.

También es importante determinar la forma de las **distribuciones**, es decir, determinar el nivel de *kurtosis* y *skewness* (sesgo), por lo que es ideal hacer las gráficas de cada una de las variables.

````{admonition} Code
:class: info, dropdown

Para determinar el tamaño de los bins en cada histograma conviene tomar un tamaño estándar primero, luego, con base en las observaciones, mover a mano cada uno de los bins de forma manual dependiendo de cada una de las escalas.

```python
bin_size = [2.5, 750, 200, 500, 150, 0.5]

for idx, col in enumerate(df_numeric):
    hist_data = [df_sample.dropna()[col]]
    group_labels = [col]

    fig = ff.create_distplot(
        hist_data,
        group_labels,
        show_rug=False,
        colors=[palette[idx]],
        bin_size=bin_size[idx]
        )

    fig.update_layout(
        title=f'Distribución de <b>{col}',
        xaxis_title='Item',
        yaxis_title='Frecuencia relativa',
        height=500,
        width=700,
        showlegend=False,
        template='plotly_white',
        margin=dict(l=10, r=10, b=10, t=50)
    )

    fig.show(renderer='svg')
```
````

Resultado.

```{figure} /images/hist.svg
---
height: 400 px
name: hist
---
Histograma con *función de densidad ajustada* (KDE).
```

Es importante tener en cuenta que cuando sesgadas o con cierto nivel de kurtosis, los algoritmos de machine learning no tendrán el mejor comportamiento. Por ello, estudiaremos algunas estrategias para hacer correcciones y estas tomen una forma más *"normal"*.