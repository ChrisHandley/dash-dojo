---
title: Python Dash Fundamentals
teaching: 10
exercises: 10
questions:
  - "What is Dash?"
  - "How can I create a simple Dash app?"
  - "How do I prepare it for extension?"
objectives:
  - "Build your first Dash app"
keypoints:
  - "Dash is a framework running in Python built on Flask."
  - "It exploits the power of Plotly for visually appealing and interactive plots."
---

## Dash - What is it?

Dash is an open source framework for data visualization interfaces and has been developed since 2017.

It has implementations for R and Julia, and is popular with data scientists and engineering as it off loads the need to learn
how to build web apps from the ground up.

Dash is build on three core technologies;

1. Python
2. React.js
3. Plotly.js

Dash does the heavy lifting for us, so we can focus on the data and how we want to analyze it.

Dash is used in enterprise and science.

- A dashboard to analyze trading positions in real-time
- A visualization of millions of Uber rides
- An interactive financial report

## Let's Build!

First we need to set up a directory to work in and our environment using virtualenv.

```
mkdir dash-dojo
virtualenv --python=python3 .env
source .env/bin/activate
```

{: .language-bash}

With the environment ready we can install the libraries that we need.

```
pip install dash numpy plotly pandas
```

{: .language-bash}

We are also going to prepare our app so it will be ready for deploymnet on AWS Elastic Beanstalk

In our directory let's make a source directory and within that create two files. The first is `__init__.py` which informs the environment that this is a
directory with python files to source as modules.

Now unlike previous past Dojos where we make our main python file `app.py`, for use with AEB we name it `application.py`.

In application.py we can make our very simply Dash app.

```
from dash import Dash, html, dcc
import pandas
import plotly.express as px

app = Dash(__name__)

application = app.server

app.title = 'Dash Dojo'

app.layout = html.Div(
    children=[
        html.H1(children="Avocado Analytics",)
    ]
)


if __name__ == '__main__':
    application.run(debug=True, port=8080)
```

{: .language-python}

Let's break down what is going on here.

- `Dash` initialises an instance of the application.
- `dcc` aka dash core components, enables interactive componentsfor our graphs, dropdowns sliders.
- `html` aka dash html components, allows access to the html tags.
- `pandas` is for the manipulation of data.
- `plotly.express` is our quick presets for plots

Before we make a plot, we are just going to in effect generate a basic webpage.

The title will appear in the tab title of our browser.

`app.layout` is where all the business happens with defining the layout of the webapp.

Notice when we run the program, we use `application.run` rather than app.run as we would normally do, again a AEB nuance.

{% include links.md %}
