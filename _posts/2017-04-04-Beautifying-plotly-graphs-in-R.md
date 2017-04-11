---
layout: post
title: Beautifying plotly graphs in R.
---
<p align="justify">Hello!
Till now I was styling the graphs in Photoshop rather than prepare them properly in RStudio (sooo lame, huh?) But I decided it's time to learn how to do it, so here is the little tutorial about making a plot beautiful using only R and plotly package. 

We'll gonna play with San Francisco average monthly temperatures availaible <a href="http://www.holiday-weather.com/san_francisco/averages/">here</a>.</p>


What we have to do before plotting a graph:
```r
a <- c(1:12)
b <- c(23, 28,  38, 54,  73, 42,  24, 27,  78, 63,  41, 51)
weather <- data.frame(month=a,temp=b)
weather
   month temp
1      1   10
2      2   11
3      3   11
4      4   11
5      5   13
6      6   13
7      7   14
8      8   14
9      9   14
10    10   14
11    11   13
12    12   10
```

Quick  reminder  about plotly:

<p align="justify"><code>plotly</code> charts are described declaratively in the call signature of <code>plotly::plot_ly</code>, <code>plotly::add_trace</code>, and <code>plotly::layout</code>. Every aspect of a plotly chart (the colors, the grid-lines, the data, and so on) has a corresponding key in these call signatures. This page contains an extensive list of these attributes.</p>

<p align="justify">Plotly's graph description places attributes into two categories: <code>traces</code> (which describe a single series of data in a graph) and <code>layout</code> attributes that apply to the rest of the chart, like the title, xaxis, or annotations).</p>

<p align="justify">Here is a simple example of a plotly chart inlined with links to each attribute's reference section.</p>

```r
library(plotly)

p <- plot_ly(economics,
             type = "scatter",        # all "scatter" attributes: https://plot.ly/r/reference/#scatter
             x = ~date,               # more about scatter's "x": /r/reference/#scatter-x
             y = ~uempmed,            # more about scatter's "y": /r/reference/#scatter-y
             name = "unemployment",   # more about scatter's "name": /r/reference/#scatter-name
             marker = list(           # marker is a named list, valid keys: /r/reference/#scatter-marker
               color="#264E86"        # more about marker's "color" attribute: /r/reference/#scatter-marker-color
             )) %>%
  layout(                        # all of layout's properties: /r/reference/#layout
         title = "Unemployment", # layout's title: /r/reference/#layout-title
         xaxis = list(           # layout's xaxis is a named list. List of valid keys: /r/reference/#layout-xaxis
            title = "Time",      # xaxis's title: /r/reference/#layout-xaxis-title
            showgrid = F),       # xaxis's showgrid: /r/reference/#layout-xaxis-showgrid
         yaxis = list(           # layout's yaxis is a named list. List of valid keys: /r/reference/#layout-yaxis
            title = "uidx")     # yaxis's title: /r/reference/#layout-yaxis-title
  )
```
 
<p align="justify">Having that in mind let’s create the simplest possible barplot with empty layout attribute:</p>

```r
p <- plot_ly(
    x = weather$month,
    y = weather$temp,
    type = "bar"
) %>%
layout()
```

<p align="center">
  <img src="/images/beautifying_plot/naked_plot.png">
</p>

<p align="justify">Now we can start styling it! <a href="https://plot.ly/r/reference/#layout">Here</a> you can check all possible layout properties.</p>

#### Changing background color

<p align="justify">I’m gonna kick off with changing the background. To do so, we have to add 2 keys to <code>layout</code>: <code>paper_bgcolor</code> (sets the color of paper where the graph is drawn.) and  <code>plot_bgcolor</code> (sets the color of plotting area in-between x and y axes.) I chose a nice, royal blue.</p>

```r
layout(paper_bgcolor='#5875D5',
       plot_bgcolor='#5875D5')
```

<p align="center">
  <img src="/images/beautifying_plot/blue.png">
</p>



#### Changing bars color

<p align="justify">If you did too, you can now see that the plot became completely unreadable. Let’s fix it by changing bar’s color to white, adding <code>marker = list(color = '#ffffff')</code> (sets the marker color; it accepts either a specific color or an array of numbers that are mapped to the colorscale relative to the max and min values of the array or relative to `cmin` and `cmax` if set) to <code>plot_ly</code> part:</p>

