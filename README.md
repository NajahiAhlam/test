Type '{ name: string; y: unknown; }[]' is not assignable to type '(number | PointOptionsObject | [string, number | null] | null)[]'.
  Type '{ name: string; y: unknown; }' is not assignable to type 'number | PointOptionsObject | [string, number | null] | null'.
    Type '{ name: string; y: unknown; }' is not assignable to type 'PointOptionsObject'.
      Types of property 'y' are incompatible.
        Type 'unknown' is not assignable to type 'number | null | undefined'.ts(2322)
(property) SeriesPieOptions.data?: (number | Highcharts.PointOptionsObject | [string, number | null] | null)[] | undefined
(Highcharts, Highmaps) An array of data points for the series. For the pie series type, points can be given in the following ways:

An array of numerical values. In this case, the numerical values will be interpreted as y options. Example: (see online documentation for example)

An array of objects with named values. The following snippet shows only a few settings, see the complete options set below. If the total number of data points exceeds 
      data: seriesData,
