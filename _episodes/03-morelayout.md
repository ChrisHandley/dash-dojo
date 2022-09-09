---
title: More Dash Layout and Plot
teaching: 10
exercises: 10
questions:
  - "Reuseable layout?"
  - "How can I add plots side by side?"
  - "Can I have tabs?"
objectives:
  - "More layout"
  - "Tabs"
keypoints:
  - "Dash Bootstrap to the Rescue"
---

Let's be efficient and and use functions to generate portions of our layout.

```
def generate_table(dataframe, max_rows=10):
    return html.Table([
        html.Thead(
            html.Tr([html.Th(col) for col in dataframe.columns])
        ),
        html.Tbody([
            html.Tr([
                html.Td(dataframe.iloc[i][col]) for col in dataframe.columns
            ]) for i in range(min(len(dataframe), max_rows))
        ])
    ])
```

{: .lanugage-python}

What we can see here is that the function `generate_table` to generate our layout markup, in this case it is
generating some html, in the form of a table, but this could also be Dash Core Components.

```
import dash
import dash_core_components as dcc
import dash_html_components as html
import pandas as pd
import plotly.express as px

df = pd.DataFrame({
    "Fruit": ["Apples", "Oranges", "Bananas", "Apples", "Oranges", "Bananas"],
    "Amount": [4, 1, 2, 2, 4, 5],
    "City": ["SF", "SF", "SF", "Montreal", "Montreal", "Montreal"]
})

fig = px.bar(df, x="Fruit", y="Amount", color="City", barmode="group")

df = pd.read_csv('https://gist.githubusercontent.com/chriddyp/c78bf172206ce24f77d6363a2d754b59/raw/c353e8ef842413cae56ae3920b8fd78468aa4cb2/usa-agricultural-exports-2011.csv')


def generate_table(dataframe, max_rows=10):
    return html.Table([
        html.Thead(
            html.Tr([html.Th(col) for col in dataframe.columns])
        ),
        html.Tbody([
            html.Tr([
                html.Td(dataframe.iloc[i][col]) for col in dataframe.columns
            ]) for i in range(min(len(dataframe), max_rows))
        ])
    ])


app = dash.Dash(__name__)

application = app.server
app.config.suppress_callback_exceptions = True

app.title = 'Dash Dojo'

app.layout = html.Div(children=[
    html.H1(children='Fruit Analytics'),

    html.Div(children='''
        Dash: A web application framework for your data.
    '''),

    dcc.Graph(
        id='example-graph',
        figure=fig
    ),
    html.H4(children='US Agriculture Exports (2011)'),
    generate_table(df)
])


if __name__ == '__main__':
    application.run(debug=True, port=8080)
```

{: .language-python}

## Rows and Columns

Thus far our plots and tables have existed one on top of the other.

Let's consider two ways to lay things out. The first uses standard Dash Core Components and HTML.

```
app.layout = html.Div([
    html.Div(children=[
        html.Label('Dropdown'),
        dcc.Dropdown(['New York City', 'Montréal', 'San Francisco'], 'Montréal'),

        html.Br(),
        html.Label('Multi-Select Dropdown'),
        dcc.Dropdown(['New York City', 'Montréal', 'San Francisco'],
                     ['Montréal', 'San Francisco'],
                     multi=True),

        html.Br(),
        html.Label('Radio Items'),
        dcc.RadioItems(['New York City', 'Montréal', 'San Francisco'], 'Montréal'),
    ], style={'padding': 10, 'flex': 1}),

    html.Div(children=[
        html.Label('Checkboxes'),
        dcc.Checklist(['New York City', 'Montréal', 'San Francisco'],
                      ['Montréal', 'San Francisco']
        ),

        html.Br(),
        html.Label('Text Input'),
        dcc.Input(value='MTL', type='text'),

        html.Br(),
        html.Label('Slider'),
        dcc.Slider(
            min=0,
            max=9,
            marks={i: f'Label {i}' if i == 1 else str(i) for i in range(1, 6)},
            value=5,
        ),
    ], style={'padding': 10, 'flex': 1})
], style={'display': 'flex', 'flex-direction': 'row'})
```

{: .language-python}

Using `html.Div` we have our two columns, and within each we have defined some `Dropdowns` using `dcc`, radio items, and input text box with a default entry, and a slider.

These components will be used later in callbacks and our interactive plots.

Style in our html component is exactly as we would define it using CSS.

## Dash Bootstrap Components

While this is one way of defining out layout the other alternative is `Dash Boostrap Components`.

Let's return to our plot and graph from previous examples but use `dbc` instead to control layout.

```
from dash import Dash, html, dcc
import pandas as pd
import plotly.express as px
import dash_bootstrap_components as dbc

df = pd.DataFrame({
    "Fruit": ["Apples", "Oranges", "Bananas", "Apples", "Oranges", "Bananas"],
    "Amount": [4, 1, 2, 2, 4, 5],
    "City": ["SF", "SF", "SF", "Montreal", "Montreal", "Montreal"]
})

fig = px.bar(df, x="Fruit", y="Amount", color="City", barmode="group")

df = pd.read_csv('https://gist.githubusercontent.com/chriddyp/c78bf172206ce24f77d6363a2d754b59/raw/c353e8ef842413cae56ae3920b8fd78468aa4cb2/usa-agricultural-exports-2011.csv')


def generate_table(dataframe, max_rows=10):
    return html.Table([
        html.Thead(
            html.Tr([html.Th(col) for col in dataframe.columns])
        ),
        html.Tbody([
            html.Tr([
                html.Td(dataframe.iloc[i][col]) for col in dataframe.columns
            ]) for i in range(min(len(dataframe), max_rows))
        ])
    ])


app = Dash(__name__, external_stylesheets=[dbc.themes.BOOTSTRAP])

application = app.server
app.config.suppress_callback_exceptions = True

app.title = 'Dash Dojo'

app.layout = dbc.Container(
    [
        html.H1(children='Fruit Analytics'),

        html.Div(children='''
        Dash: A web application framework for your data.
    '''),

        dbc.Row(
            [
                dbc.Col(dcc.Graph(
                    id='example-graph',
                    figure=fig
                ), md=4),
                dbc.Col([
                    html.H4(children='US Agriculture Exports (2011)'),
                    generate_table(df)
                ], md=8)

            ],
        )

    ], fluid=True,
)


if __name__ == '__main__':
    application.run(debug=True, port=8080)

```

{: .language-python}

Now our app layout begins with a `dbc.Container` which is subject to the style sheet (and we can use many pre-exisitng ones!).

Within the container, the way we define the layout is similar to pure html, but how we can define `dbc.Row` and `dbc.Col`, and use these to structure our layout.

There is also the assumption that there are 12 columns that components can span, and we can either allow autosizing of these, or define the number used by each column component, or even define their sizes with respect to specific screen sizes.

Here we used `md` to denote a medium sized screen. `width` would allow us to use the whole screen no matter the device size. We can also note that when we resize the screen the plot and table reposition dynamically.

## Markdown

Just like these lessons, we can write portions of our Dash app with Markdown

```
...

markdown_text = '''
### Dash Markdown
Here is some test using [CommonMark](http://commonmark.org/)
'''

...

app.layout = dbc.Container(
    [
        ...
        dcc.Markdown(children=markdown_text)
        ...
    ]
)

```

{: .language-python}

{% include links.md %}
