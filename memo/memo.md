Project memo
================
Team name

This document should contain a detailed account of the data clean up for
your data and the design choices you are making for your plots. For
instance you will want to document choices you’ve made that were
intentional for your graphic, e.g. color you’ve chosen for the plot.
Think of this document as a code script someone can follow to reproduce
the data cleaning steps and graphics in your handout.

``` r
library(tidyverse)
library(broom)
library(readxl)
install.packages("ggridges")
library(ggridges)
#GOLD assessment results for all EHS students
EHS_all_students <- read_excel("../data/ignore/Promise Early Education 2024-2025 Data- CLEAN.xlsx", 
    sheet = "EHS (birth-3)")

#GOLD assessment results for white vs. non-white EHS students
EHS_with_race <- read_excel("../data/ignore/Promise Early Education 2024-2025 Data- CLEAN.xlsx", 
    sheet = "EHS Race")

#GOLD assessment results for DLL vs non-DLL EHS students
EHS_Dual_Language <- read_excel("../data/ignore/Promise Early Education 2024-2025 Data- CLEAN.xlsx", 
    sheet = "EHS Eng SE")

#GOLD assessment results for multi-year vs first-year participating EHS students
EHS_multi_year <- read_excel("../data/ignore/Promise Early Education 2024-2025 Data- CLEAN.xlsx", 
    sheet = "EHS MY-NMY")
```

## Data Clean Up Steps for Overall Data

Since the data that we received from Promise Early education was not
formatted well to put into R and would be extremely complicated to clean
up using code, we did all of our data cleaning in Google sheets. This
included formatting all variables into columns, and adding factors such
as race, years participated in the program, language status, year, etc.
for groups of students.

## Plots

#### Final Plot 1: Comparing GOLD assessment scores of Dual Language vs. Non Dual-Language Learners

``` r
EHS_Dual_Language |>
  mutate(
    DLL_Group = if_else(`Dual Langage` == "Yes", "DLL", "Non-DLL"),
    `Time Period` = fct_relevel(`Time Period`, 
                                "Fall 2024/2025", 
                                "Winter 2024/2025", 
                                "Spring 2024/2025"),
    Average = as.numeric(Average)
  ) |>
  group_by(Category, DLL_Group, `Time Period`) |>
  summarise(
    Weighted_Average = sum(Average * `# Children`, na.rm = TRUE) / sum(`# Children`, na.rm = TRUE),
    Total_Children = sum(`# Children`, na.rm = TRUE),
    .groups = "drop"
  ) |>
  ggplot(aes(x = `Time Period`, y = Weighted_Average, 
             color = DLL_Group, group = DLL_Group)) +
  geom_line(linewidth = 1) +
  geom_point(size = 2) +
  facet_wrap(~ Category) +
  labs(
    title = "Average Skill Scores: DLL (Dual Language Learners) vs. Non-DLL Students",
    caption = "Non-DLL students scored higher than DLL students in all skill areas, with both groups improving from Fall to Spring.",
    x = "Assessment Period (2024–2025 School Year)",
    y = "Weighted Average Score",
    color = "Group"
   ) + scale_x_discrete(
  labels = c(
    "Fall 2024/2025" = "Fall",
    "Winter 2024/2025" = "Winter",
    "Spring 2024/2025" = "Spring"
  )
) +
  scale_color_brewer(palette = "Set2") + 
  theme(
    axis.text.x = element_text(angle = 45, hjust = 1),
    plot.margin = margin(t = 10, r = 10, b = 40, l = 10),
    plot.caption = element_text(hjust = 0.5),
  )
```

![](memo_files/figure-gfm/dual-language-plot-1.png)<!-- -->

``` r
ggsave("dual-language-plot.png")
```

    ## Saving 7 x 5 in image

### Final Plot 2: Comparing GOLD assessment scores of Multi-Year vs. First Time participants of the Promise Program

``` r
EHS_multi_year |>
  mutate(
    Program_Status = if_else(`Multi-Year` == "yes", "Multi-Year", "First-Year")) |>
  group_by(Category, Program_Status) |>
  summarise(
    Weighted_Average = sum(Average * `# Children`) / sum(`# Children`),
    Total_Children = sum(`# Children`),
    .groups = 'drop'
  ) |>
  mutate(
    Legend_Label = paste0(Program_Status, " (n=", Total_Children, ")")
  ) |>
  mutate(
    Category = forcats::fct_reorder(Category, Weighted_Average, .fun = max)
  ) |>
  ggplot(aes(x = Category, y = Weighted_Average, fill = Legend_Label)) +
  geom_col(position = position_dodge()) +
  labs(
    title = "Higher Average Gold Assesment Scores for Multi-Year Students",
    subtitle = "Categories ranked top-down by the highest weighted average score", 
    y = "Score", 
    x = " ",
    fill = "Program Status (Total Children)",
    caption = "Data reflects scores weighted by child enrollment (n) included in the legend. \n Higher scores on the GOLD assessment indicate greater mastery of developmental objectives."
  ) +
  coord_flip() +
  scale_fill_brewer(palette = "Dark2") +
    theme(
    plot.caption = element_text(
      size = 9, 
      hjust = 0.5,
      face = "italic"
    )
  )
