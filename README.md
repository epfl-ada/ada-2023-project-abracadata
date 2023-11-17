# Sunscreen to Screen Time: A Cinema Forecast

## Abstract
In an [industry](https://en.wikipedia.org/wiki/Film_industry) where millions are invested, recognizing the impact of uncontrollable, yet avoidable, factors is crucial to ensure a prudent allocation of resources. This research seeks to investigate the influence of extreme heat waves or sub-freezing temperatures, accompanied by high winds and formidable storms, on the patterns of cinema attendance. The study delves into the intersection of meteorological nuances and cinematic success, aiming to decipher the influence of adverse weather conditions on the box office performance of movies. While seasonal weather inherently exhibits randomness, this study leverages this characteristic through the application of regression discontinuity. By strategically selecting prominent [U.S. major cities](https://en.wikipedia.org/wiki/Metropolitan_statistical_area), the research endeavors to probe potential correlations between specific weather phenomena and fluctuations in cinema attendance. The goal is to provide an insight into the interplay between environmental factors and box office outcomes.

## Research Questions
Drawing upon our investigation into the correlation between weather conditions and box office performance, we formulated the following five research questions:

1. Does weather affect box office revenue for movies?
2. If weather indeed affects box office revenue for movies, does the causal effect remain consistent across all cities (homogeneous effect), or is the effect diminished in cities accustomed to extreme weather (heterogeneous effect)?
3. Do specific weather conditions lead to the release of more movies?
4. Do specific weather conditions influence the popularity, in terms of attendance, of certain movie genres?
5. Do blockbuster films or smaller independent productions experience differential box office sensitivity to adverse weather conditions?

We have chosen to focus our analysis exclusively on data from the United States. This decision is based on several factors, including the ready availability and diversity of data from the U.S. Additionally, the United States holds significant importance in the film industry, not only as a representative market but also as a major contributor to global box office revenues. Given the substantial impact of the U.S. on the film industry, we believe that trends observed within the U.S. market are likely to be reflective of, and mirrored in, international data as well.

## Additional Datasets 
- [**Weather**](https://www.ncei.noaa.gov/) - Datasets containing daily weather records for cities. We restricted the date range to 1992-2012 due to the unreliability of weather data before 1992, and the [CMU Movie Summary Corpus dataset](http://www.cs.cmu.edu/~ark/personas/) only includes data until 2012. The raw datasets have entries that specify daily weather, including several columns like `station`, `latitude`, `longitude`, `elevation` and `name`. However, we opted to retain only five original columns, deeming them the most crucial and relevant for our analysis project. These columns are: `date`, `prcp` (precipitation, in milimeters), `snow` (snow, in milimeters), `tmax` (maximum temperature, in tenths of degrees Celsius), `tmin` (mininum temperature, in tenths of degrees Celsius).

- [**U.S. Box Office**](https://www.boxofficemojo.com/chart/top_lifetime_gross/) - Dataset containing the lifetime grosses of movies in the U.S. domestic market as of August 2019. The date range was again restricted to 1992-2012. This dataset serves as an extension to the [CMU Movie Summary Corpus dataset](http://www.cs.cmu.edu/~ark/personas/), which only contains international movie box office revenue. Our analysis focuses on the `lifetime_gross` column; however, we also utilize the `studio` column, which will be employed for addressing our fifth research question in later stages.

- [**Inflation Rate**](https://www.bls.gov/data/inflation_calculator.htm) - Dataset containing the U.S. annual inflation rate. The sole column in this dataset, labeled `factor` is employed to adjust the domestic `lifetime_gross` for each movie, based on the inflation rate of the corresponding year. This contributes to a more comprehensive and precise analysis of the data.

## Methods

### Assumptions

When cleaning and parsing the data, a few general assumptions were made to make our data collection more feasible:

- When querying weather data, given the vast differences in weather across the U.S., we decided to analyze data per city. However, we encountered a granularity dilemma: determining the optimal number of cities to query for our data to be representative, if querying any at all. To mitigate this issue, we selected cities that would effectively represent the majority of the U.S. in terms of population, while simultaneously balancing them by considering the number of movie theaters in each city. Ideally, we aimed for a well-rounded and fair representation of all weather types, leading to the selection of the following nine cities to serve as representatives of the U.S.: [New York](https://en.wikipedia.org/wiki/New_York_City), [Los Angeles](https://en.wikipedia.org/wiki/Los_Angeles), [Chicago](https://en.wikipedia.org/wiki/Chicago), [Dallas](https://en.wikipedia.org/wiki/Dallas), [San Francisco](https://en.wikipedia.org/wiki/San_Francisco), [Seattle](https://en.wikipedia.org/wiki/Seattle), [Washington DC](https://en.wikipedia.org/wiki/Washington,_D.C.), [Atlanta](https://en.wikipedia.org/wiki/Atlanta), and [Miami](https://en.wikipedia.org/wiki/Miami).

- One aspect of U.S. weather that we decided to add, if time permits, is [natural disasters](https://en.wikipedia.org/wiki/List_of_natural_disasters_in_the_United_States). Unlike Europe, many of the cities mentioned above are prone to experiencing some natural phenomenon that may cause movie theaters to shut down for extended periods of time. In San Francisco, this natural disaster would be earthquakes and, in cases of particularly strong ones, movie theaters may not be running until weeks later. Similarly, in Miami this would be hurricanes. Although not all hurricanes land, even the threat of a hurricane can cause movie theaters to shut down for at least a week, as people migrate out of the city.

- One other assumption was regarding snowfall. For cities that do not have any snow, like Los Angeles and Miami, the weather stations did not record any snow (`NaN`), and we decided to put these values as `0`.

- We also created a rough formula for calculating the average temperature for a day.While conventional average temperature calculations consider all twenty-four hours, our focus is on the time period when people are typically outdoors for movies. Observing a standard hourly temperature graph reveals that the temperature reaches its daily minimum around 6h-7h and its daily maximum around 15h-16h. Assuming peak activity from 7h to 22h, we formulated the following equation for average temperature: $\frac{2}{3} \cdot {T_{high}} + \frac{1}{3} \cdot {T_{low}}$.

- As our research questions revolve around the distinction between “good” and “bad” weather, we decided to scrutinize different definitions for clarity and depth. For our initial analyses, we employed the following basic definition: "good" weather was determined when the temperature/precipitation exceeded or fell below the cumulative average, and deemed "bad" otherwise.

### Step 1: Datasets Preprocessing
- [**CMU Movie Summary Corpus**](http://www.cs.cmu.edu/~ark/personas/)
    - In accordance with our research questions, we exclusively utilized the `movie.metadata.tsv` table. We performed a cleaning process to retain only the columns: `movie_name`, `movie_genres`, `movie_release_date`, `movie_runtime`, and `movie_box_office_revenue`. 
    - As mentioned earlier, the dataset exclusively provides global box office revenue, while our focus as a group was specifically on U.S. domestic box office revenue. This meant that we had to search other data sites, such as [Box Office Mojo](https://www.boxofficemojo.com/chart/top_lifetime_gross/), to acquire more specific data for merging. 
    - The base outline of our project relies on using the movie title to differentiate between all movies. However, further organizing the data by genre or runtime will enable us to investigate whether there is a discernible trend correlating weather conditions and genre. For example, if the general trend shows that people are less likely to go to the movies during rainy weather, a more in-depth analysis of movies may reveal that individuals tend to prefer horror movies when it's raining compared to sunny weather.
- [**Weather**](https://www.ncei.noaa.gov/)
    - Handling missing values.
    - Creating a new column called Tavg which is the average of max temperature and min temperature.
    - Plotting the distribution.

### Step 2: Data Analysis
- [**Regression discontinuity**](https://en.wikipedia.org/wiki/Regression_discontinuity_design): Albeit seasonal, weather is random. We exploit this feature to construct a regression discontinuity and study the effect of bad weather on the box office revenue of a movie. We apply a simple linear regression analysis where the outcome is box office revenue with the independent variables being the weather dummy, plus some covarieates, over several days after a movie is released. We apply a kernel to put more weight on the days closer to the release date.

- Then, after having answered our main research question, we address the next follow up research questions with further statistical analysis to test our various hypotheses.

### Step 3: Visualization
- Finally, to help better demonstrate our results, we present a set of appropriate visualization for our data, which emphasizes our findings.

## Proposed timeline
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

## Organization within the team
<table class="tg" style="undefined;table-layout: fixed; width: 342px">
<colgroup>
<col style="width: 164px">
<col style="width: 178px">
</colgroup>
<thead>
  <tr>
    <th class="tg-0lax"></th>
    <th class="tg-0lax">Tasks</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0lax">@anni5701</td>
    <td class="tg-0lax"></td>
  </tr>
  <tr>
    <td class="tg-0lax">@kat-04</td>
    <td class="tg-0lax"></td>
  </tr>
  <tr>
    <td class="tg-0lax">@luisvfandrade</td>
    <td class="tg-0lax"></td>
  </tr>
  <tr>
    <td class="tg-0lax">@teozema</td>
    <td class="tg-0lax"></td>
  </tr>
  <tr>
    <td class="tg-0lax">@SimoKerraz</td>
    <td class="tg-0lax"></td>
  </tr>
</tbody>
</table>
