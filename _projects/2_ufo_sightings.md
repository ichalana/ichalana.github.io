---
name: UFO Sightings in the US
tools: [Python, Pandas, Altair, vega-lite]
image:
description: Two Altair + vega-lite views of ~69,000 US UFO reports - when, what shape, and where.
custom_js:
  - vega-5.30.0.min
  - vega-lite-5.20.1.min
  - vega-embed-6.26.0.min
  - justcharts
---

# UFO Sightings in the US (1950–2013)

These two visualizations use the [NUFORC UFO sightings dataset](https://github.com/UIUC-iSchool-DataViz/is445_data/blob/main/ufo-scrubbed-geocoded-time-standardized-00.csv), which has about 80,000 geocoded reports with a date, a reported shape, and a location. I kept the 68,694 reports from the US between 1950 and 2013 (2014 is only partially covered, so I dropped it).

## Plot 1: When were sightings reported, and what did people see?

<vegachart schema-url="{{ site.baseurl }}/assets/json/ufo_timeline_shapes.json" style="width: 100%"></vegachart>

The top chart shows the number of UFO sightings reported each year, and the bottom chart shows how those sightings break down by the shape the witness described. Year is encoded on the x-axis as a **quantitative** field and the count of sightings on the y-axis as a **quantitative** aggregate (`sum`), drawn as an area so the steep rise after the mid-1990s stands out. In the bar chart, shape is a **nominal** field on the y-axis (sorted by count so the most common shapes are on top) and the count is a **quantitative** length on the x-axis. I colored the bars by shape with the `tableau10` scheme: shape is categorical with no order, so it needs distinct hues rather than a light-to-dark ramp, and tableau10 has exactly enough clearly different colors for my 9 named shapes plus "other". On the analysis side, I replaced the invalid `24:00` times with `00:00` so pandas could parse the timestamps, pulled out the year, merged near-duplicate shape labels (for example `changed`/`changing` and `flare`/`flash`), and grouped all but the 9 most common shapes into "other". I then aggregated the data to one row per (year, shape) with a count, which keeps the embedded JSON small while still letting vega-lite re-sum the counts after each filter.

## Plot 2: Where are sightings reported?

<vegachart schema-url="{{ site.baseurl }}/assets/json/ufo_map.json" style="width: 100%"></vegachart>

The map shows where sightings were reported. Each circle is a 1° by 1° grid cell placed by its longitude and latitude (**quantitative** fields in an `albersUsa` projection, which also places Alaska and Hawaii in insets), drawn over gray state outlines. The number of sightings in each cell is a **quantitative** field encoded twice, as circle size and as color, so busy areas stand out both ways. I used the sequential `orangered` color scheme because sightings go from few to many in one direction, so a single-hue light-to-dark ramp reads naturally as "more", and the warm colors stay visible against the gray background. Both size and color use a **log scale**: a handful of metro areas (Los Angeles, New York, Chicago, Seattle, Phoenix) have well over a thousand reports each, and on a linear scale every rural cell would look the same. For the data transformation, I converted latitude and longitude to numbers (dropping the few unparseable rows), rounded each to the nearest whole degree, and counted reports per (grid cell, shape). That turns ~69,000 points into about 6,500 rows, so the map loads quickly.

## Interactivity

**Plot 1** has two linked selections. Drag across the top timeline to **brush a range of years**, and the bar chart re-counts the shapes for only those years. This makes it easy to see that "light" reports take over in recent decades while "disk" (the classic flying saucer) was relatively more common before 1980. **Click a bar** (shift-click to pick several) to filter the timeline to just that shape and see how its popularity changed over time; double-click to clear. **Plot 2** has a **shape dropdown** that filters the map to one shape before the counts are summed, so you can check whether any shape is regional. Both charts have tooltips with exact counts. Together, these turn two fairly busy static charts into something you can use to ask your own questions, such as "what were people seeing in the 1970s?"

## The Data & The Analysis

<div class="left">
{% include elements/button.html link="https://github.com/UIUC-iSchool-DataViz/is445_data/blob/main/ufo-scrubbed-geocoded-time-standardized-00.csv" text="The Data" %}
</div>

<div class="right">
{% include elements/button.html link="https://github.com/ichalana/ichalana.github.io/blob/main/python_notebooks/ufo_sightings_analysis.ipynb" text="The Analysis" %}
</div>
