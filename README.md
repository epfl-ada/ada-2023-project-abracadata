# Sunscreen to Screen Time: A Cinema Forecast

## Abstract
A film's success is conventionally attributed to factors such as casting decisions, director, and storyline. However, our research aims to study one that is often overlooked: the effect of weather on a movie's success, namely through changes in domestic box office revenue. Whereas a day with sunny weather across the United States might see an increase in revenue, a blizzard in New York may cause more people to stay home, leading to a decrease. In order to mitigate the randomness of weather patterns, we will apply regression discontinuity on our data to discern the underlying differences. The goal of our research is to analyze the correlation between weather conditions and box office revenue.

## Research Questions
To analyze the relationship of weather and box office performance, we aim to answer the following research questions:

1. Does weather generally affect box office revenue for movies?
2. If weather does affect box office revenue for movies, is the causal effect consistent across all cities (homogeneous effect), or is the effect diminished in cities accustomed to extreme weather (heterogeneous effect)?
3. Do specific weather conditions lead to the release of more movies?
4. Do specific weather conditions influence the popularity, in terms of attendance, of certain movie genres?
5. Do blockbuster films or smaller independent productions experience differential box office sensitivity to adverse weather conditions?

We have chosen to focus our analysis exclusively on data from the United States. This decision is based on several factors, including the quantity and quality of data available. Additionally, the United States holds significant importance in the film industry, not only as a representative market but also as a major contributor to global box office revenues. Given the substantial impact of the U.S. on the film industry, we believe that trends observed within the U.S. market are a good representation of international data.

