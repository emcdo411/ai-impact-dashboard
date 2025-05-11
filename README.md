# 📊 AI-Powered Career Tools & Stock Market Dashboard

This interactive RShiny dashboard explores the growth of AI-powered career platforms and their impact on stock market performance. It allows users to toggle between light and dark themes, view interactive plots, and explore data tables with filtering options.

## 🚀 Features

- Adoption trends of AI in enterprise vs. personal use.
- Startup funding for career platforms powered by AI.
- AI usage growth over time.
- Stock performance of AI companies vs. S&P 500.
- Sector revenue projections and volatility insights.

## 🧠 Tech Stack

- **R**, **shiny**, **shinydashboard**
- **plotly** for interactive charts
- **DT** for data tables
- **quantmod** (simulated in this app)
- **shinyWidgets** for enhanced UI elements

## 🔧 How to Run

```R
# Required packages
install.packages(c("shiny", "shinydashboard", "plotly", "DT", "shinyWidgets", "quantmod"))

# Run the app
shiny::runApp("app_directory")
```

## 📂 App Structure

Below is the core server and UI logic:

<details>
<summary>📜 Click to expand R code</summary>

```r
library(shiny)
library(shinydashboard)
library(plotly)
library(DT)
library(shinyWidgets)
library(quantmod)

# Define data
adoption_data <- data.frame(
  Year = c(2018, 2019, 2020, 2021, 2022, 2023),
  Enterprise = c(10, 15, 30, 45, 60, 75),
  Personal = c(5, 10, 20, 35, 50, 65)
)

startup_data <- data.frame(
  Platform = c("AIResume", "JobGenie", "SmartHire", "SkillMatch"),
  Funding = c(5, 10, 7, 3)
)

usage_data <- data.frame(
  Year = rep(2018:2023, each = 2),
  Type = rep(c("Career", "Stock"), times = 6),
  Usage = c(1, 2, 3, 5, 6, 7, 9, 12, 13, 15, 17, 20)
)

stock_data <- data.frame(
  Year = 2018:2023,
  AI_Stocks = c(100, 110, 130, 160, 200, 250),
  SP500 = c(100, 105, 110, 120, 130, 140)
)

sector_data <- data.frame(
  Sector = c("Tech", "Healthcare", "Finance", "Education"),
  Revenue = c(200, 150, 100, 50)
)

volatility_data <- data.frame(
  Year = 2018:2023,
  Volatility = c(5, 7, 10, 8, 12, 15)
)

# UI with theme toggle
ui <- dashboardPage(
  dashboardHeader(title = "AI Career & Stock Dashboard"),
  dashboardSidebar(
    sidebarMenu(
      menuItem("Adoption", tabName = "adoption"),
      menuItem("Startups", tabName = "startups"),
      menuItem("Usage", tabName = "usage"),
      menuItem("Stock Performance", tabName = "stocks"),
      menuItem("Sectors & Volatility", tabName = "sectors")
    ),
    br(),
    prettySwitch("dark_mode", "Dark Mode", value = FALSE, fill = TRUE)
  ),
  dashboardBody(
    tags$head(tags$style(HTML("
      body.dark-mode {
        background-color: #2c3e50;
        color: white;
      }
      .box {
        background-color: inherit !important;
      }
    "))),
    tabItems(
      tabItem(tabName = "adoption",
              fluidRow(box(plotlyOutput("adoptionPlot"), width = 12))),
      tabItem(tabName = "startups",
              fluidRow(box(DTOutput("startupTable"), width = 12))),
      tabItem(tabName = "usage",
              fluidRow(box(plotlyOutput("usagePlot"), width = 12))),
      tabItem(tabName = "stocks",
              fluidRow(box(plotlyOutput("stockPlot"), width = 12))),
      tabItem(tabName = "sectors",
              fluidRow(box(plotlyOutput("sectorPlot"), width = 6),
                       box(plotlyOutput("volatilityPlot"), width = 6)))
    )
  )
)

server <- function(input, output, session) {
  observe({
    if (input$dark_mode) {
      tags$script(HTML("document.body.classList.add('dark-mode');"))
    } else {
      tags$script(HTML("document.body.classList.remove('dark-mode');"))
    }
  })

  output$adoptionPlot <- renderPlotly({
    plot_ly(adoption_data, x = ~Year) %>%
      add_trace(y = ~Enterprise, name = 'Enterprise', type = 'scatter', mode = 'lines+markers') %>%
      add_trace(y = ~Personal, name = 'Personal', type = 'scatter', mode = 'lines+markers')
  })

  output$startupTable <- renderDT({
    datatable(startup_data)
  })

  output$usagePlot <- renderPlotly({
    plot_ly(usage_data, x = ~Year, y = ~Usage, color = ~Type, type = 'scatter', mode = 'lines+markers')
  })

  output$stockPlot <- renderPlotly({
    plot_ly(stock_data, x = ~Year) %>%
      add_trace(y = ~AI_Stocks, name = 'AI Stocks', type = 'scatter', mode = 'lines+markers') %>%
      add_trace(y = ~SP500, name = 'S&P 500', type = 'scatter', mode = 'lines+markers')
  })

  output$sectorPlot <- renderPlotly({
    plot_ly(sector_data, x = ~Sector, y = ~Revenue, type = 'bar')
  })

  output$volatilityPlot <- renderPlotly({
    plot_ly(volatility_data, x = ~Year, y = ~Volatility, type = 'scatter', mode = 'lines+markers')
  })
}

shinyApp(ui, server)
```

</details>