```

![](memo_files/figure-gfm/multi-year-plot-1.png)<!-- -->

### Final Plot 3: Comparing progress of GOLD assessment scores from different ages of the Early Head Start Program (EHS)

We initially started with a specific color palate but changed the colors
to a more accessible color pattern. Furthermore, we reordered the bars
in each bar graph in numerical order. In these graphs, we focused on how
different age groups impacted the weighted average assessment scores for
GOLD across various categories. Mathematics, regardless of age,
consistently scored the lowest while Physical scored the highest. This
relatively similar trend occurred over all three tested age ranges, with
only the average values increasing. The graphs were also focused on the
time period between three seasons: Fall 2024/2025, Winter 2024/2025, and
Spring 2024/2025.

``` r
age_one <- EHS_all_students |>
  filter(`age range` == "1-2") |>
  group_by(Category) |>
  summarise(
    Weighted_Average = weighted.mean(Average, `# Children`),
    Children_N = sum(`# Children`),
    .groups = "drop"
  )

ggplot(age_one, aes(x = reorder(Category, Weighted_Average), y = Weighted_Average)) +
  geom_col(fill = "#E69F00") +
  labs(
    title = "Proficiency in Categories by Age Range",
    subtitle = "From Year 1 to Year 2",
    x = "Skills by Catagory", 
    y = "Weighted Average"
  )
```

![](memo_files/figure-gfm/agerange1to2-1.png)<!-- -->

``` r
ggsave("ageplot1.png")
```

    ## Saving 7 x 5 in image

``` r
age_two <- EHS_all_students |>
  filter(`age range` == "2-3") |>
  group_by(Category) |>
  summarise(
    Weighted_Average = weighted.mean(Average, `# Children`),
    Children_N = sum(`# Children`),
    .groups = "drop"
  )

ggplot(age_two, aes(x = reorder(Category, Weighted_Average), y = Weighted_Average)) +
  geom_col(fill = "#56B4E9") +
  labs(
    title = "Proficiency in Categories by Age Range",
    subtitle = "From Year 2 to Year 3",
    y = "Weighted Average", 
    x = "Skills by Catagory"
  )
```

![](memo_files/figure-gfm/agerange2to3-1.png)<!-- -->

``` r
ggsave("ageplot2.png")
```

    ## Saving 7 x 5 in image

``` r
age_birth <- EHS_all_students |>
  filter(`age range` == "B-1") |>
  group_by(Category) |>
  summarise(
    Weighted_Average = weighted.mean(Average, `# Children`),
    Children_N = sum(`# Children`),
    .groups = "drop"
  )

ggplot(age_birth, aes(x = reorder(Category, Weighted_Average), y = Weighted_Average)) +
  geom_col(fill = "#009E73") +
  labs(
    title = "Proficiency in Categories by Age Range",
    subtitle = "From Birth to Year 1",
    y = "Weighted Average", 
     x = "Skills by Catagory"
  ) 
```

![](memo_files/figure-gfm/agerangebto1-1.png)<!-- -->

``` r
ggsave("ageplotbirth.png")
```

    ## Saving 7 x 5 in image

### Final Plot 4: Comparing GOLD assessment scores of students in the EHS program based on race

``` r
EHS_with_race |>
  mutate(Race_Group = if_else(`White (Yes or No)` == "Yes", "White", "Non-White")) |>
  group_by(Category, Race_Group) |>
  summarise(
    Weighted_Average = sum(Average * `# Children`) / sum(`# Children`),
    Total_Children = sum(`# Children`)
  ) |>
  ggplot(aes(x = Category, y = Weighted_Average, fill = Race_Group)) +
  geom_col(position = position_dodge()) +
  geom_text(
    aes(label = paste0("n=", Total_Children)),
    position = position_dodge(width = 1),
    vjust = -0.1
  ) +
  scale_fill_viridis_d()+
  labs(
    title = "Weighted Average GOLD Assessment Scores by Category and Race",
    subtitle = "Counts (n) represent the total number of children contributing to each weighted average.",
    x = "Category",
    y = "Weighted Average Score",
    fill = "Race"
  ) +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

    ## `summarise()` has grouped output by 'Category'. You can override using the
    ## `.groups` argument.

![](memo_files/figure-gfm/race-plot-1.png)<!-- -->

``` r
ggsave("raceplot.png")
```

    ## Saving 7 x 5 in image