## Additional Datasets 
- [**Weather**](https://www.ncei.noaa.gov/) - Datasets containing daily weather records for cities. We limited the date range to 1992-2012 as weather data becomes unreliable prior to 1992 and the [CMU Movie Summary Corpus](http://www.cs.cmu.edu/~ark/personas/) only contains data until 2012. The raw datasets have entries that specify daily weather, including the columns `station`, `elevation`, and `name`. However, we opted to retain only five columns: `date`, `prcp` (precipitation [mm]), `snow` (snow [mm]), `tmax` (maximum temperature, in tenths of Celsius), and `tmin` (mininum temperature, in tenths of Celsius).

- [**U.S. Box Office**](https://www.boxofficemojo.com/chart/top_lifetime_gross/) - Dataset containing the lifetime gross of movies in the U.S. market as of August 2019. The date range was also restricted to 1992-2012. This dataset serves as an extension to the [CMU Movie Summary Corpus dataset](http://www.cs.cmu.edu/~ark/personas/), which only contains international movie box office revenue. Our analysis focuses on the `lifetime_gross` column; however, we also utilize the `studio` column when addressing our fifth research question.

- [**Inflation Rate**](https://www.bls.gov/data/inflation_calculator.htm) - Dataset containing the annual U.S. inflation rate. The sole column in this dataset, labeled `factor`, is employed to adjust the domestic `lifetime_gross` for each movie based on the corresponding inflation rate. This contributes to a more precise analysis of the data.

## Methods

### Assumptions

When cleaning and parsing the data, a few general assumptions were made to make our data collection more feasible:

- When querying weather data, given the vast differences in weather across the U.S., we decided to analyze data per city. However, this raised the question of granularity: what is the optimal number of cities to select allowing for a good tradeoff between weather representation and computation feasibility. To mitigate this issue, we selected cities that would represent the majority of the U.S. in terms of population, while simultaneously balancing them by considering the number of movie theaters in each city. Ideally, we aimed for a fair representation of all weather types, leading to the selection of the following nine cities to represent the U.S.: New York, Los Angeles, Chicago, Dallas, San Francisco, Seattle, Washington DC, Atlanta, and Miami.

- One aspect of U.S. weather that we decided to add, if time permits, is natural disasters. Unlike Europe, many of the cities mentioned above are prone to experiencing some natural phenomenon that may cause movie theaters to shut down for extended periods of time. In San Francisco, this natural disaster would be earthquakes and, in cases of particularly strong ones, movie theaters may not be running until weeks later. Similarly, in Miami this would be hurricanes. Although not all hurricanes land, even the threat of a hurricane can cause movie theaters to shut down for at least a week as people migrate out of the city.

- One other assumption was regarding snowfall. For cities that do not snow, like Los Angeles and Miami, the weather stations did not record any (`NaN`), so we decided to put these values as `0`.

- We also created a simple formula for calculating the average temperature of a day. While conventional average temperature calculations consider all twenty-four hours, our focus is on the time period when people are typically outdoors for movies. Observing a standard hourly temperature graph reveals that the temperature reaches its daily minimum around 6h-7h and its daily maximum around 15h-16h. Assuming people are outdoors from 7h to 22h, we formulated the following equation for average temperature: $\frac{2}{3} \cdot {T_{high}} + \frac{1}{3} \cdot {T_{low}}$.

- As our research questions revolve around the distinction between “good” and “bad” weather, we decided to analyze different definitions for clarity and depth. The basic definition that we used for our initial analyses was the following: “good” weather is defined as temperature and precipitation being higher or lower than the cumulative average, respectively, and “bad” otherwise.

### Step 1: Dataset Prepocessing
- [**CMU Movie Summary Corpus**](http://www.cs.cmu.edu/~ark/personas/)
    - We cleaned the `movie.metadata.tsv` table to retain the columns: `movie_name`, `movie_genres`, `movie_release_date`, `movie_runtime`, and `movie_box_office_revenue`. 
    - As mentioned earlier, the dataset only contains global box office revenue, while our research questions centered on U.S. domestic box office revenue. This meant that we had to search other data sites, such as [Box Office Mojo](https://www.boxofficemojo.com/chart/top_lifetime_gross/), for our desired data. 
    - The base outline of our project relies on using the movie titles to differentiate between all movies. However, further organizing the data by genre or runtime will enable us to investigate nuances of the relationship between weather and movie revenue. For example, if the general trend shows that people are less likely to go to the movies during rainy weather, a more in-depth analysis of movies may reveal that horror movies increase rather than decrease during rainy weather.
- [**Weather**](https://www.ncei.noaa.gov/)
    - The weather data provided contained many missing values, mainly in snow. We zeroed any `NaN` values as mentioned in **Assumptions** and removed all rows that contained missing temperatures.
    - We also created a new column in the dataset labeled `Tavg` that applies the average temperature rule also mentioned in **Assumptions**.

### Step 2: Data Analysis
- [**Regression discontinuity**](https://en.wikipedia.org/wiki/Regression_discontinuity_design): Albeit seasonal, weather is random. We exploit this feature to construct a regression discontinuity and study the effect of bad weather on box office revenue. We apply a simple linear regression analysis where the outcome is box office revenue and the independent variables are a weather dummy, plus covariates, over the course of several days post movie release. We apply a kernel to put more weight on the days closer to the release date.

- Then, after having answered our main research question, we address the next follow up research questions with further statistical analysis to test our various hypotheses.

### Step 3: Visualization
- Finally, to help better demonstrate our results, we will create a set of appropriate visualizations for our data.

## Proposed Timeline
```
.
├── 20.11.22 - Regression discontinuity analysis
│  
├── 22.11.22 - Additional statistical analysis
│  
├── 01.12.22 - Homework 2 deadline
│    
├── 04.12.22 - Final analysis
│  
├── 11.12.22 - Data story draft
│  
├── 14.12.22 - Finalize code implementations and visualizations
│  
├── 17.12.22 - Finalize data story
│  
├── 22.12.22 - Milestone 3 deadline
.

```

## Taks Distribution

|                  Group Member                    |              Task Distribution for Milestone 2           |
| ------------------------------------------------ | -------------------------------------------------------- |
| Luís Maria Serrano De Vilhena Freire D'Andrade   | Movie and Weather Data Gathering <br> Written Report     |
| Alex Richaume                                    | Movie Data Analysis                                      |
| Katherine Lu                                     | Weather Data Gathering <br> Written Report               |
| Mohammed Kerraz                                  | Weather Data Analysis                                    |
| José Maria De Mattamouros R. Fonseca De Oliveira | Movie Data Analysis <br> Detailing Milestone 3 Processes |
