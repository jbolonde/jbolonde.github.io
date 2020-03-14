## Oregon COVID-19 cases

```{r}
library(tidyverse)
library(plotly)

# read data from github
jhu_url <- paste("https://raw.githubusercontent.com/CSSEGISandData/", 
                 "COVID-19/master/csse_covid_19_data/", "csse_covid_19_time_series/", 
                 "time_series_19-covid-Confirmed.csv", sep = "")

JHU_COVID19_data <- read_csv(jhu_url)

# build US dataset
us_cases <- JHU_COVID19_data %>% 
  rename(province = "Province/State", country_region = "Country/Region") %>% 
  pivot_longer(-c(province, country_region, Lat, Long), names_to = "Date", values_to = "cumulative_cases") %>% 
  filter(country_region == "US") %>%
  mutate(case_date = as.Date(Date, "%m/%d/%y"))

# build oregon dataset
oregon_cases <- us_cases %>% 
  filter(province == "Oregon" & case_date >= "2020-03-09") %>% 
  mutate(daily_cases = cumulative_cases - lag(cumulative_cases, default = first(cumulative_cases)))

# viz of oregon cases
oregon_cases_viz <- plot_ly(oregon_cases, 
                            x = ~oregon_cases$case_date, 
                            y = ~oregon_cases$daily_cases,
                            type = "bar",
                            name = "daily_cases",
                            text = ~oregon_cases$daily_cases,
                            textposition = 'auto',
                            title = "Oregon COVID 19 cases") 

oregon_cases_viz %>% 
  add_lines(x = ~oregon_cases$case_date, 
            y = ~oregon_cases$cumulative_cases,
            name = "Cumulative_cases") %>% 
  layout(title = 'COVID 19 cases')

```
