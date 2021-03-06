# Scales and Axes
## Linear Scale
~~~javascript
// naming convention for scaleLinear >> axes for which scaling exectuted
var y = d3.scaleLinear()
		.domain([0, 828])
		.range([0, 400]); 
console.log(y(100)); //48.3
console.log(y(414)); //200

console.log(y.invert(48.3)); //100
console.log(y.invert(200)); //414
~~~
## Log Scale
> For exponentially growing value
~~~javascript
var x = d3.scaleLog()
		.domain([0, 828])
		.range([0, 400])
		.base(10);
console.log(x(500)); //32.9
console.log(x(5000)); //181.1

console.log(x.invert(32.9)); //500
console.log(x.invert(181.1)); //5000
~~~
## Time Scales
> Special type of linear scale
> Time scales take JS data objects, i.e. Date(year, month, day), in their domain instead of integers
~~~javascript
var x = d3.scaleTime()
		.domain([new Date(2000, 0, 1), new Date(2001, 0, 1)])
		.range([0, 400])
		.base(10);
console.log(x(new Date(2000, 7, 1))); //199
console.log(x.invert(199)); //Tue Aug 01 2000
~~~
## Ordinal Scale
> For coloring: category to color (mapping)
> RANGE 에 mapping 이 없다면 RANGE value 를 loop back

> Color Schemes
> ref) https://github.com/d3/d3-scale-chromatic
~~~javascript
var color = d3.scaleOrdinal()
		.domain(["Africa", "America", "Asia",])
		.range(["RED", "ORANGE", "YELLOW"]);
console.log(color("Africa")); // "RED"
console.log(color("Asia")); // "YELLOW"
console.log(color("China")); // "RED"
console.log(color("USA")); // "ORANGE"
~~~
~~~javascript
// using d3 color scheme
var color = d3.scaleOrdinal()
		.domain(["Africa", "America", "Asia",])
		.range(d3.schemeCategory10);
console.log(color("Africa")); // #1f77b4
console.log(color("Asia")); // #9467bd
console.log(color("China")); // #e377c2
console.log(color("USA")); // #7f7f7f
~~~

## Band Scale
> For bar chart

