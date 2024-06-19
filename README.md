'Highcharts.defaultOptions.legend' is possibly 'undefined'.ts(18048)
import Highcharts
     backgroundColor: Highcharts.defaultOptions.legend.backgroundColor || '#FFFFFF',


     Type '{ name: string; data: number[]; }' is not assignable to type 'SeriesOptionsType'.
  Type '{ name: string; data: number[]; }' is not assignable to type 'SeriesAreaOptions | SeriesAreasplineOptions | SeriesBarOptions | SeriesColumnOptions | ... 25 more ... | SeriesWaterfallOptions'.
    Property 'type' is missing in type '{ name: string; data: number[]; }' but required in type 'SeriesWaterfallOptions'.ts(2322)
waterfall.d.ts(781, 9): 'type' is declared here.
No quick fixes available

  series: [{
        name: 'Year 1990',
        data: [631, 727, 3202, 721]
    }, {
        name: 'Year 2000',
        data: [814, 841, 3714, 726]
    }, {
        name: 'Year 2018',
        data: [1276, 1007, 4561, 746]
    }]
