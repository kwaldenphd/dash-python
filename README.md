# Getting Started With `plotly` and `dash` in Python

<a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license"><img style="border-width: 0;" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" alt="Creative Commons License" /></a>
This tutorial is licensed under a <a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license">Creative Commons Attribution-NonCommercial 4.0 International License</a>.

## Lab Goals

## Acknowledgements

The author consulted the following resources when writing  this tutorial:
- [`plotly` documentation and tutorials](https://plotly.com/python/)
- [`dash` documentation and tutorials](https://dash.plotly.com/)

# Table of Contents

- [Overview](#overview)
  * [Basic `dash` Syntax](#basic-dash-syntax)
  * [Customizing `dash` Layout with `dash_html_components`](#customizing-dash-layout-with-dash-html-components)
  * [Customizing `dash` Layout with `dash_core_components`](#customizing-dash-layout-with-dash-core-components)
  * [Setting Up Callbacks](#setting-up-callbacks)
  * [Additional Resources](#additional-resources)
  * [Publishing a `dash` App](#publishing-a-dash-app)
- [Practice Problems](#practice-problems)
- [Lab Notebook Questions](#lab-notebook-questions)

# Overview

We may want to create an interactive web application featuring a `plotly` figure.

We see this kind of interactivity in dashboard-style interfaces:
SUCH AS

We can use the `dash` web application framework in combination with `plotly` to "publish" a figure as an interactive web application.

`dash` was developed by the Plotly team and released in 2017.

In addition to a limited free open-source version, Plotly also offers paid enterprise versions of `dash`.

What exactly is `dash`?

"Dash is a productive Python framework for building web analytic applications. Written on top of Flask, Plotly.js, and React.js, Dash is ideal for building data visualization apps with highly custom user interfaces in pure Python. It's particularly suited for anyone who works with data in Python. Through a couple of simple patterns, Dash abstracts away all of the technologies and protocols that are required to build an interactive web-based application. Dash is simple enough that you can bind a user interface around your Python code in an afternoon. Dash apps are rendered in the web browser. You can deploy your apps to servers and then share them through URLs. Since Dash apps are viewed in the web browser, Dash is inherently cross-platform and mobile ready" (`plotly`, [Introduction to Dash](https://dash.plotly.com/introduction)).

For those familiar with RStudio's [Shiny package](v) (designed to build interactive web applications from within the R/RStudio programming environment), `dash` was originally pitched as the equivalent package for Python.

Though originally built exclusively for `Python`, `dash` has since released versions for R/RStudio and Julia.

For more background on `dash`:
- plotly, ["Introducing Dash"](https://medium.com/plotly/introducing-dash-5ecf7191b503) *Medium* (21 June 2017)
- Plotly, ["PLOTCON 2016: Chris Parmer, Dash: Shiny for Python"](https://youtu.be/5BAthiN0htc) *YouTube* (1 December 2016)
- `plotly`, [Dash Enterprise App Gallery](https://dash-gallery.plotly.host/Portal/)
- [Plotly, Dash GitHub repository](https://github.com/plotly/dash)

To install `dash`:
- using `pip`: `pip install dash`
- in Jupyter notebook using `pip`: `pip install jupyter-dash`
- in Jupyter notebook using `conda`: `conda install -c conda-forge -c plotly jupyter-dash`

The remainder of this tutorial focuses on `dash` outside the Jupyter notebook environment.

For more on using `dash` in a notebook environment: plotly, [Jupyter-Dash Github Repository](https://github.com/plotly/jupyter-dash)

## Basic `dash` Syntax

`dash` apps include two main components.

The app ***layout*** describes what the application looks like.

The app ***interactivity*** describes the application's interactivity.

`dash` uses pre-built Python classes for all visual components of the application (which can be customized if needed using JavaScript and React.js).

The basic syntax for creating an application using `dash`:

```Python
# at this point the figure has already been created and updated/configured/customized as needed

# import dash components
import dash
import dash_core_components as dcc
import dash_html_components as html

# create app
app = dash.Dash()

# set app layout
app.layout = html.Div([
    dcc.Graph(figure=fig)
])

# run app
app.run_server(debug=True, use_reloader=False)
```

Let's create an app using our tile-based choropleth map.
```Python
# import urllib
from urllib.request import urlopen

# import json
import json

# load geojson data
with urlopen('https://raw.githubusercontent.com/plotly/datasets/master/geojson-counties-fips.json') as response:
    counties = json.load(response)

# import pandas
import pandas as pd

# load attribute data as dataframe
df = pd.read_csv("https://raw.githubusercontent.com/plotly/datasets/master/fips-unemp-16.csv",
                   dtype={"fips": str})

# import plotly
import plotly.express as px

# create figure
fig = px.choropleth_mapbox(df, geojson=counties, locations='fips', color='unemp',
                           color_continuous_scale="Viridis",
                           range_color=(0, 12),
                           mapbox_style="carto-positron",
                           zoom=3, center = {"lat": 37.0902, "lon": -95.7129},
                           opacity=0.5,
                           labels={'unemp':'unemployment rate'}
                          )

# update figure layout
fig.update_layout(margin={"r":0,"t":0,"l":0,"b":0})

# show figure
fig.show()

# import dash components
import dash
import dash_core_components as dcc
import dash_html_components as html

# create app
app = dash.Dash()

# set app layout
app.layout = html.Div([
    dcc.Graph(figure=fig)
])

# run app
app.run_server(debug=True, use_reloader=False)
```

At the bottom of each documentation page for a `plotly` figure type will be a "What About Dash?" section that provides a brief overview of how to publish that figure in a `dash` application.

For example, [link to the "What About Dash?"](https://plotly.com/python/scattermapbox/#what-about-dash) section of the "Scatter Plots on Mapbox in Python" page.

## Customizing `dash` Layout with `dash_html_components`

This example makes very few modifications to `app.layout`.

We can start to customize a `dash` app by using an external style sheet.

Similar to how we use CSS in combination with HTML, external style sheets can also be loaded and applied in `dash`.
```Python
# load style sheet; this example uses URL; could also use local .css file
external_stylesheets = ['https://codepen.io/chriddyp/pen/bWLwgP.css']

# call style sheet when starting app
app = dash.Dash(APP_NAME, external_stylesheets=external_stylesheets)
```

We can also start to modify components of the application by declaring HTML tags.

Remember in HTML, tags like `<h1>`, `<p>`, etc. are used for styling and formatting.

The `dash_html_components` library has a component for every HTML tag.

We can create declarations for those elements to govern how they are formatted or styled in the `dash` application.

This is similar to how we create declarations in an external style sheet.

Let's modify our app layout to include a `H1` title, a `div` subtitle, and a `Graph` object.

```Python
# omitted code creates and customizes figure

# import dash components
import dash
import dash_core_components as dcc
import dash_html_components as html

# load style sheet; this example uses URL; could also use local .css file
external_stylesheets = ['https://codepen.io/chriddyp/pen/bWLwgP.css']

# call style sheet when starting app
app = dash.Dash(Test_App, external_stylesheets=external_stylesheets)

# set app layout
app.layout = html.Div(children=[
    html.H1(children='Hello Dash'),

    html.Div(children='''
        Dash: A web application framework for Python.
    '''),

    dcc.Graph(
        id='example-graph',
        figure=fig
    )
])

# run app
app.run_server(debug=True, use_reloader=False)
```

We see the component declarations nested under `app.layout`.

`dash` generates an HTML element for each of these components.

For example, `dash` takes `html.H1(children='Hello Dash')` and generates `<h1>Hello Dash</h1>`.

`dash` can also take keyword arguments as part of these component declarations.

Let's say we want to override the external style sheet or set formatting for an element not convered in the style sheet.
```Python
# import dash components
import dash
import dash_core_components as dcc
import dash_html_components as html

# load style sheet; this example uses URL; could also use local .css file
external_stylesheets = ['https://codepen.io/chriddyp/pen/bWLwgP.css']

# call style sheet when starting app
app = dash.Dash(Test_App, external_stylesheets=external_stylesheets)

# color declaration 
colors = {'background': '#111111', 'text': '#7FDBFF'}

# omitted code creates figure

# update figure layout using colors dictionary keys
fig.update_layout(plot_bgcolor=colors['background'], paper_bgcolor=colors['background'], font_color=colors['text'])

# set app layout
app.layout = html.Div(style={'backgroundColor': colors['background']}, children=[
    html.H1(
        children='Hello Dash',
        style={
            'textAlign': 'center',
            'color': colors['text']
        }
    ),

    html.Div(children='Dash: A web application framework for Python.', style={
        'textAlign': 'center',
        'color': colors['text']
    }),

    dcc.Graph(
        id='example-graph-2',
        figure=fig
    )
])

# run app
if Test_App == '__main__':
    app.run_server(debug=True)
```

We create the `colors` dictionary with `background` and `text` keys and color values.

We apply those custom colors to the figure using `fig.update_layout`.

We apply those custom colors as keyword arguments for the `app.layout` components.

For more on the `dash-html-components` library: [`plotly`, Dash HTML Components](https://dash.plotly.com/dash-html-components)

For more on the `dash` layout options: [`plotly`, Dash Layout](https://dash.plotly.com/layout)

## Customizing `dash` Layout Using `dash_core_components`

Where `dash` really shines (and has functionality that compares with RStudio Shiny) is in the higher-level core components.

These core components include things like:
- Dropdown input menu
- Slider
- Range slider
- Input box
- Input text area
- Checkboxes
- Radio buttons
- Buttons
- Single date picker
- Date range picker
- File upload
- Multi-tab disply

These core components are generated through a combination of JavaScript, HTML, and CSS in combination with the React.js library.

The syntax for `dash-core-components` is similar to the HTML element declarations in the previous sections.

A few examples are provided below.

### Dropdown

```Python
# import dash core components
import dash_core_components as dcc

# sample code for a dropdown menu
dcc.Dropdown(
    options=[
        {'label': 'New York City', 'value': 'NYC'},
        {'label': 'Montréal', 'value': 'MTL'},
        {'label': 'San Francisco', 'value': 'SF'}
    ],
    value='MTL'
)

# this example takes a list of dictionaries as the label and value pairs for the dropdown; value sets the default dropdown valu

# sample code for multi-select dropdown
dcc.Dropdown(
    options=[
        {'label': 'New York City', 'value': 'NYC'},
        {'label': 'Montréal', 'value': 'MTL'},
        {'label': 'San Francisco', 'value': 'SF'}
    ],
    multi=True,
    value="MTL"
) 

# setting multi to True allows for multi-select

# for more on dropdown components: https://dash.plotly.com/dash-core-components/dropdown
```
### Slider

```Python
# import dash core components
import dash_core_components as dcc

# sample code for a slider
dcc.Slider(
    min=-5,
    max=10,
    step=0.5,
    value=-3
)  

# this example sets the min and max value for the slider, as well as the step increments and default value

# another example that includes slider labels
dcc.Slider(
    min=0,
    max=9,
    marks={i: 'Label {}'.format(i) for i in range(10)},
    value=5,
)

# this example sets marks for each slider value

# for more on slider components: https://dash.plotly.com/dash-core-components/slider
```
### RangeSlider

```Python
# import dash core components
import dash_core_components as dcc

# sample code for a range slider
dcc.RangeSlider(
    count=1,
    min=-5,
    max=10,
    step=0.5,
    value=[-3, 7]
)  

# example specifies min and max values, default positions, and step increment

# another range slider example with labels
dcc.RangeSlider(
    marks={i: 'Label {}'.format(i) for i in range(-5, 7)},
    min=-5,
    max=6,
    value=[-3, 4]
)

# again marks is used to set the mark position and label text

# for more on range sliders: https://dash.plotly.com/dash-core-components/rangeslider
```

### Input

```Python
# import dash core components
import dash_core_components as dcc

# sample code for an input box
dcc.Input(
    placeholder='Enter a value...',
    type='text',
    value=''
)  

# placeholder sets the placeholder text; type sets the input type; value sets the default value

# for more on input: https://dash.plotly.com/dash-core-components/input
```

### TextArea

```Python
# import dash core components
import dash_core_components as dcc

# sample code for a text area box
dcc.Textarea(
    placeholder='Enter a value...',
    value='This is a TextArea component',
    style={'width': '100%'}
)

# placeholder sets the placeholder value, value sets the default value, and style sets the box width

# for more on Textarea: https://dash.plotly.com/dash-core-components/textarea 
```

### Checkboxes

```Python
# import dash core components
import dash_core_components as dcc

# sample code for vertical list of checkboxes (default includes multi-select)
dcc.Checklist(
    options=[
        {'label': 'New York City', 'value': 'NYC'},
        {'label': 'Montréal', 'value': 'MTL'},
        {'label': 'San Francisco', 'value': 'SF'}
    ],
    value=['MTL', 'SF']
)  

# options set the label and value for each check box, and value sets the default values

# another example with a horizontal or inline list of checkboxes
dcc.Checklist(
    options=[
        {'label': 'New York City', 'value': 'NYC'},
        {'label': 'Montréal', 'value': 'MTL'},
        {'label': 'San Francisco', 'value': 'SF'}
    ],
    value=['MTL', 'SF'],
    labelStyle={'display': 'inline-block'}
)

# modifying labelStyle changes the layout

# for more on Checklist: https://dash.plotly.com/dash-core-components/checklist
```

### Other Core Component Types

You're starting to get the picture.

For more examples and documentation:
- [`dcc.RadioItems()`](https://dash.plotly.com/dash-core-components/radioitems)
- [`html.Button()`](https://dash.plotly.com/dash-html-components/button)
- [`dcc.DatePickerSingle()`](https://dash.plotly.com/dash-core-components/datepickersingle)
- [`dcc.DatePickerRange()`](https://dash.plotly.com/dash-core-components/datepickerrange)
- [`dcc.Upload()`](https://dash.plotly.com/dash-core-components/upload)
- [`dcc.Tabs()`](https://dash.plotly.com/dash-core-components/tabs)
- [`dcc.Graph()`](https://dash.plotly.com/dash-core-components/graph)


For more on the `dash_core_components` library: [`plotly`, Dash Core Components](https://dash.plotly.com/dash-core-components)

For more on the `dash_html_components` library: [`plotly`, Dash HTML Components](https://dash.plotly.com/dash-html-components)

## Setting Up Callbacks

At this point, these layout components are interactive in the sense that you can interact with them.

But the components are not response, in the sense that interacting with them does not change the app's behavior.

We need to make the layout components responsive and interactive.

We can do this in `dash` using callback functions.

In `dash` syntax, callback functions are Python functions called by `dash` whenever an input component's property changes.

An example that uses a responsive input box.
```Python
# import dash components
import dash
import dash_core_components as dcc
import dash_html_components as html
from dash.dependencies import Input, Output

# set external style sheet
external_stylesheets = ['https://codepen.io/chriddyp/pen/bWLwgP.css']

# initialize app
app = dash.Dash(__name__, external_stylesheets=external_stylesheets)

# configure app layout
app.layout = html.Div([
    html.H6("Change the value in the text box to see callbacks in action!"),
    html.Div(["Input: ",
              dcc.Input(id='my-input', value='initial value', type='text')]),
    html.Br(),
    html.Div(id='my-output'),

])

# decorator that tells dash to call named function when input value changes so output components reflect that change
@app.callback(
    Output(component_id='my-output', component_property='children'),
    Input(component_id='my-input', component_property='value')
)

# name output function
def update_output_div(input_value):
    return 'Output: {}'.format(input_value)


# initialize app
if __name__ == '__main__':
    app.run_server(debug=True)
```

Let's walk through what happens in this example.

The application inputs and outputs are described using the `@app.callback` decorator, similar to the layout and component declarations covered previously.

The decorator instructs `dash` to call this function whenever the input value changes, so the output components are updated accordingly.

What you name the function arguments is up to you, as long as the names in the function definition match the names in the callback.

The `@app.callback` decorator references components described in `app.layout`.

Thus the component names in `@app.callback` need to reflect the component names used with `app.layout`.

In this example, those component names are `my-input` and `my-output`.

We see those names set using the `id` parameter with `app.layout`, and reflected in the callback.

The `@app.callback` decorator should always come immediately before the `def` function definition.

For more on function decorators in Python: [`python`, PEP 318 -- Decorators for Functions and Methods](https://www.python.org/dev/peps/pep-0318/)

In `dash` syntax, application inputs and outputs are treated as component properties. 

In this example, the `my-input` component is set to take the input as its `value`, and `my-output` is set to reflect the `my-input` value.

Whenever an input property changes (in this example characters in the input box change), the callback function is automatically called.

`dash` provides the function with the new input value and updates the output component property.

Note that we had to import `from dash.dependencies import Input, Output`, which is different than `dash_core_components.Input`.

Taken together, this functionality is called ***reactive programming***.

The keyword arguments used to describe each component become incredibly important when setting up callback functions.

Let's look at another application that uses callbacks.

In this examle, global population data is plotted as a bubble chart.

Points are colored by continent, sized by population, and a slider is used to mark each year of available data.

```Python
# import dash components and plotly
import dash
import dash_core_components as dcc
import dash_html_components as html
from dash.dependencies import Input, Output
import plotly.express as px

# import pandas
import pandas as pd

# load dataframe
df = pd.read_csv('https://raw.githubusercontent.com/plotly/datasets/master/gapminderDataFiveYear.csv')

# load external stylesheet
external_stylesheets = ['https://codepen.io/chriddyp/pen/bWLwgP.css']

# initialize app
app = dash.Dash(__name__, external_stylesheets=external_stylesheets)

# set app layout with graph and slider components
app.layout = html.Div([
    dcc.Graph(id='graph-with-slider'),
    dcc.Slider(
        id='year-slider',
        min=df['year'].min(),
        max=df['year'].max(),
        value=df['year'].min(),
        marks={str(year): str(year) for year in df['year'].unique()},
        step=None
    )
])


# set callback for slider and graph; note the slider is the input and the graph is the output
@app.callback(
    Output('graph-with-slider', 'figure'),
    Input('year-slider', 'value'))

# function definition for slider
def update_figure(selected_year):
    filtered_df = df[df.year == selected_year]

    fig = px.scatter(filtered_df, x="gdpPercap", y="lifeExp",
                     size="pop", color="continent", hover_name="country",
                     log_x=True, size_max=55)

    fig.update_layout(transition_duration=500)

    return fig


# launch application
if __name__ == '__main__':
    app.run_server(debug=True)
```

This example loads as `pandas` dataframe using `pd.read_csv()`.

Loading the data outside of the callback functions means that the data loading operation is only done once, rather than each time the function is called.

The callback does not modify the original data--it works with `pandas` filters to to create copies of the `dataframe`.

NOTE: callback functions should NEVER alter the underlying `dataframe`.

We also set the layout transition time using `fig.update_layout()` in combination with `transition_duration`. 

This parameter gives the resulting figure smooth transitions between slider year values.

For more on `dash` callbacks:
- [`plotly`, Basic Dash Callbacks](https://dash.plotly.com/basic-callbacks)
- [`plotly`, Advanced Callbacks](https://dash.plotly.com/advanced-callbacks)

## Additional resources

We're just scratching the surface of what you can do using `dash`.

Callback functions can include multiple inputs and outputs, as well as chained callback functions.
- [`plotly`, Basic Callbacks](https://dash.plotly.com/basic-callbacks)

`dash` also allows for interactive visualizations that change based on how the user is interacting with the visualization.
- [`plotly`, Interactive Visualizations](https://dash.plotly.com/interactive-graphing)

To learn more, consult the [Dash User Guide](https://dash.plotly.com/).

The [Dash Enterprise App Gallery](https://dash-gallery.plotly.host/Portal/) includes sample apps with the back-end `dash` code.

See also: Daniel Barker, ["A short Python tutorial using the open-source Plotly "Dash" library"](https://towardsdatascience.com/a-short-python-tutorial-using-the-open-source-plotly-dash-library-part-i-e59fb1f1a457) *Towards Data Science* (24 April 2018).

## Publishing a `dash` App

In these examples, we are still working in your local environment.

The `dash` app may run in a web browser, but it uses your computer as a local host (`localhost`).

For a `dash` app to be available via the World Wide Web, you need to deploy it to a server.

A few free server options that support public Flask applications (the framework `dash` is built on) include Heroku and Reclaim Hosting.

*Alternatively you could take Prof. Walden's "Minimal Computing" class and build your own LAMP stack server.*

More information on deplying a `dash` app using Heroku (free): [`plotly`, Heroku for Sharing Public Dash apps for Free](https://dash.plotly.com/deployment).

See also: Elsa Scola, ["The Easiest Way to Deploy Your Dash App for Free"](https://towardsdatascience.com/the-easiest-way-to-deploy-your-dash-app-for-free-f92c575bb69e) *Towards Data Science* (2 May 2020).

Another option for acquiring a domain and web hosting is Reclaim Hosting.

"Founded in 2013, Reclaim Hosting provides educators and institutions with an easy way to offer their students domains and web hosting that they own and control. Our goal is to make the process of offering a flexible web space and domain name to your students as easy as possible and giving you the support you need to make it all happen" ([About Reclaim Hosting](https://reclaimhosting.com/about/)).

At the time this tutorial was written (December 2020), [Reclaim Hosting's personal plan](https://reclaimhosting.com/shared-hosting/) is $30 annually and includes 2 GB of storage.

For more information in deploying a Flask app using Reclaim Hosting (or another cPanel provider):
- [`python`, Flask: Deploy an App](https://python-adv-web-apps.readthedocs.io/en/latest/flask_deploy.html)
- Tony Hirst, ["Creating a Simple Python Flash App via cPanel on Reclaim Hosting"](https://blog.ouseful.info/2016/09/17/creating-a-simple-python-flask-app-running-on-reclaim-hosting/) *OUseful.Info* (17 September 2016)

# Practice problems

There are no practice problems. There is only final project proposal work.

# Lab Notebook Questions
