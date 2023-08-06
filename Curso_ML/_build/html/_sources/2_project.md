# Proyecto end-to-end

Emplearemos el conjunto de datos [California Housing Prices](https://www.kaggle.com/datasets/camnugent/california-housing-prices) como base da datos de ejemplo que permitirá la ejemplificación de las herramientas utilizadas.

## Visualizaciones con Plotly

Plotly funciona instanciando un objeto al que usualmente llamaremos `fig = go.Figure()` al cuál podemos agregar trazos y actualizar ciertas configuraciones a través de sus métodos.

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
name: directive-fig
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
name: directive-fig
---
Agrando línea de tendencia.
```