![band scale](https://raw.githubusercontent.com/d3/d3-scale/master/img/band.png)
> Domain 은 Ordinal Scale 로 Range 는 Linear Scale 로 전달
~~~javascript
var x = d3.scaleBand()
		.domain(["Africa", "America", "Asia",])
		.range([0,400])
		.paddingInner(0.2)
		.paddingOuter(0.2);
console.log(x("Africa")); // #1f77b4
console.log(x("Asia")); // #9467bd

console.log(x.bandwidth()); //45.9
~~~
~~~javascript
d3.json("data/buildings.json").then(function(data){
    console.log(data);

    data.forEach(function(d) {
        d.height = +d.height;
    });

    var x = d3.scaleBand()
        .domain(["Burj Khalifa", "Shanghai Tower", 
            "Abraj Al-Bait Clock Tower", "Ping An Finance Centre", 
            "Lotte World Tower", "One World Trade Center",
            "Guangzhou CTF Finance Center"])
        .range([0, 400])
        .paddingInner(0.3)
        .paddingOuter(0.3);

    var y = d3.scaleLinear()
        .domain([0, 828])
        .range([0, 400]);

    var rects = svg.selectAll("rect")
            .data(data)
        .enter()
            .append("rect")
            .attr("y", 0)
            .attr("x", function(d){
                return x(d.name);
            })
            .attr("width", x.bandwidth) /* matching bandwidth == rectengle width */
            .attr("height", function(d){
                return y(d.height);
            })
            .attr("fill", function(d) {
                return "grey";
            });

})
~~~

## Min, Max, Extent
~~~javascript
d3.json("data/buildings.json").then(function(data){
    console.log(data);

    data.forEach(d => {
        d.height = +d.height;
    });

    var x = d3.scaleBand()
        .domain(data.map(function(d){ /* Call-back function on "data" */
            return d.name;
        }))
        .range([0, 400])
        .paddingInner(0.3)
        .paddingOuter(0.3);

    var y = d3.scaleLinear()
        .domain([0, d3.max(data, function(d){ /* Call-back function on "data" */
            return d.height;
        })])
        .range([0, 400]);

    var rects = svg.selectAll("rect")
        .data(data)
        .enter()
        .append("rect")
        .attr("y", 0)
        .attr("x", function(d){
            return x(d.name);
        })
        .attr("width", x.bandwidth)
        .attr("height", function(d){
            return y(d.height);
        })
        .attr("fill", "grey");

});
~~~
> min 은 최소값을 반환, extent 는 [min value, max value] 를 반환

## Group
~~~js
var margin = { left:100, right:10, top:10, bottom:100 };

var width = 600 - margin.left - margin.right,
    height = 400 - margin.top - margin.bottom;

var g = d3.select("#chart-area")
    .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
    .append("g") /* Append Group */
        .attr("transform", "translate(" + margin.left + ", " + margin.top + ")") /* And translate */
~~~

## Axis Generator
~~~js
var g = d3.select("#chart-area")
    .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
    .append("g")
        .attr("transform", "translate(" + margin.left 
            + ", " + margin.top + ")");

// X Label
g.append("text")
    .attr("class", "x axis-label")
    .attr("x", width / 2)
    .attr("y", height + 140)
    .attr("font-size", "20px")
    .attr("text-anchor", "middle")
    .text("The word's tallest buildings");

// Y Label
g.append("text")
    .attr("class", "y axis-label")
    .attr("x", - (height / 2))
    .attr("y", -60)
    .attr("font-size", "20px")
    .attr("text-anchor", "middle")
    .attr("transform", "rotate(-90)") /* Rotation transformation */
    .text("Height (m)");
~~~
~~~js
d3.json("data/buildings.json").then(function(data){
	console.log(data);
	var xAxisCall = d3.axisBottom(x);
	g.append("g")
	 .attr("class", "x axis")
	 .attr("transform", "translate(0, " + height + ")")
	 .call(xAxisCall)
	 .selectAll("text")
	 .attr("y", "10")
	 .attr("x", "-5")
	 .attr("text-anchor", "end")
	 .attr("transform", "rotate(-40)"); /* Rotation transformation */

	var yAxisCall = d3.axisLeft(y)
		.ticks(3) /* Only 3 intermediate ticks */
		.tickFormat(function(d){ /* Specify tick text format */
		    return d + "m";
		});
	g.append("g")
	 .attr("class", "y-axis")
	 .call(yAxisCall);
})
~~~
> Additional Functions: tickValue([1,2,3]), 

> Axis Generator: axisTop, axisRight, axisBottom, axisLeft

## Bar chart
~~~js
var margin = { left:100, right:10, top:10, bottom:150 };

var width = 600 - margin.left - margin.right,
    height = 400 - margin.top - margin.bottom;

var g = d3.select("#chart-area")
    .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
    .append("g")
        .attr("transform", "translate(" + margin.left 
            + ", " + margin.top + ")");

// X Label
g.append("text")
    .attr("class", "x axis-label")
    .attr("x", width / 2)
    .attr("y", height + 140)
    .attr("font-size", "20px")
    .attr("text-anchor", "middle")
    .text("The word's tallest buildings");

// Y Label
g.append("text")
    .attr("class", "y axis-label")
    .attr("x", - (height / 2))
    .attr("y", -60)
    .attr("font-size", "20px")
    .attr("text-anchor", "middle")
    .attr("transform", "rotate(-90)")
    .text("Height (m)");

d3.json("data/buildings.json").then(function(data){
    console.log(data);

    data.forEach(function(d){
        d.height = +d.height;
    });

    var x = d3.scaleBand()
        .domain(data.map(function(d){ return d.name; }))
        .range([0, width])
        .paddingInner(0.3)
        .paddingOuter(0.3);

    var y = d3.scaleLinear()
        .domain([0, d3.max(data, function(d){
            return d.height;
        })])
        .range([height, 0]); /* Reverse */

    var xAxisCall = d3.axisBottom(x);
    g.append("g")
        .attr("class", "x axis")
        .attr("transform", "translate(0, " + height + ")")
        .call(xAxisCall)
        .selectAll("text")
            .attr("y", "10")
            .attr("x", "-5")
            .attr("text-anchor", "end")
            .attr("transform", "rotate(-40)");

    var yAxisCall = d3.axisLeft(y)
        .ticks(3)
        .tickFormat(function(d){
            return d + "m";
        });
    g.append("g")
        .attr("class", "y-axis")
        .call(yAxisCall);

    var rects = g.selectAll("rect")
        .data(data)
    
    rects.enter()
        .append("rect")
            .attr("y", function(d){ return y(d.height); })
            .attr("x", function(d){ return x(d.name); })
            .attr("width", x.bandwidth)
            .attr("height", function(d){ return height - y(d.height); }) /* Reverse */
            .attr("fill", "grey");

})
~~~