```r
plot_ly(
    x = weather$month,
    y = weather$temp,
    type = "bar"
    marker = list(color = '#ffffff')
   ) %>%
   layout()
```

#### Styling axis

<p align="justify">Now we can proceed to styling X and Y axis.  We will continue adding the keys to the <code>layout</code> part, right after <code>plot_bgcolor</code>. As both xaxis and yaxis will have many arguments, we will wrap them in <code>list()</code>. See below:</p>

```r
layout(paper_bgcolor='#4666d1',
        plot_bgcolor='#4666d1',
       xaxis = list(place for arguments),
       yaxis = list(place for arguments)
)
```

First thing, let’s adjust the color and make it white:
```r
xaxis = list(
        color = '#ffffff'),
yaxis = list(
        color = '#ffffff')
```

So far, we have this:
<p align="center">
  <img src="/images/beautifying_plot/blue_plot.png">
</p>

<p align="justify">How about adding some text, so we would know what are we looking at? Let’s replace X axis values by month’s names. </p>

`weather$month <- c("Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec")`

<p align="justify">Let’s make them a factor and add levels, so R knows that they have specific order. (You can read more about it in <a href="https://doyouevendata.github.io/Extra-Simple-GGPLOT2-Graphs-in-RStudio/">previous post</a>, section Factors and Levels)</p>

```r
factor(weather$month, levels = c("Jan", "Feb", "Mar", 
                                        "Apr", "May", "Jun", 
                                        "Jul", "Aug", "Sep", 
                                        "Oct", "Nov", "Dec"))
```                                        

<p align="justify">If you re-generate the plot, the month’s names should be visible under the bars.</p>

<p align="center">
  <img src="/images/beautifying_plot/months_prosto.png">
</p>

#### Rotating axis text

<p align="justify">I don’t like it that way, I prefer them rotated a bit. To rotate, add to xaxis a <code>tickangle</code> key.</p>

```r
xaxis = list(
        color = '#ffffff',
        tickangle = -45
),
```

<p align="center">
  <img src="/images/beautifying_plot/months_krzywo.png">
</p>

<p align="justify">Much better. We don’t need to add title to the x axis, I think we can easily guess what it represents, but y axis could use one, therefore we add <code>title</code> key to the yaxis part:</p>

```r
yaxis = list(
        color = '#ffffff',
        title = "degrees in Celsius")

```
<p align="justify">But surely you would say “hey, but I want to show Celsius degrees by printing this cute little circle, like everyone else!” No worries.  Change <code>"degrees in Celsius"</code> to <code>"degrees in \U2103"</code> and you have it. (why U2130 changes to the cute circle? <a href="http://www.fileformat.info/info/unicode/char/2103/index.htm">Here</a> is the answer.)</p>

#### Styling titles

<p align="justify">We nearly have it, we are only missing the general title and some font styling. Let’s add a <code>title</code> to the general layout part:</p>

```r
title = "Average Temperature: San Francisco"
```

<p align="justify">and choose font properties. Again, we will wrap arguments in <code>list()</code>. Read more about title font <a href="https://plot.ly/r/reference/#layout-title">here</a>.</p>

```r
titlefont = list(
                family = "Agency FB",
                size = 30,
                color = '#ffffff')
```            

<p align="justify">I chose the Agency FB font, you can use anything that is present in your operating system. Now what is left is to define the same font for both x and y axis:</p>

```r
font = list(
                family = "Agency FB",
                size = 25)
```   

<p align="justify">As you can see, my plot is too big and doesn’t fit to the window. Let’s add a 10px <code>margin</code>:</p>

```r
         title = "Average Temperature: San Francisco",
         titlefont = list(
           family = "Agency FB",
           size = 45,
           color = '#ffffff'),
         font = list(
           family = "Agency FB",
           size = 25),
         margin = 10,
```

et voila:

<p align="center">
  <img src="/images/beautifying_plot/final.png">
</p>

You will find the complete code at the end of this article.

#### Bars in different colors

<p align="justify">The above plot is simple and not crazy at all. Let's add more colors showing both Celsius and Fahrenheit degrees on one plot. First step, we need to change the white color of bars to something more fancy. I propose <code>#ff9900</code>, or as Html Css Color calls it, Orage Peel. Change:</p>

```r
plot_ly(
  x = weather$month,
  y = weather$temp,
  type = "bar",
  marker = list(color = '#ff9900'),
) %>%
```

