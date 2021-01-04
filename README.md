# Getting Started With `plotly` and `dash` in Python

<a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license"><img style="border-width: 0;" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" alt="Creative Commons License" /></a>
This tutorial is licensed under a <a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license">Creative Commons Attribution-NonCommercial 4.0 International License</a>.

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
    
[Click here](https://raw.githubusercontent.com/kwaldenphd/dash-python/main/dash-python.ipynb) and select the "Save As" option to download this lab as as Jupyter Notebook.

# Overview

1. We may want to create an interactive web application featuring a `plotly` figure.

2. We see this kind of interactivity in dashboard-style interfaces, like the [Johns Hopkins University COVID-19 Dashboard](https://coronavirus.jhu.edu/map.html).

3. We can use the `dash` web application framework in combination with `plotly` to "publish" a figure as an interactive web application.

4. `dash` was developed by the Plotly team and released in 2017.

5. In addition to a limited free open-source version, Plotly also offers paid enterprise versions of `dash`.

6. What exactly is `dash`?

7. "Dash is a productive Python framework for building web analytic applications. Written on top of Flask, Plotly.js, and React.js, Dash is ideal for building data visualization apps with highly custom user interfaces in pure Python. It's particularly suited for anyone who works with data in Python. Through a couple of simple patterns, Dash abstracts away all of the technologies and protocols that are required to build an interactive web-based application. Dash is simple enough that you can bind a user interface around your Python code in an afternoon. Dash apps are rendered in the web browser. You can deploy your apps to servers and then share them through URLs. Since Dash apps are viewed in the web browser, Dash is inherently cross-platform and mobile ready" (`plotly`, [Introduction to Dash](https://dash.plotly.com/introduction)).

8. For those familiar with RStudio's [Shiny package](v) (designed to build interactive web applications from within the R/RStudio programming environment), `dash` was originally pitched as the equivalent package for Python.

9. Though originally built exclusively for `Python`, `dash` has since released versions for R/RStudio and Julia.

10. For more background on `dash`:
- plotly, ["Introducing Dash"](https://medium.com/plotly/introducing-dash-5ecf7191b503) *Medium* (21 June 2017)
- Plotly, ["PLOTCON 2016: Chris Parmer, Dash: Shiny for Python"](https://youtu.be/5BAthiN0htc) *YouTube* (1 December 2016)
- `plotly`, [Dash Enterprise App Gallery](https://dash-gallery.plotly.host/Portal/)
- [Plotly, Dash GitHub repository](https://github.com/plotly/dash)

11. To install `dash`:
- using `pip`: `pip install dash`
- in Jupyter notebook using `pip`: `pip install jupyter-dash`
- in Jupyter notebook using `conda`: `conda install -c conda-forge -c plotly jupyter-dash`

12. The remainder of this tutorial focuses on `dash` outside the Jupyter notebook environment.

13. For more on using `dash` in a notebook environment: plotly, [Jupyter-Dash Github Repository](https://github.com/plotly/jupyter-dash)

## Basic `dash` Syntax

14. `dash` apps include two main components.

15. The app ***layout*** describes what the application looks like.

16. The app ***interactivity*** describes the application's interactivity.

17. `dash` uses pre-built Python classes for all visual components of the application (which can be customized if needed using JavaScript and React.js).

18. The basic syntax for creating an application using `dash`:

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

19. Let's create an app using our tile-based choropleth map.
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

20. At the bottom of each documentation page for a `plotly` figure type will be a "What About Dash?" section that provides a brief overview of how to publish that figure in a `dash` application.

21. For example, [link to the "What About Dash?"](https://plotly.com/python/scattermapbox/#what-about-dash) section of the "Scatter Plots on Mapbox in Python" page.

## Customizing `dash` Layout with `dash_html_components`

22. This example makes very few modifications to `app.layout`.

23. We can start to customize a `dash` app by using an external style sheet.

24. Similar to how we use CSS in combination with HTML, external style sheets can also be loaded and applied in `dash`.
```Python
# load style sheet; this example uses URL; could also use local .css file
external_stylesheets = ['https://codepen.io/chriddyp/pen/bWLwgP.css']

# call style sheet when starting app
app = dash.Dash(APP_NAME, external_stylesheets=external_stylesheets)
```

25. We can also start to modify components of the application by declaring HTML tags.

26. Remember in HTML, tags like `<h1>`, `<p>`, etc. are used for styling and formatting.

27. The `dash_html_components` library has a component for every HTML tag.

28. We can create declarations for those elements to govern how they are formatted or styled in the `dash` application.

29. This is similar to how we create declarations in an external style sheet.

30. Let's modify our app layout to include a `H1` title, a `div` subtitle, and a `Graph` object.

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

32. We see the component declarations nested under `app.layout`.

33. `dash` generates an HTML element for each of these components.

34. For example, `dash` takes `html.H1(children='Hello Dash')` and generates `<h1>Hello Dash</h1>`.

35. `dash` can also take keyword arguments as part of these component declarations.

36. Let's say we want to override the external style sheet or set formatting for an element not convered in the style sheet.
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

37. We create the `colors` dictionary with `background` and `text` keys and color values.

38. We apply those custom colors to the figure using `fig.update_layout`.

39. We apply those custom colors as keyword arguments for the `app.layout` components.

40. For more on the `dash-html-components` library: [`plotly`, Dash HTML Components](https://dash.plotly.com/dash-html-components)

41. For more on the `dash` layout options: [`plotly`, Dash Layout](https://dash.plotly.com/layout)

## Customizing `dash` Layout Using `dash_core_components`

42. Where `dash` really shines (and has functionality that compares with RStudio Shiny) is in the higher-level core components.

43. These core components include things like:
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

44. These core components are generated through a combination of JavaScript, HTML, and CSS in combination with the React.js library.

45. The syntax for `dash-core-components` is similar to the HTML element declarations in the previous sections.

46. A few examples are provided below.

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

47. You're starting to get the picture.

48. For more examples and documentation:
- [`dcc.RadioItems()`](https://dash.plotly.com/dash-core-components/radioitems)
- [`html.Button()`](https://dash.plotly.com/dash-html-components/button)
- [`dcc.DatePickerSingle()`](https://dash.plotly.com/dash-core-components/datepickersingle)
- [`dcc.DatePickerRange()`](https://dash.plotly.com/dash-core-components/datepickerrange)
- [`dcc.Upload()`](https://dash.plotly.com/dash-core-components/upload)
- [`dcc.Tabs()`](https://dash.plotly.com/dash-core-components/tabs)
- [`dcc.Graph()`](https://dash.plotly.com/dash-core-components/graph)


49. For more on the `dash_core_components` library: [`plotly`, Dash Core Components](https://dash.plotly.com/dash-core-components)

50. For more on the `dash_html_components` library: [`plotly`, Dash HTML Components](https://dash.plotly.com/dash-html-components)

## Setting Up Callbacks

51. At this point, these layout components are interactive in the sense that you can interact with them.

52. But the components are not response, in the sense that interacting with them does not change the app's behavior.

53. We need to make the layout components responsive and interactive.

54. We can do this in `dash` using callback functions.

55. In `dash` syntax, callback functions are Python functions called by `dash` whenever an input component's property changes.

56. An example that uses a responsive input box.
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

57. Let's walk through what happens in this example.

58. The application inputs and outputs are described using the `@app.callback` decorator, similar to the layout and component declarations covered previously.

59. The decorator instructs `dash` to call this function whenever the input value changes, so the output components are updated accordingly.

60. What you name the function arguments is up to you, as long as the names in the function definition match the names in the callback.

61. The `@app.callback` decorator references components described in `app.layout`.

62. Thus the component names in `@app.callback` need to reflect the component names used with `app.layout`.

63. In this example, those component names are `my-input` and `my-output`.

64. We see those names set using the `id` parameter with `app.layout`, and reflected in the callback.

65. The `@app.callback` decorator should always come immediately before the `def` function definition.

66. For more on function decorators in Python: [`python`, PEP 318 -- Decorators for Functions and Methods](https://www.python.org/dev/peps/pep-0318/)

67. In `dash` syntax, application inputs and outputs are treated as component properties. 

68. In this example, the `my-input` component is set to take the input as its `value`, and `my-output` is set to reflect the `my-input` value.

69. Whenever an input property changes (in this example characters in the input box change), the callback function is automatically called.

70. `dash` provides the function with the new input value and updates the output component property.

71. Note that we had to import `from dash.dependencies import Input, Output`, which is different than `dash_core_components.Input`.

72. Taken together, this functionality is called ***reactive programming***.

73. The keyword arguments used to describe each component become incredibly important when setting up callback functions.

74. Let's look at another application that uses callbacks.

75. In this example, global population data is plotted as a bubble chart.

76. Points are colored by continent, sized by population, and a slider is used to mark each year of available data.

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

77. This example loads as `pandas` dataframe using `pd.read_csv()`.

78. Loading the data outside of the callback functions means that the data loading operation is only done once, rather than each time the function is called.

79. The callback does not modify the original data--it works with `pandas` filters to to create copies of the `dataframe`.

80. NOTE: callback functions should NEVER alter the underlying `dataframe`.

81. We also set the layout transition time using `fig.update_layout()` in combination with `transition_duration`. 

82. This parameter gives the resulting figure smooth transitions between slider year values.

83. For more on `dash` callbacks:
- [`plotly`, Basic Dash Callbacks](https://dash.plotly.com/basic-callbacks)
- [`plotly`, Advanced Callbacks](https://dash.plotly.com/advanced-callbacks)

## Additional resources

84. We're just scratching the surface of what you can do using `dash`.

85. Callback functions can include multiple inputs and outputs, as well as chained callback functions.
- [`plotly`, Basic Callbacks](https://dash.plotly.com/basic-callbacks)

86. `dash` also allows for interactive visualizations that change based on how the user is interacting with the visualization.
- [`plotly`, Interactive Visualizations](https://dash.plotly.com/interactive-graphing)

87. To learn more, consult the [Dash User Guide](https://dash.plotly.com/).

88. The [Dash Enterprise App Gallery](https://dash-gallery.plotly.host/Portal/) includes sample apps with the back-end `dash` code.

89. See also: Daniel Barker, ["A short Python tutorial using the open-source Plotly "Dash" library"](https://towardsdatascience.com/a-short-python-tutorial-using-the-open-source-plotly-dash-library-part-i-e59fb1f1a457) *Towards Data Science* (24 April 2018).

## Publishing a `dash` App

90. In these examples, we are still working in your local environment.

91. The `dash` app may run in a web browser, but it uses your computer as a local host (`localhost`).

92. For a `dash` app to be available via the World Wide Web, you need to deploy it to a server.

93. A few free server options that support public Flask applications (the framework `dash` is built on) include Heroku and Reclaim Hosting.
  - *ND Students: Alternatively you could take Prof. Walden's "Minimal Computing" class and build your own LAMP stack server.*

94. More information on deplying a `dash` app using Heroku (free): [`plotly`, Heroku for Sharing Public Dash apps for Free](https://dash.plotly.com/deployment).

95. See also: Elsa Scola, ["The Easiest Way to Deploy Your Dash App for Free"](https://towardsdatascience.com/the-easiest-way-to-deploy-your-dash-app-for-free-f92c575bb69e) *Towards Data Science* (2 May 2020).

96. Another option for acquiring a domain and web hosting is Reclaim Hosting.

97. "Founded in 2013, Reclaim Hosting provides educators and institutions with an easy way to offer their students domains and web hosting that they own and control. Our goal is to make the process of offering a flexible web space and domain name to your students as easy as possible and giving you the support you need to make it all happen" ([About Reclaim Hosting](https://reclaimhosting.com/about/)).

98. At the time this tutorial was written (December 2020), [Reclaim Hosting's personal plan](https://reclaimhosting.com/shared-hosting/) is $30 annually and includes 2 GB of storage.

99. For more information in deploying a Flask app using Reclaim Hosting (or another cPanel provider):
- [`python`, Flask: Deploy an App](https://python-adv-web-apps.readthedocs.io/en/latest/flask_deploy.html)
- Tony Hirst, ["Creating a Simple Python Flash App via cPanel on Reclaim Hosting"](https://blog.ouseful.info/2016/09/17/creating-a-simple-python-flask-app-running-on-reclaim-hosting/) *OUseful.Info* (17 September 2016)
