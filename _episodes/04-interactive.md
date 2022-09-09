---
title: Interactive Plots
teaching: 30
exercises: 15
questions:
  - "How can a Plot Respond to an Input"
objectives:
  - "Interactive Elements"
  - "Callbacks"
keypoints:
  - "???"
---

So far, plots and diagrams, generated using `plotly`, have been static. But with Dash we can make these plots interactive.

## Callbacks

To begin, we need to provide a means to give some sort of input for the dashboard. For starters let's use a text input.

```
from dash import Dash, dcc, html, Input, Output

...

app.layout = dbc.Container(
      [
            ...

            html.Div([
                  "Input: ",
                  dcc.Input(id='my-input', value='initial value', type='text')
            ]),
            html.Br(),
            html.Div(id='my-output'),

            dbc.Tabs(
                  ...
            )
      ]
)

```

{: .language-python}

We've created our input field but we now need to use that input to create some form of response on the dashboard.

```
@app.callback(
      Output(component_id='my-output', component-property='children'),
      Input(component_id='my-input', component-property='value')
)
def update_output(input_value):
      return f'Output: {input_value}'

```

{: .language-python}

The `app.callback` decorator is defining the use of the function `update_output` and dynamically passing in and out of it values, or entire elements.

The `dcc.Input` is one such input, a field, with a defined initial value and type. But we can use others.

## Figures and Sliders

```
from dash import Dash, dcc, html, Input, Output
import plotly.express as px

import pandas as pd

df = pd.read_csv('https://raw.githubusercontent.com/plotly/datasets/master/gapminderDataFiveYear.csv')


app = Dash(__name__)

app.layout = html.Div([
    dcc.Graph(id='graph-with-slider'),
    dcc.Slider(
        df['year'].min(),
        df['year'].max(),
        step=None,
        value=df['year'].min(),
        marks={str(year): str(year) for year in df['year'].unique()},
        id='year-slider'
    )
])


@app.callback(
    Output('graph-with-slider', 'figure'),
    Input('year-slider', 'value'))
def update_figure(selected_year):
    filtered_df = df[df.year == selected_year]

    fig = px.scatter(filtered_df, x="gdpPercap", y="lifeExp",
                     size="pop", color="continent", hover_name="country",
                     log_x=True, size_max=55)

    fig.update_layout(transition_duration=500)

    return fig


if __name__ == '__main__':
    app.run_server(debug=True)
```

{: .language-python}

If we look at our new layout (which uses no bootstrap components), we return to `dcc.Graph` the figure object created by the callback function.

Our input to that callback is `dcc.Slider`, with passes the value of the slider.

## Multiple Inputs

Building on our example above we can generate a list of continents for use in the dropdown, and use the dropdown as a means to filter the data of the dataframe that is used to form the plot

```
from dash import Dash, dcc, html, Input, Output
import plotly.express as px

import pandas as pd
import numpy as np

df = pd.read_csv(
    'https://raw.githubusercontent.com/plotly/datasets/master/gapminderDataFiveYear.csv')


continent = df.continent.unique()
continent = np.insert(continent, 0, 'All')


app = Dash(__name__)

app.layout = html.Div([
    dcc.Graph(id='graph-with-slider'),
    dcc.Slider(
        df['year'].min(),
        df['year'].max(),
        step=None,
        value=df['year'].min(),
        marks={str(year): str(year) for year in df['year'].unique()},
        id='year-slider'
    ),
    dcc.Dropdown(continent, id='continent_choice', value='All')
])


@app.callback(
    Output('graph-with-slider', 'figure'),
    Input('year-slider', 'value'),
    Input('continent_choice', 'value'))
def update_figure(selected_year, continent_choice):
    filtered_df = df[df.year == selected_year]
    if continent_choice == 'All':
        filtered_df2 = filtered_df
    else:
        filtered_df2 = filtered_df[filtered_df.continent == continent_choice]

    fig = px.scatter(filtered_df2, x="gdpPercap", y="lifeExp",
                     size="pop", color="continent", hover_name="country",
                     log_x=True, size_max=55)

    fig.update_layout(transition_duration=500)

    return fig


if __name__ == '__main__':
    app.run_server(debug=True)
```

{: .language-python}

{% include links.md %}