Then we need to add Fahrenheit data:

```r
weather$fahrenheit <- c(50,  52,  52,  52,  55,  55,  57,  57,  57,  57,  55,  50)
```

and plot it:

```r
plotly(...)
 %>%
 add_trace(y = ~weather$fahrenheit) %>%
 layout(...)          
```            
<p align="justify">R is quite intelligent and seeing that we are plotting two types of bars it have added a legend on the side. Here is what we gonna do to add a final cut to the plot:</p>

* add names to the traces, so the legend says "Celsius degrees, Farenheit degrees", not trace 0 and trace 1

* change Y axis title, as its no longer Celsius degrees

* add color to Fahrenheit bars (what do you think about <code>#9119b8</code>, called Dark Orchid?)

```r
plot_ly(
...
...
  name = "Celsius degrees"
) %>%
  add_trace(y = ~weather$fahrenheit,
            marker = list(color = '#9119b8' ),
            name = "Fahrenheit degrees"
            ) %>%
```

Here's the result:

<p align="center">
  <img src="/images/beautifying_plot/final_color.png">
</p>
<p align="center">
  <img src="/images/beautifying_plot/meme.png">
</p>


Full code:
<style>
   .tabs {
  position: relative;
  min-height: 700px;
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

<div class="tabs">
    
   <div class="tab">
       <input type="radio" id="tab-1" name="tab-group-1" checked>
       <label for="tab-1">white</label>   
       <div class="content">
           <pre class="code">
        <code class="r">
a <- c(1:12)
b <- c(10,  11,  11,  11,  13,  13,  14,  14,  14,  14,  13,  10)
weather <- data.frame(month=a,temp=b)
weather$month <- c('Jan', 'Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec')
weather$month <- factor(weather$month, levels = c("Jan", "Feb", "Mar", 
                                        "Apr", "May", "Jun", 
                                        "Jul", "Aug", "Sep", 
                                        "Oct", "Nov", "Dec"))

plot_ly(
  x = weather$month,
  y = weather$temp,
  type = "bar",
  marker = list(color = '#ffffff'),
   ) %>%
       layout(paper_bgcolor='#5875D5',plot_bgcolor='#5875D5',
              xaxis = list(
                      color = '#ffffff',
                      tickangle = -45),
              yaxis = list(
                      color = '#ffffff',
                      title = "degrees in \U2103"),
              title = "Average Temperature: San Francisco",
              titlefont = list(
                family = "Agency FB",
                size = 45,
                color = '#ffffff'),
              font = list(
                family = "Agency FB",
                size = 25),
              margin = 10
        )
        </code>
      </pre>
       </div> 
   </div>
    
   <div class="tab">
       <input type="radio" id="tab-2" name="tab-group-1">
       <label for="tab-2">purple-orange</label>   
       <div class="content">
           <pre class="code">
        <code class="r">
a <- c(1:12)
b <- c(10,  11,  11,  11,  13,  13,  14,  14,  14,  14,  13,  10)
c <- c(50,  52,  52,  52,  55,  55,  57,  57,  57,  57,  55,  50)
weather <- data.frame(month=a,temp=b, fahrenheit=c)
weather$month <- c('Jan', 'Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec')
weather$month <- factor(weather$month, levels = c("Jan", "Feb", "Mar", 
                                                  "Apr", "May", "Jun", 
                                                  "Jul", "Aug", "Sep", 
                                                  "Oct", "Nov", "Dec"))

plot_ly(
  x = weather$month,
  y = weather$temp,
  type = "bar",
  marker = list(color = '#ff9900'),
  name = "Celsius degrees"
) %>%
  add_trace(y = ~weather$fahrenheit,
            marker = list(color = '#9119b8' ),
            name = "Fahrenheit degrees"
            ) %>%
  layout(paper_bgcolor='#5875D5',plot_bgcolor='#5875D5',
         xaxis = list(
           color = '#ffffff',
           tickangle = -45),
         yaxis = list(
           color = '#ffffff',
           title = "degrees"),
         title = "Average Temperature: San Francisco",
         titlefont = list(
           family = "Agency FB",
           size = 45,
           color = '#ffffff'),
         font = list(
           family = "Agency FB",
           size = 25),
         margin = 10,
         legend = list(font=list(color = '#ffffff'))
  )

</code></pre>
       </div> 
   </div>    
</div>
