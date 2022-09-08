---
title: Dash Layout and Plot
teaching: 10
exercises: 10
questions:
  - "I layout my Dash app?"
  - "How do I add a plot?"
objectives:
  - "Layout basics"
  - "A simple plot"
keypoints:
  - "We can make things a lot easier when we start using Dash Bootstrap."
---

So far using Dash we basically have the same result as Flask. But now we are going to add in a plot.

## Avacado analytics

Pandas comes with some test data to use.

```
from dash import Dash, html, dcc
import pandas as pd
import plotly.express as px

df = pd.DataFrame({
    "Fruit": ["Apples", "Oranges", "Bananas", "Apples", "Oranges", "Bananas"],
    "Amount": [4, 1, 2, 2, 4, 5],
    "City": ["SF", "SF", "SF", "Montreal", "Montreal", "Montreal"]
})

fig = px.bar(df, x="Fruit", y="Amount", color="City", barmode="group")

app = Dash(__name__)

application = app.server
app.config.suppress_callback_exceptions = True

app.title = 'Dash Dojo'

app.layout = html.Div(
    children=[
        html.H1(children="Fruit Analytics",)
    ]
)


if __name__ == '__main__':
    application.run(debug=True, port=8080)
```

{: .language-python}

Running through our steps with Pandas, we create a `Dataframe` that has 3 columns.

We can print this to the command line and we would get the following.

```
     Fruit  Amount      City
0   Apples       4        SF
1  Oranges       1        SF
2  Bananas       2        SF
3   Apples       2  Montreal
4  Oranges       4  Montreal
5  Bananas       5  Montreal
```

{: .language-bash}

We can then create the figure using plotly express, where we use the stock barchart method, with x axis being Fruit type, y axis being Amount, and bars coloured by city.

With the `fig` object generated we can put it into our layout.

```
app.layout = html.Div(children=[
    html.H1(children='Fruit Analytics'),

    html.Div(children='''
        Dash: A web application framework for your data.
    '''),

    dcc.Graph(
        id='example-graph',
        figure=fig
    )
])
```

{: .language-dash}

A lot of the heavy lifting is removed as we make use of the Dash Core Components, and the Graph layout object, simply passing to it the figure object.

{% include links.md %}
