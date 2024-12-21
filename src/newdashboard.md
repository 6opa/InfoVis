---
theme: dashboard
title: Exhibition Analysis Dashboard
toc: false
---

# Exhibition Analysis 📊

<!-- Load and transform the data -->

```js
const exhibitions = FileAttachment("data/artvis_dump_NEW.csv").csv({typed: true});
```
```js
// Funktion zur Deduplizierung der Ausstellungstitel
function uniqueExhibitions() {
  // Deduplizierung anhand des Ausstellungstitels
  return Array.from(
    new Set(exhibitions.map(d => d.e.title))
  ).map(title => exhibitions.find(d => d.e.title === title));
}
```
```js
// Shared color scale for gender categories (Pink for female, Blue for male)
const color = Plot.scale({
  color: {
    type: "categorical",
    domain: ["F", "M"],
    range: ["pink", "blue"],
    unknown: "var(--theme-foreground-muted)"
  }
});
```
```js
// Function to plot exhibitions by gender over time
function exhibitionTimeline(data, {width} = {}) {
  return Plot.plot({
    title: "Exhibitions by Gender Over Time",
    width,
    height: 300,
    y: {grid: true, label: "Number of Exhibitions"},
    x: {
      label: "Year",
      domain: [d3.min(data, d => d.e.startDate), d3.max(data, d => d.e.startDate)],
      tickFormat: "%Y"
    },
    color: {...color, legend: true},
    marks: [
      Plot.barY(
        data,
        Plot.groupX(
          {y: "count"},
          {
            x: "e.startDate",
            fill: "a.gender",
            offset: "zero",
            tip: true
          }
        )
      ),
      Plot.ruleY([0])
    ]
  });
}
```