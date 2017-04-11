---
layout: post
title: Graphs generated dynamically on hover (using R, shiny and plotly)
---
<p align="justify">
Hello! Recently I was asked to create an R program showing one graph and dynamically generating another using the information about mouse position over the first graph. It is not as difficult as I thought, so here's the tutorial.
</p>

<style>
   .tabs {
	position: relative;
	min-height: 650px;
	/* This part sucks */
	clear: both;
	margin: 25px 0;
	font-family: 'Helvetica';
}
.tab {
	float: left;
}
.tab label {
	background: none;
	padding: 10px;
	cursor: pointer;
	margin-left: -1px;
	position: relative;
	left: 1px;
	background: none;
	color: #808080;
	border-style: solid;
	border-width: 1px 1px 1px 1px;
	border-color: #ddd;
	border-radius: 5px 5px 0px 0px;
	border-bottom-color: white;
	padding: 11px;
	padding-bottom: 5px;
}
.tab [type=radio] {
	display: none;
}
.content {
	position: absolute;
	height: 100%;
	top: 28px;
	left: 0;
	right: 0;
	bottom: 0;
	padding: 20px;
	font-family: 'Droid Sans Mono', monospace;
	background: white;
	margin: 0px 0px 10px 0px;
	text-align: left;
	font-size: 11px;
	line-height: 1.2;
	overflow: auto;
	white-space: pre;
	border-top: 1px solid #ddd;
}
[type=radio]:checked~label {
	background: white;
	border-bottom: 1px solid white;
	z-index: 2;
}
[type=radio]:checked~label~.content {
	z-index: 1;
}
pre {
	background: #f5f5f5;
	margin: 10px 0px 10px 0px;
	text-align: left;
	font-size: 11px;
	line-height: 1.2;
	overflow: auto;
	white-space: pre;
	box-shadow: 5px 5px 0 #DDD;
	padding: 10px;
}
</style>


#### What we wanna achieve?
<p align="justify">
We're gonna create a simple R application showing annual and monthly average temperature anomalies. On one static graph we will illustrate the annual  average temperature anomalies, showing that they are constantly growing. While moving the mouse over this graph, we will dynamically generate another graph, which will illustrate monthly average temperature anomalies for the specific year our mouse is pointing to. Let me show this idea on a picture.
</p>
<p align="center">
  <img src="/images/dynamic_histogram/sketch.png">
</p>


#### What we need?
- shiny library, which is "A web application framework for R" and "turns your analyses into interactive web applications"
- plotly library, to draw plots
- 2 R files, one called server.R (script that contains the instructions that your computer needs to build your app) and one called ui.R (script that controls the layout and appearance of your app)
- data

<p align="justify">
You can download data files from <a href="doyouevendata.github.io/images/dynamic_histogram/annual.csv">here</a> and <a href="doyouevendata.github.io/images/dynamic_histogram/monthly.csv">here</a>. These are excel sheets prepared from the data availaible on NASA page (<a href="https://data.giss.nasa.gov/gistemp/">link</a>). Nasa's data looks like that:
</p>
![image_sketch](/images/dynamic_histogram/all_table.png)
<p align="justify">
It contains the year, average temperature anomaly for each month (January to December), than the average values for periods January-December, December-November and each quarter. What we've created from it:</p>
![image_sketch](/images/dynamic_histogram/tables.png)

To install shiny and plotly execute:
```r
install.packages("shiny")
install.packages("plotly")
library("plotly")
library("shiny")
```

#### Building app.
<p align="justify">
Now, our program has 2 components and their schema is imposed by Shiny. You can learn more about Shiny by visiting their website <a href="www.shiny.rstudio.com">www.shiny.rstudio.com</a>
</p>

We will start with ui.R, which by default looks like that:
```r
library(shiny)
ui <- fluidPage(

)
```
We will add few lines:
```r
library(shiny)
library(plotly)

ui <- fluidPage(
    h2("Average temperature anomaly for each year in 1880-2016 period"),
    plotlyOutput("plot"),
    h2("Monthly temperature anomaly for specific year"),
    plotlyOutput("plot2")
)
 ```
where:

* `library(plotly)` is the information that we gonna use plotly library
* `h2("Average temperature anomaly for each year in 1880-2016 period")` is the header of first section
* `plotlyOutput("plot")` is the place for output of the plotly function (the graph) we will call plot
* `h2("Monthly temperature anomaly for specific year")` is the header of second section
* `plotlyOutput("plot2")` is the place for output of the plotly function (the graph) we will call plot2

<p align="justify">This is what we've created so far:</p>
<p align="center">
  <img src="/images/dynamic_histogram/layout.png">
</p>

<p align="justify">
If we are minimalists and don't need our program to be fancy-looking, this is all we have to do. Now, to the server.R part! By default, it looks like that:</p>

```r
library(shiny)
server <- function(input, output) {

}
```
<p align="justify">
We wanna tell our script few things. First, we will use plotly, so we should add <code>library(plotly)</code> just below <code>library(shiny)</code>.Then, we have to import the data we will work on. Also, script has to know that we have created 2 areas to plot graphs, called <code>plot</code> and <code>plot2</code> and the output of our actions should be directed to them. Hence:</p>

