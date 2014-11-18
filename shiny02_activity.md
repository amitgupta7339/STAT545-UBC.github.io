# Stat 545 shiny tutorial using Gapminder data
Julia Gustavsen  

<!--
TO-DO:

* check with others about the way we use the renderUI and the levels of countries, is there something terribly bad about this?
* Consider putting the Gapminder data file into the app's data folder as tsv
* make clearer the link between output in ui.R and output$ in server.R
* make a clear when we will stop for little exercises. 
* should I use dplyr instead of subset() for filtering? Doesn't really change much. 
* discuss can use source(rscript.r) in server.R too to load in functions 
* fix sloppy sentences

-->

# Purpose of this tutorial:

* Introduce Shiny
* Write simple Shiny app using GapMinder data


# What is Shiny?

* In a nutshell: an R  package developped by Rstudio that enables you to share interactive graphics (either locally or on the web)
* Simple way to make interactive plots using R
* Uses R to harness power of html, css and javascript for presenting data on the web
* Find more info here: [Shiny: A web application framework for R](http://shiny.rstudio.com/)

# Installing Shiny 

To get started make sure you have Shiny installed:

```
install.packages("shiny")
```

(It also helps if you are running an up-to-date Rstudio version. )

# ui.R and server.R files

Shiny makes it very easy to write your own, but to keep things simple it does have some requirements for it to run.

To get started, within your Stat 547 course folder (same as your github repo right??? this is important for when we try to run `runGitHub` **nope this will have to be a different folder since the repos will have to be public **) create a folder entitled: "Shiny-apps" and within that folder create a directory entitled "Gapminder-app". You can do this either from your terminal window or using your graphical directory to create these folder. Each Shiny app gets its own folder.

Using the terminal (the "$" here represents your terminal prompt **NOT** your R console prompt) to make our folders:

```
$ mkdir Shiny-apps
$ cd Shiny-apps
$ mkdir Gapminder-app
$ cd Gapminder-app
```

Or use your R studio navigator to make these directories or in R

```
dir.create("Shiny-apps")
dir.create("Shiny-app/Gapminder-app")
```

Your Stat-547 directory now probably looks something like this:
(will make little screenshot later o)

```
├───Stat-547
    ├───Amazing-homework
    ├───Brilliant-notes
    └───Shiny-apps
        └───Gapminder-app
```   

Shiny, in its most basic form, requires only 2 files: ui.R and server.R

Making sure we are in our "Gapminder-app" directory, create an empty R script entitled `ui.R` and an empty R script entitled `server.R`

```
├───Stat-547
    ├───Amazing-homework
    ├───Brilliant-notes
    └───Shiny-apps
        └───Gapminder-app
            ├───ui.R
            └───server.R
```

Your `ui.R` creates your **u**ser **i**nterface for your Shiny app. This is the part that controls the layout, the way you can insert images and where you specify how the user can interact with your app. 
Your `server.R` file serves up your data, reacts to the user input and contains the overall data processing "guts."

## First shiny app

We are going to slowly build our app over the course of this class and the next. We will start by making a very basic interface using  our UI script and slowly start adding some bells and whistles to it. 

So the minimum you can have is:

### `ui.R`

```
shinyUI(fluidPage(
))
```

Let's add a bit of text to that:

```
shinyUI(fluidPage(
titlePanel("Gapminder Shiny app")
))
```

### `server.R`

```
shinyServer(function(input, output) {
})
```

There you've created your first shiny app! Well it will be slighly more exciting if we run it!

### Running Shiny apps

You have three different ways that you can run these apps.

1. From your R console prompt. Make sure you have loaded the Shiny library first. Then use the Shiny command `runApp()` with the name of the **directory** that contains your `server.R` and `ui.R` files.

```
library(shiny)  # only needed once per session
runApp("Gapminder-app")
```

2. If either your `server.R` or `ui.R` file is your current script in R studio press "ctr-shift-enter"

3. R studio is smart and recognizes when you are working with files for a Shiny app. If either your `server.R` or `ui.R` file is your current script in R studio you should see a button at the upper right of your source window that says "Run App". You can click on it and your Shiny app will run. 

![Run Shiny in R studio](img/Shiny-run-app-screenshot-circled.png)

With this option you have a lot of control over how the Shiny app is run.

![Run Shiny options in R studio](img/Shiny-run-app-options-screenshot.png)

Depending on your Rstudio and shiny set up you should see an empty window open (either in Rstudio or in your default web browser). R will be "listening" until we close the window (if in its own web browser window) or when we click on the stop button to tell it to stop listening for feedback. (Insert pic of stop button.)

### Adding text elements to the `ui.R`

Let's add to our `ui.R` 

```
shinyUI(fluidPage(
  titlePanel("Gapminder Shiny app"),
  
  sidebarLayout(
    sidebarPanel("User inputs will be here"),
    mainPanel("My cool graph will go here")
  )
))
```
Leaving our `server.R` script the same, let's re-run our app. 

It should look something like this:

![Gapminder Shiny app first text](img/Gapminder_shiny_app_screenshot_first_text.png)

Let's go quickly go through a few of these commands:

`titlePanel()` gives our app page a title. 

`sidebarLayout()` is one of the most popular shiny app layouts and the one that we will use today. There are other options for setting up a page such as the gridlayout (just like it sounds), tabsets (where you can switch between web-browser like tabs contained in your app), and a similar navbar (which  is like tabsets, but doesn't look like tabs, rather just other sections to click on in your header). See [Shiny application layout guide](http://shiny.rstudio.com/articles/layout-guide.html) for more details.

`sidebarLayout()` always takes two arguments: `sidebarPanel()` and `mainPanel()`. These are fairly self-explanatory. 


# Loading in data  

Let's start using our data from the gapminder dataset. We will load our data into the `server.R`

```
gdURL <- "http://tiny.cc/gapminder"
gDat <- read.delim(file = gdURL) 

shinyServer(function(input, output) {
})
```

So that reads the data in, but what if we want to show some of the data? First we need to make a variable in the server.R

```
gdURL <- "http://tiny.cc/gapminder"
gDat <- read.delim(file = gdURL) 

shinyServer(function(input, output) {

  output$gapminder_table <- renderTable({ 
                                       gDat
  })
})
```

The `output$` is the standard in Shiny to use variables on the server side. Here is were we first see one of our `renderX` functions which is responsible managing our data and "serving" it up.  Here we have told the server that we want to have a table in our output.

If we run our app now we can see that nothing has changed, but for anything to appear we have to add it to our ui.R

Let's to our `ui.R` so that we can have a output for our table. 

```
shinyUI(fluidPage(
  titlePanel("Gapminder Shiny app"),
  
  sidebarLayout(
    sidebarPanel("User inputs will be here"
    ),
    mainPanel("My cool graph will go here",
              tableOutput("gapminder_table")
    )
  )
))
```

This is our first instance of using something generated from the `server.R` We use the `output$` variable name to call this in our `ui.R` and it generates a table.  

# Control widgets 

We will start buidling up our plot in a way so that we can choose a country to display when looking at gdp by year. 

## Filtering data by user input

What if we want to filter our table by country? Let's add in a way to filter our data. We can select certain countries. 

## Adding in a select box 

So first we will create a dropdown menu for the countries. 

The way we do this is by adding control widgets, these are different buttons, drop down menus, sliders and other places for users to give input into your app. Today we will work with 2 different widgets: `selectInput` which enables you to choose from a list of options and `sliderInput` which allows you to choose from a range of values along a bar. For more widgets and info on Shiny widgets check out the [Shiny Widget Gallery](http://shiny.rstudio.com/gallery/widget-gallery.html). You can also get help on a specifc widget from its help menu `?sliderInput`.

So to add in these widgets that the user interacts with we will add them to the `ui.R` page the same way that we were adding in text. We will add our control widgets to our sidepanel. 

We need to give our control widgets at least 2 arguments: first a label so that we can access the value from our `server.R` script  (called "inputId") and second a label for the widget in your ui (called "label" in all input functions). If we pull up the help page for `selectInput` we see we have to give an additional argument: choices. Let's add in a dropdown-menu containing three countries into our `ui.R` page.  

```
shinyUI(fluidPage(
  titlePanel("Gapminder Shiny app"),
  
  sidebarLayout(
    sidebarPanel("User inputs will be here",
      selectInput("select_country", 
                  label = h2("Country"),
                  choices = list("Chad", 
                                 "Iraq", 
                                 "Mali")
                  )
    mainPanel("My cool graph will go here",
                            tableOutput("gapminder_table")
    )
  )
))
```

And when you run your app you should get something that looks like this:

(update[Gapminder Shiny app with select box](img/Gapminder_shiny_app_select_test_box.png))

So now we have a way to select one of three countries. 


## Making Shiny react to your inputs

So now let's associate your `ui.R` with something in `server.R`

So you have options on how  you want your shiny app to respond to the user's input. You can deliver an image, a plot, a table, text, or html. 

We will use `renderText` on the `server.R` side to output the text. 

First let's see how to react to this input and then let's see how to use this to change the display in our table. 

We see the text appear when we run our Shiny app. 

Anything that is is `output$` needs to have an associated `render` function. You can `renderImage`, `renderPlot`, `renderPrint`, `renderTable`, `renderText` and `renderUI` (which renders a Shiny tag object or html).

All of these functions take one argument `renderText()` which is R code nested inside `{}`. We want to use our select box to change this input. We will slightly modify `server.R` to reflect this.

```
gdURL <- "http://tiny.cc/gapminder"
gDat <- read.delim(file = gdURL) 

shinyServer(function(input, output) {
  
  output$gapminder_table <- renderTable({ 
    gDat
  })
  output$output_country <- renderText({
    paste("Country selected", input$select_country)
  })
})
```

If we re-run our app we won't see any difference, but let's add a line into our `ui.R` that will let us display our text. You add these outputs to the `ui.R` the same way that you add static text and the control widgets. For now we will start building a way to see the chosen country in the main area. Let's add `textOutput("output_country")` to the `mainPanel()` of `ui.R`.

```
shinyUI(fluidPage(
  titlePanel("Gapminder Shiny app"),
  
  sidebarLayout(
    sidebarPanel("User inputs will be here",
      selectInput("select_country", 
                  label = "Country",
                  choices = list("Chad", 
                                 "Iraq", 
                                 "Mali")
      )),
      mainPanel("My cool graph will go here",
                textOutput("output_country"),
                tableOutput("gapminder_table")
      )
    )
  ))
```

Now when we run our app we see that this text is "reactive", that is it changes when we select a different "country". We have used the `inputId` "select_country" from `ui.R` to give this value to our `server.R` that in turn serves up this value to our `textOutput("output_country")` which shows us our selected country. Neat!

Let's use this to change how we display our table. Let's change what is inside our renderTable be filtered so that it is only our desired country. 

`server.R`

```
gdURL <- "http://tiny.cc/gapminder"
gDat <- read.delim(file = gdURL) 

shinyServer(function(input, output) {
  
  output$gapminder_table <- renderTable({ 
    one_country_data  <- subset(gDat, country == input$select_country)
  })
  output$output_country <- renderText({
    paste("Country selected", input$select_country)
  })
})
```

Let's add in a way to choose the year. We will use a slider that will let us select the year that we want to start and the end year. We will also put this in the sidebar. 

## Adding in a slider to choose the year

We will add our `sliderInput` control widget to `ui.R`. `sliderInput` requires at least 5 arguments: `inputId` which is the name of the variable when dealing with server.R , `label` this is the text that will be associated with the widget, `min` the lowest value available on the slider, `max` the highest value available on the slider, `value` initial value of the slider. Note that we will have 2 values for the slider so we will pass 2 values for `value` to achieve this. We know that our gapminder data goes from 1952 to 2007, so let's use those values as our min and max. 

```
shinyUI(fluidPage(
  titlePanel("Gapminder Shiny app"),
  
  sidebarLayout(
    sidebarPanel("User inputs will be here",
      selectInput("select_country", 
                  label = "Country",
                  choices = list("Chad", 
                                 "Iraq", 
                                 "Mali")
                  ),
      sliderInput("year_range", 
                  label = "Range of years:",
                  min = 1952, max = 2007, value = c(1955, 2005))
    ),
    mainPanel("My cool graph will go here",
              textOutput("output_country"),
              tableOutput("gapminder_table")              
    )
  )
))
```

When you run your shiny app it should look like this:

[Gapminder Shiny app with select box](img/Gapminder_shiny_app_year_slider_widget.png)

The year has a comma after the thousand digit which makes it look less like a year. Let's fix that by adding the `format` argument to `sliderInput`. We will change it to `format = "####"`. 

```
shinyUI(fluidPage(
  titlePanel("Gapminder Shiny app"),
  
  sidebarLayout(
    sidebarPanel("User inputs will be here",
      selectInput("select_country", 
                  label = "Country",
                  choices = list("Chad", 
                                 "Iraq", 
                                 "Mali")
      ),
      sliderInput("year_range", 
                  label = "Range of years:",
                  min = 1952, max = 2007, 
                  value = c(1955, 2005),
                  format = "####")
    ),
    mainPanel("My cool graph will go here",
              textOutput("output_country"),
              tableOutput("gapminder_table")              
    )
  )
))
```

Ahh much better. 

**Exercise**:  Let's change server.R so that we can filter our table using this slider too.

`server.R`

```
gdURL <- "http://tiny.cc/gapminder"
gDat <- read.delim(file = gdURL) 

shinyServer(function(input, output) {
  
  output$gapminder_table <- renderTable({ 
    one_country_data  <- subset(gDat, country == input$select_country & year >= input$year_range[1] & year <= input$year_range[2] )
  })
  output$output_country <- renderText({
    paste("Country selected", input$select_country)
  })
})
```

* **Exercise**: Add in text output to show the year chosen. 

Now your files should look like:
`server.R`

```
gdURL <- "http://tiny.cc/gapminder"
gDat <- read.delim(file = gdURL) 

shinyServer(function(input, output) {
  
  output$gapminder_table <- renderTable({ 
    one_country_data  <- subset(gDat, country == input$select_country & year >= input$year_range[1] & year <= input$year_range[2] )
  })
  output$output_country <- renderText({
    paste("Country selected", input$select_country)
  })
  output$output_years <- renderText({
    paste("The years selected are between", input$year_range[1], "and", input$year_range[2] )
  })
})
```

Let's add this output to our `ui.R`

```
shinyUI(fluidPage(
  titlePanel("Gapminder Shiny app"),
  
  sidebarLayout(
    sidebarPanel("User inputs will be here",
      selectInput("select_country", 
                  label = "Country",
                  choices = list("Chad", 
                                 "Iraq", 
                                 "Mali")
      ),
      sliderInput("year_range", 
                  label = "Range of years:",
                  min = 1952, max = 2007, 
                  value = c(1955, 2005),
                  format = "####")
    ),
    mainPanel("My cool graph will go here",
              textOutput("output_country"),
              textOutput("output_years"),
              tableOutput("gapminder_table")
              
    )
  )
))
```
## Running Shiny app in "showcase" mode
(optional)
If you want to see  this idea demonstrated more clearly you can run you Shiny app in "showcase made". At your R prompt run:

```
library(shiny)
runApp('Gapminder-app', display.mode= "showcase")
```

The app will highlight what is reacting in `server.R` when you select a different country. 

[Gapminder Shiny app with select box](img/Gapminder_shiny_app_showcase_mode.png)

This is a good way to run more complicated apps when you are learning and are curious about how they work. All of the Shiny examples can be run like this. 

## Finally, making a reactive plot 

Ok so we are getting closer to our goal! Let's learn how to bring in real data to play with. 

We are going to work with your old friend the gapminder data. Let's load in the data. We will load in the data above our shiny server. We will also load in the ggplot2 library since we will use that for our plot.  

### Generate plot from data 

Now that we've laid our beautiful foundation, we are ready to build our plot! So now that we have everything organized we just add our ggplot call to the `server.R` and tell `ui.R` to show our plot. Let's go:

To add a plot we do it in the same way that we add text by using our family of `renderX` calls. This time we will use `renderPlot`. We will use our selected country from the dropdown menu to subset our data set and then we will graph the gross domestic product per capita by year for our selected country. 

Added to our `shinyServer()` in `server.R` :

```
library(ggplot2)
gdURL <- "http://tiny.cc/gapminder"
gDat <- read.delim(file = gdURL) 

shinyServer(function(input, output) {
  
  output$gapminder_table <- renderTable({ 
    one_country_data  <- subset(gDat, country == input$select_country & year >= input$year_range[1] & year <= input$year_range[2] )
  })
  output$output_country <- renderText({
    paste("Country selected", input$select_country)
  })
  output$output_years <- renderText({
    paste("The years selected are between", input$year_range[1], "and", input$year_range[2] )
  })
  output$ggplot_gdp_vs_country <- renderPlot({
   p <-  ggplot(subset(gDat, country == input$country_from_gapminder), aes(x = year, y = gdpPercap))
   p + geom_point() + xlim(c(input$year_range[1], input$year_range[2]))
  })
})
```

Then the only thing left to do is to is to add the plot to `ui.R`. We do this in the same way that we added the text. We add `plotOutput("ggplot_gdp_vs_country")`. Tada! You can try plotting different countries and different years. 

But we see that we have some repetition in our plot and since we want to keep nice clean code that we can easily maintain we should get rid of this. Let's get rid of the redundancy by moving the subsetting of the data out of the plot and table render functions. 

**Explain what happens (or show?) to move the `one_country_data  <- subset(gDat, country == input$select_country & year >= input$year_range[1] & year <= input$year_range[2] )` out of the renderX Function  and explain that it is not in a reactive expression.**
So we can work around this by using a function called `reactive`.

`server.R` with added plot and `reactive()` data. 

```
library(ggplot2)
gdURL <- "http://tiny.cc/gapminder"
gDat <- read.delim(file = gdURL) 

shinyServer(function(input, output) {
  
  one_country_data  <- reactive({
    subset(gDat, country == input$select_country & year >= input$year_range[1] & year <= input$year_range[2] )
  })
   output$gapminder_table <- renderTable({ 
    one_country_data()
    })
  output$output_country <- renderText({
    paste("Country selected", input$select_country)
  })
  output$output_years <- renderText({
    paste("The years selected are between", input$year_range[1], "and", input$year_range[2] )
  })
  output$ggplot_gdp_vs_country <- renderPlot({
    p <-  ggplot(subset(gDat, country == input$country_from_gapminder), aes(x = year, y = gdpPercap))
    p + geom_point() + xlim(c(input$year_range[1], input$year_range[2]))
  })
})
```

Let's run that.

Ok it works. Let's use this in our plot too:

```
gdURL <- "http://tiny.cc/gapminder"
gDat <- read.delim(file = gdURL) 

shinyServer(function(input, output) {
  
  one_country_data  <- reactive({
    subset(gDat, country == input$select_country & year >= input$year_range[1] & year <= input$year_range[2] )
  })
  
  output$gapminder_table <- renderTable({ 
    one_country_data()
    })
  output$output_country <- renderText({
    paste("Country selected", input$select_country)
  })
  output$output_years <- renderText({
    paste("The years selected are between", input$year_range[1], "and", input$year_range[2] )
  })
  
  output$ggplot_gdp_vs_country <- renderPlot({
    p <-  ggplot(one_country_data(), aes(x = year, y = gdpPercap))
    p + geom_point() 
  })
})
```

Run that. No errors, ok let's add the plot to our `ui.R`

```
shinyUI(fluidPage(
  titlePanel("Gapminder Shiny app"),
  
  sidebarLayout(
    sidebarPanel("User inputs will be here",
      selectInput("select_country", 
                  label = "Country",
                  choices = list("Chad", 
                                 "Iraq", 
                                 "Mali")
      ),
      sliderInput("year_range", 
                  label = "Range of years:",
                  min = 1952, max = 2007, 
                  value = c(1955, 2005),
                  format = "####")
    ),
    mainPanel("My cool graph will go here",
              textOutput("output_country"),
              textOutput("output_years"),
              plotOutput("ggplot_gdp_vs_country"),
              tableOutput("gapminder_table")         
    )
  )
))
```

### Using our app with more than 3 countries

Earlier in our app we used the `selectInput` widget to get info from the user about "countries". Many Shiny apps use this sort of input where the options you select are coded into the `ui.R`, however since you are bffs with the Gapminder data and you demand sophistication in your data exploration we will look at an alternative to populating a `selectInput(..., choices = list())` with all 142 countries that are found in the gapminder dataset without typing them all in. 

So what we need to do is to generate a dynamic select list from the server side. We are going to use a new `renderX` function called `renderUI`. It works like the text and plot rendering output called, but will output a **u**ser **i**nterface.

To use this function we will first put a render call in `server.R` 

```
  # Drop-down selection box generated from Gapminder dataset
  output$choose_country <- renderUI({
    selectInput("country_from_gapminder", "Country", as.list(levels(gDat$country)))
  })
```

To get this to display in our app let's add a call to this output in our panel where we let the user choose the year and country. We add a simple call to `uiOutput("choose_country")` in `ui.R` in our sidepanel.  

```
shinyUI(fluidPage(
  titlePanel("Gapminder Shiny app"),
  
  sidebarLayout(
    sidebarPanel("User inputs will be here",
      uiOutput("choose_country"),
      selectInput("select_country", 
                  label = "Country",
                  choices = list("Chad", 
                                 "Iraq", 
                                 "Mali")
      ),
      sliderInput("year_range", 
                  label = "Range of years:",
                  min = 1952, max = 2007, 
                  value = c(1955, 2005),
                  format = "####")
    ),
    mainPanel("My cool graph will go here",
              textOutput("output_country"),
              textOutput("output_years"),
              plotOutput("ggplot_gdp_vs_country"),
              tableOutput("gapminder_table")
              
    )
  )
))
```

We run the app and see that we have a select box that populates from all of the countries in Gapmindder. But now we have a confusing app that still only reacts to our three country drop-down menu. 

Let's tidy up our app a bit and get rid of the old select box, change the select panel box to something more informative, get rid of our silly first sentence "my cool graph will go here", and change the text output for country to something that reflects our new select box (even though it is on the server.R side we see that idvariable name is called the same way as if it were on the `ui.R` page. (**Add better explanation here**)

So now our `server.R` script looks like this:

```
library(ggplot2)
gdURL <- "http://tiny.cc/gapminder"
gDat <- read.delim(file = gdURL) 

shinyServer(function(input, output) {

  # Drop-down selection box generated from Gapminder dataset
  output$choose_country <- renderUI({
    selectInput("country_from_gapminder", "Country", as.list(levels(gDat$country)))
  })
  one_country_data  <- reactive({
    subset(gDat, country == input$country_from_gapminder & year >= input$year_range[1] & year <= input$year_range[2] )
  })
   output$gapminder_table <- renderTable({ 
    one_country_data()
    })
  output$output_country <- renderText({
    paste("Country selected", input$country_from_gapminder)
  })
  output$output_years <- renderText({
    paste("The years selected are between", input$year_range[1], "and", input$year_range[2] )
  })
  
  output$ggplot_gdp_vs_country <- renderPlot({
    p <-  ggplot(one_country_data(), aes(x = year, y = gdpPercap))
    p + geom_point() 
  })
})
```

Our `ui.R` script looks like this:

```
shinyUI(fluidPage(
  titlePanel("Gapminder Shiny app"),
  
  sidebarLayout(
    sidebarPanel("User inputs will be here",
      uiOutput("choose_country"),
      sliderInput("year_range", 
                  label = "Range of years:",
                  min = 1952, max = 2007, 
                  value = c(1955, 2005),
                  format = "####")
    ),
    mainPanel("My cool graph will go here",
              textOutput("output_country"),
              textOutput("output_years"),
              plotOutput("ggplot_gdp_vs_country"),
              tableOutput("gapminder_table")              
    )
  )
))
```

And our app looks like this:

[Gapminder Shiny app with select box](img/Gapminder_shiny_app_select_box_from_gapminder_data.png)

### Adding headers and html gadgets to Shiny App

We can add some different header styles to our `ui.R` app if we want. 

We can change our `ui.R` to have the sidebar title to have a bigger header by using some tags (many of which come from html. If you know html,great, if not, no worries). Let's change our sidebarPanel text to have the formatting of Header 2 (You can have headers 1 to 6 with 1 being to biggest and 6 being the smallest). To do that we wrap the text in `h2()`.

```
shinyUI(fluidPage(
  titlePanel("Gapminder Shiny app"),
  
  sidebarLayout(
    sidebarPanel(
    h2("What country and years do you want to see?"),
      uiOutput("choose_country"),
      sliderInput("year_range", 
                  label = "Range of years:",
                  min = 1952, max = 2007, 
                  value = c(1955, 2005),
                  format = "####")
    ),
    mainPanel("My cool graph will go here",
              textOutput("output_country"),
              textOutput("output_years"),
              plotOutput("ggplot_gdp_vs_country"),
              tableOutput("gapminder_table")              
    )
  )
))
```
Let's get rid of our boring placeholder text and try wrapping our `textOutput()` in a header tag. 

```
shinyUI(fluidPage(
  titlePanel("Gapminder Shiny app"),
  
  sidebarLayout(
    sidebarPanel(
      h2("Choose country and years from Gapminder data set"),
      uiOutput("choose_country"),
      sliderInput("year_range", 
                  label = "Range of years:",
                  min = 1952, max = 2007, 
                  value = c(1955, 2005),
                  format = "####")
    ),
    mainPanel(h3(textOutput("output_country")),
              textOutput("output_years"),
              plotOutput("ggplot_gdp_vs_country"),
              tableOutput("gapminder_table")              
    )
  )
))
```

![Gapminder Shiny app first text with h1 tag](img/Gapminder_shiny_app_screenshot_first_text_header.png)

Like many things in R we can play around with this and take pains to make it beautiful, but today we will just show you one more of these tricks. We can center align the text:

```
shinyUI(fluidPage(
  titlePanel("Gapminder Shiny app"),
  
  sidebarLayout(
    sidebarPanel(
      h2("Choose country and years from Gapminder data set"),
      uiOutput("choose_country"),
      sliderInput("year_range", 
                  label = "Range of years:",
                  min = 1952, max = 2007, 
                  value = c(1955, 2005),
                  format = "####")
    ),
    mainPanel(h3(textOutput("output_country"), align = "center"),
              textOutput("output_years"),
              plotOutput("ggplot_gdp_vs_country"),
              tableOutput("gapminder_table")              
    )
  )
))
```

![Gapminder Shiny app first text with h3 tag centered](img/Gapminder_shiny_app_screenshot_first_text_centered_header.png)

There are many other tags you can use to format your text in Shiny such as `p()` to make paragraphs, and `code()` to display text like code. You can make ordered and unordered lists too, but these will require you to add  "tags" to the function wrapper `tags$ul()` (unordered list item). You can list all tags by running `names(tags)`. You can get further information on these in the [Shiny html tags glossary](http://shiny.rstudio.com/articles/tag-glossary.html).

# Execution

* server.R is run once, 
* code inside `shinyServer()` is run everytime you visit the app so you can use this space to make data for each user. 
* Anything in a `renderX()` is rerun whenever it is changed. This will influence where you put your code to determine what is run just once or what is run everytime the app refreshes. 

* However if you want to change it so that your app does not change right away you can add a "submit" button which is a way to only render the app once it is clicked. `submitButton(text = "Apply Changes")`. Add this to our `ui.R` and no plot appears until we click on the button. 

# Deployment

So you are ready to show you app to the world. What are your options?

## Stat UBC Shiny Pro server

* some students will use Jenny's server-get that organized
* how to determine who? volunteers? -Jenny
* get logins for students -Jenny
*  where to direct other students to deploy on the web?

## Run apps from public Github repositories.

* `runGitHub()` is a way to run an app from github using Rstudio. 
* to use this push your server.R and ui.R files to a public github repo (in their own folder, remember Shiny is particular). `runGitHub()` takes a few arguments, if I had a public repo in the Stat545 organization called "julia_gustavsen_shiny" and I had pushed my Gapminder app `server.R` and `ui.R` (and any other necessary files) to that repo, we could run my app using `runGitHub("STAT545-UBC/julia_gustavsen_shiny",subdir = "Shiny-apps/Gapminder-app/"). The subdirectory argument refers to the subdirectory in the repository. 

## Deploy your app to www.shinyapps.io and send url to users

* From either your `server.R` or `ui.R` app click on the "Publish" button
- include pic
* Follow the directions from the website which will basically be:
    * `library(devtools)`
    * ` devtools::install_github("rstudio/shinyapps")`
    * sign up for an account (I used my Github id to login)
    * let Rstudio know what your account is by running the token and secret code that the website gives you ( it will look something like this:  shinyapps::setAccountInfo(name='YourNameHere', token='GeneratedAlphaNumericCode', secret='YoursecretCodeHere')
    * then deploy your app from Rstudio `shinyapps::deployApp('Shiny-apps/Gapminder-app')` and it should deploy the app to a url under your account name (e.g. my gapminder app is here: https://jooolia.shinyapps.io/Gapminder-app/)
  * if you get an error "bad signature" run `Sys.setlocale(locale="en_US.UTF-8")` on linux or mac and `Sys.setlocale(locale="English")` on Windows thanks [Stack Overflow](http://stackoverflow.com/questions/20943687/shinyapps-setaccountinfo-error) and then try setting your account info and then deploying the app again. If the deployment does not work from the console prompt try clicking into your `server.R` or `uir.R` script and then try deployment using the "Publish" button. 


# Link section for further help and ideas 

## Tutorials:

* [R studio Shiny tutorial](http://shiny.rstudio.com/tutorial/)
* [Shiny tutorial by Hadley Wickam (pdf)](https://www.dropbox.com/sh/lk88vl1i4s1cfx0/AAC4Q1IMtal6wYCbLciFTP2Za/shiny/shiny-slides.pdf?dl=0)
* [Make a bilingual Shiny app](http://withr.me/blog/2014/10/17/design-a-bilingual-shiny-application/)
* [Baby Names Shiny Tutorial from Strata in NY (dropbox folder)](https://www.dropbox.com/sh/lk88vl1i4s1cfx0/AABnkGh6XitTe6Al0QVXdUfca/shiny?dl=0)
* [Shiny Cheatsheet by RStudio](http://shiny.rstudio.com/articles/cheatsheet.html)

## Demos:
* [R Graph Catalog](http://shinyapps.stat.ubc.ca/r-graph-catalog/)
* [Shiny Dashboard example using some toy data](http://glimmer.rstudio.com/reinholdsson/shiny-dashboard/)
* [World Bank population data and using the "animint" package for animation](https://cpsievert.shinyapps.io/animintRmarkdown/)
* [dygraphs (pretty time-series graphs)](http://rstudio.github.io/dygraphs/)
* [Gallery of Shiny Apps](http://www.showmeshiny.com/)

## Interactive plots (not neccessarily Shiny):
* [Karl Broman: "Why aren't all of our graphs interactive"(using json d3.js) (presentation)](https://www.biostat.wisc.edu/~kbroman/presentations/InteractiveGraphs3/)
* [New York Times: Corporate Taxes in the US](http://www.nytimes.com/interactive/2013/05/25/sunday-review/corporate-taxes.html?smid=pl-share)