```r
library(shiny)
library(plotly)

server <- function(input, output) {

monthly <- read.csv(file="path_to_monthly_file.csv",sep=",",header=TRUE)
annual <- read.csv(file="path_to_annual_file.csv",sep=",",header=TRUE)

  output$plot <- renderPlotly({   
  
    })
  
  output$plot2 <- renderPlotly({    
  
    })
}
```
<p align="justify">We will wrap all the actions generating graphs in a call to <code>renderPlot</code> to indicate that:</p>
  
   * It is "reactive" and therefore should re-execute automatically
       when inputs change
   * Its output type is a plot
   
   
#### Plotting graphs. 
<p align="justify">Now we can go straight to the point, which means plotting graphs! Building the first, static graph will be very easy (level: one-line-easiness). We only have to tell <code>plotly</code> "dude, take my annual dataset, put Years on Y axis, put temperature anomalies on Y axis, make it a scatter chart, where data are presented as line", translate it to plotly language and place in the first plot output:</p>

```r
output$plot <- renderPlotly({
    plot_ly(annual, x=~Year, y=~J.D, type = "scatter", mode="lines")
})
```
<p align="center">
  <img src="/images/dynamic_histogram/annual_graph.png">
</p>
<p align="justify">By the way, <code>plotly</code> is an awesome tool, so awesome you should go right now to <a href="https://plot.ly/feed/">its page</a> and learn more about it. If you are too lazy to do that, just type <code>?plotly</code> into R console. Also, <a href="https://plot.ly/r/reference/">here</a> you can find something about chart types in Plotly.

And now the most interesting part, generating graph dynamically on hover. To know where our mouse pointer is, we have to capture and store mouse event (check <a href="https://www.rdocumentation.org/packages/plotly/versions/4.5.6/topics/event_data">documentation</a>).</p>

```r
mouse_event <- event_data("plotly_hover")
```

<p align="justify">If you are curious how mouse event looks like, here is the one captured while mouse pointer is over year 2016 point of graph:</p>

```r
print(mouse_event)
        curveNumber pointNumber    x    y
1           0         136         2016 1.22
```

<p align="justify">What we need is the year, stored in the third column of event (curveNumber = first column, pointNumber = second column, x = third column). We wanna store that information in a variable called...wait for it....<code>year</code>. Yep.</p>

```r
year <- mouse_event[3]
```

<p align="justify">Now we wanna plot the graph showing monthly temperature anomalies for this particular year, so let's create a subset from our monthly dataset selecting only these rows where year = particular year captured in mouse event.</p>

```r
monthly_subset <- monthly[monthly$Year==year$x,]
```

And plot a graph:

```r
plot_ly(monthly_subset, x=~Month, y=~Deviation, type = "scatter", mode = "points + lines")
```

So to sum this part up, this is what your `output$plot2` should look like:

```r
  output$plot2 <- renderPlotly({
    mouse_event <- event_data("plotly_hover")
    year <- mouse_event[3]
    monthly_subset <- monthly[monthly$Year==year$x,]
    plot_ly(monthly_subset, x=~Month, y=~Deviation,type = "scatter", mode = "points + lines")
  })
  ```

<p align="justify">Or, if you want your code to take only 1 line instead of 4 and be impossible to understand, you can do that:</p>

```r
  output$plot2 <- renderPlotly({
    plot_ly(monthly[monthly$Year==event_data("plotly_hover")[3]$x,], x=~Month, y=~Deviation, type = "scatter", mode="lines + points")
  })
```

<p align="justify">Are you ready to see the final result? Put your mouse pointer over the graph!</p>

<iframe src="https://ymra.shinyapps.io/online/" style="width:100%; height:800px;"></iframe>

You can reviev the files below, or on <a href="https://github.com/ymra/dynamic_histogram">my github</a>.
<div class="tabs">
    
   <div class="tab">
       <input type="radio" id="tab-1" name="tab-group-1" checked>
       <label for="tab-1">ui.R</label>   
       <div class="content">
           <pre class="code">
        <code class="r">
          library(shiny)
          library(plotly)

          ui <- fluidPage(
            h4("Average temperature anomaly for each year in 1880-2016 period",align="center"),
            div(plotlyOutput("plot",width = "500px", height = "300px"), align = "center"),
            h4("Monthly temperature anomaly for specific year",align="center"),
            div(plotlyOutput("plot2",width = "500px", height = "300px"), align = "center")
          )
        </code>
      </pre>
       </div> 
   </div>
    
   <div class="tab">
       <input type="radio" id="tab-2" name="tab-group-1">
       <label for="tab-2">server.R</label>   
       <div class="content">
           <pre class="code">
        <code class="r">
            library(shiny)
            library(plotly)


            server <- function(input, output) {
              # Read data
              monthly <- read.csv(file="monthly.csv", sep=",", header = TRUE)
              annual <- read.csv(file="annual.csv", sep=",", header = TRUE)

              monthly$Month <- factor(monthly$Month, levels = c("Jan", "Feb", "Mar", 
                                      "Apr", "May", "Jun", 
                                      "Jul", "Aug", "Sep", 
                                      "Oct", "Nov", "Dec"))

              output$plot <- renderPlotly({
              plot_ly(annual, x=~Year, y=~J.D,type = "scatter", mode="lines")
              })

              output$plot2 <- renderPlotly({
              mouse_event <- event_data("plotly_hover")
              year <- mouse_event[3]
              monthly_subset <- monthly[monthly$Year==year$x,]
              plot_ly(monthly_subset, x=~Month, y=~Deviation, type = "scatter", mode="lines + points")
              })
            }
</code></pre>
       </div> 
   </div>    
</div>

