Visualising Genomics Data
========================================================
author: MRC Clinical Sciences Centre
date: http://mrccsc.github.io/training.html
autosize: true
author: "MRC CSC Bioinformatics Core Team"
date:http://mrccsc.github.io/training.html
width: 1440
height: 1100
autosize: true
font-import: <link href='http://fonts.googleapis.com/css?family=Slabo+27px' rel='stylesheet' type='text/css'>
font-family: 'Slabo 27px', serif;
css:style.css

Intro slides
========================================================



Topics Covered
========================================================


* Visualising features in Gviz
  + Introduction to Gviz.
  + Plotting coverage over regions.
  + Adding annotation.
  + Plotting reads
  + Plotting splice junctions.
* Visualising high dimensional data
  + Heatmap
  + Principal Component Analysis
* Meta signal of Genomic Intervals/Regions
  + Replicate peaks
  + Average Coverage
  + Motif occurence
* Exporting data to IGV
  + Tracktables
  + Exporting DESeq2 results to IGV

Visualising Genomics Data around Genomic Features
========================================================

Genomics data can often be visualised in genome browsers such as the fantastic IGV genome browser.

This allows for the visualisation our processed data in a specific genomic context.

In genomics it is important to review our data/results and hypotheses in a browser to identify pattern or potential artefacts missed within our analysis.

Visualising Genomics Data around Genomic Features in IGV
========================================================

We have already touched alittle on using the IGV browser to review our data and get access to online data repositories.

IGV is quick, user friendly GUI to perform the essential task of genomics data review.

For more information see our course on IGV [here](http://mrccsc.github.io/IGV_course/).


Visualising Genomics Data around Genomic Features in R
========================================================

Using the genome browser to review sites of interest across the genome is a critical first step.

Using indexed files, IGV offers an method to rapidly interrogate genomics data along the linear genome.

However, as with any GUI, IGV does not offer the flexibility in displaying data we wish to achieve and to review large number of sites demands significant user input or creation of IGV batch scripts.

Visualising Genomics Data around Genomic Features in R (Gviz)
========================================================

The Gviz packages offers methods to produce publication quality plots of genomics data at genomic features of interest.


Lets get started looking at using Gviz in some Biological example.
First we need to install the package.


```r
## try http:// if https:// URLs are not supported
source("https://bioconductor.org/biocLite.R")
biocLite("Gviz")
```




Getting started with Gviz -- Linear genome axis.
========================================================

Gviz provides methods to plot many genomics data types (as with IGV) over genomic features and genomic annotation within a linear genomic reference.


The first thing we can do therefor is set up our linear axis representing positions on genomes.

For this we use our first method from Gviz **GenomeAxisTrack()**.
Here we use the **name** parameter set the name to be "myAxis".


```r
library(Gviz)
genomeAxis <- GenomeAxisTrack(name="MyAxis")
genomeAxis
```

```
Genome axis 'MyAxis'
```

Getting started with Gviz -- Plotting the axis
========================================================

Now we have created a **GenomeAxisTrack** track object we will wish to display the object using **plotTracks** function.

In order to display a axis track we need to set the limits of the plot *(otherwise where would it start and end?)*.


```r
plotTracks(genomeAxis,from=100,to=10100)
```

![plot of chunk unnamed-chunk-3](VizGenomicsData-figure/unnamed-chunk-3-1.png)



Getting started with Gviz -- Configuring the axis (part-1)
========================================================

It is fairly straightforward to create and render this axis.
Gviz offers a high degree of flexibility in the way these tracks can be plotted with some very useful plotting configurations included.

A useful feature is to add some information on the direction of the linear genome represented in this GenomeAxisTrack.

We can add labels for the 5' to 3' direction for the positive and negative strand by using the **add53** and **add35** parameters.


```r
plotTracks(genomeAxis,from=100,to=10100,
           add53=T,add35=T)
```

![plot of chunk unnamed-chunk-4](VizGenomicsData-figure/unnamed-chunk-4-1.png)


Getting started with Gviz -- Configuring the axis (part-2)
========================================================

We can also configure the resolution of the axis (albeit rather bluntly) using the **littleTicks** parameter.

This will add additional axis tick marks between those shown by default.


```r
plotTracks(genomeAxis,from=100,to=10100,
           littleTicks = TRUE)
```

![plot of chunk unnamed-chunk-5](VizGenomicsData-figure/unnamed-chunk-5-1.png)

Getting started with Gviz -- Configuring the axis (part-3)
========================================================

By default the plot labels for the genome axis track are alternating below and above the line.

We can further configure the axis labels using the **labelPos** parameter.

Here we set the labelPos to be always below the axis




```r
plotTracks(genomeAxis,from=100,to=10100,
           labelPos="below")
```

![plot of chunk unnamed-chunk-6](VizGenomicsData-figure/unnamed-chunk-6-1.png)

Getting started with Gviz -- Configuring the axis (part-4)
========================================================

In the previous plots we have producing a genomic axis which allows us to consider the position of the features in the linear genome.

In some contexts we may be more interested in relative distances around and between the genomic features being displayed.

We can then configure the axis track to give us a relative representative of distance using the **scale** parameter



```r
plotTracks(genomeAxis,from=100,to=10100,
           scale=1,labelPos="below")
```

![plot of chunk unnamed-chunk-7](VizGenomicsData-figure/unnamed-chunk-7-1.png)

Getting started with Gviz -- Configuring the axis (part-4b)
========================================================

We may want to add only a part of the scale (such as with Google Maps) to allow the reviewer to get a sense of distance.

We can specify how much of the total axis we wish to display as a scale using a value of 0 to 1 representing the proportion of scale to show.



```r
plotTracks(genomeAxis,from=100,to=10100,
           scale=0.3)
```

![plot of chunk unnamed-chunk-8](VizGenomicsData-figure/unnamed-chunk-8-1.png)


Getting started with Gviz -- Configuring the axis (part-4c)
========================================================

We can also provide numbers greater than 1 to the **scale** parameter which will determine, in absolute base pairs, the size of scale to display.


```r
plotTracks(genomeAxis,from=100,to=10100,
           scale=2500)
```

![plot of chunk unnamed-chunk-9](VizGenomicsData-figure/unnamed-chunk-9-1.png)


Getting started with Gviz -- Axis and Regions of Interest (part-1)
========================================================

Previously we have seen how to highlight regions of interest in the scale bar for IGV.

These "regions of interest" may be user defined locations which add context to the scale and the genomics data to be displayed (e.g. Domain boundaries such as topilogically associated domains)

![ROI](imgs/igv_BookMarks.png)


Getting started with Gviz -- Axis and Regions of Interest (part-2)
========================================================

We can add "regions of interest" to the axis plotted by Gviz as we have done with IGV.

To do this we will need to define some ranges to signify the positions of "regions of interest" in the linear context of our genome track.

Since the plots have no apparent context for chromosomes (yet), we will use a IRanges object to specify "regions of interest" as opposed to the genome focused GRanges.

You can see our material [here](http://mrccsc.github.io/Bioconductor/) on Bioconductor objects for more information on IRanges and GRanges.

Brief recap (Creating an IRanges)
========================================================

To create an IRanges object we will load the IRanges library and specify vectors of **start** and **end** parameters to the **IRanges** constructor function.



```r
library(IRanges)
regionsOfInterest <- IRanges(start=c(140,5140),end=c(2540,7540))
names(regionsOfInterest) <- c("ROI_1","ROI_2")
regionsOfInterest
```

```
IRanges object with 2 ranges and 0 metadata columns:
            start       end     width
        <integer> <integer> <integer>
  ROI_1       140      2540      2401
  ROI_2      5140      7540      2401
```

Getting started with Gviz -- Axis and Regions of Interest (part-3)
========================================================

Now we have our IRanges object representing our regions of interest we can include them in our axis.

We will have to recreate our axis track to allow us to include these regions of interest.

Once we have updated our GenomeAxisTrack object we can plot the axis with regions of interest included.


```r
genomeAxis <- GenomeAxisTrack(name="MyAxis",
                              range = regionsOfInterest)
plotTracks(genomeAxis,from=100,to=10100)
```

![plot of chunk unnamed-chunk-11](VizGenomicsData-figure/unnamed-chunk-11-1.png)



Getting started with Gviz -- Axis and Regions of Interest (part-4)
========================================================

We include the names specified in the IRanges for the regions of interest within the axis plot by specify the **showID** parameter to TRUE.


```r
plotTracks(genomeAxis,from=100,to=10100,
           range=regionsOfInterest,
           showId=T)
```

![plot of chunk unnamed-chunk-12](VizGenomicsData-figure/unnamed-chunk-12-1.png)


Plotting regions in Gviz - Data tracks
========================================================

Now we have some fine control of the axis, it follows that we want some to display some actual data along side our axis and/or regions of interest.

Gviz contains a general container for data tracks which can be created using the **DataTrack()** constructor function and associated object, **DataTrack**.

Generally DataTrack may be used to display all data types with some work but best fit ranges with associated signal as a matrix (multiple regions) or vector (single sample).

Lets update our IRanges object to  some score columns in the metadata columns. We can do this the **mcols** function as shown in our Bioconductor material.



```r
mcols(regionsOfInterest) <- data.frame(Sample1=c(30,20),Sample2=c(20,200))
regionsOfInterest <- GRanges(seqnames="chr5",ranges = regionsOfInterest)
```


Plotting regions in Gviz - Data tracks
========================================================

Now we have the data we need, we can create a simple **DataTrack** object.


```r
dataROI <- DataTrack(regionsOfInterest)
plotTracks(dataROI)
```

![plot of chunk unnamed-chunk-14](VizGenomicsData-figure/unnamed-chunk-14-1.png)


Plotting regions in Gviz - Data tracks
========================================================

As we have seen, Data tracks make use of GRanges which are the central workhorse of Bioconductors HTS tools.

This means we can take advantage of the many manipulations available in the Bioconductor tool set.

Lets use on of rtracklayer's importing tools to retrieve coverage from a bigWig as a GRanges object



```r
library(rtracklayer)
allChromosomeCoverage <- import.bw("Data/small_Sorted_SRR568129.bw",as="GRanges")
allChromosomeCoverage
```

```
GRanges object with 249 ranges and 1 metadata column:
        seqnames         ranges strand |     score
           <Rle>      <IRanges>  <Rle> | <numeric>
    [1]     chrM [1,     16571]      * |         0
    [2]     chr1 [1, 249250621]      * |         0
    [3]     chr2 [1, 243199373]      * |         0
    [4]     chr3 [1, 198022430]      * |         0
    [5]     chr4 [1, 191154276]      * |         0
    ...      ...            ...    ... .       ...
  [245]    chr20 [1,  63025520]      * |         0
  [246]    chr21 [1,  48129895]      * |         0
  [247]    chr22 [1,  51304566]      * |         0
  [248]     chrX [1, 155270560]      * |         0
  [249]     chrY [1,  59373566]      * |         0
  -------
  seqinfo: 25 sequences from an unspecified genome
```


Plotting regions in Gviz - Data tracks (part 4)
========================================================

Now we have our coverage as a GRanges object we can create our DataTrack object from this.

Notice we specify the chr


```r
accDT <- DataTrack(allChromosomeCoverage,chomosome="chr5")
accDT
```

```
DataTrack 'DataTrack'
| genome: NA
| active chromosome: chrM
| positions: 1
| samples:1
| strand: *
There are 248 additional annotation features on 24 further chromosomes
  chr1: 1
  chr10: 1
  chr11: 1
  chr12: 1
  chr13: 1
  ...
  chr7: 1
  chr8: 1
  chr9: 1
  chrX: 1
  chrY: 1
Call seqlevels(obj) to list all available chromosomes or seqinfo(obj) for more detailed output
Call chromosome(obj) <- 'chrId' to change the active chromosome 
```


Plotting regions in Gviz - Data tracks (part 5)
========================================================

To plot data now using the plotTracks() function we will specify the regions we wish to plot by specifying the chromsomes, start and end using the **chromosome**, **start** and **end** parameters.

By default we will get a similar point plot to seen before.


```r
plotTracks(accDT,
           from=134887451,to=134888111,
           chromosome="chr5")
```

![plot of chunk unnamed-chunk-17](VizGenomicsData-figure/unnamed-chunk-17-1.png)


Plotting regions in Gviz - Data tracks (part 6)
========================================================

By default we will get a similar point plot to seen before.

We can adjust the type of plots we want using the **type** argument.
Here as with standard plotting we can specify **"l"** to get a line plot.



```r
plotTracks(accDT,
           from=134887451,to=134888111,
           chromosome="chr5",type="l")
```

![plot of chunk unnamed-chunk-18](VizGenomicsData-figure/unnamed-chunk-18-1.png)


Plotting regions in Gviz - Data tracks (part 6)
========================================================

Many other types of plots are available for the DataTracks.

Including filled plots using "mountain".



```r
plotTracks(accDT,
           from=134887451,to=134888111,
           chromosome="chr5",type="mountain")
```

![plot of chunk unnamed-chunk-19](VizGenomicsData-figure/unnamed-chunk-19-1.png)

Plotting regions in Gviz - Data tracks (part 7)
========================================================

Histograms by specifying "h".


```r
plotTracks(accDT,
           from=134887451,to=134888111,
           chromosome="chr5",type="h")
```

![plot of chunk unnamed-chunk-20](VizGenomicsData-figure/unnamed-chunk-20-1.png)

Plotting regions in Gviz - Data tracks (part 8)
========================================================

Or smoothed plots using "smooth".



```r
plotTracks(accDT,
           from=134887451,to=134888111,
           chromosome="chr5",type="smooth")
```

![plot of chunk unnamed-chunk-21](VizGenomicsData-figure/unnamed-chunk-21-1.png)

Plotting regions in Gviz - Data tracks (part 9)
========================================================

and even a Heatmap using "heatmap".

Notice that Gviz will automatically produce the appropriate Heatmap scale.


```r
plotTracks(accDT,
           from=134887451,to=134888111,
           chromosome="chr5",type="heatmap")
```

![plot of chunk unnamed-chunk-22](VizGenomicsData-figure/unnamed-chunk-22-1.png)

Plotting regions in Gviz - Additional Parameters.
========================================================

As with all plotting functions in R, Gviz plots can be highly customisable.

Simple features such as point size and colour are easily set as for standard R plots using **sex** and **col** paramters.


```r
plotTracks(accDT,
           from=134887451,to=134888111,
           chromosome="chr5",
           col="red",cex=4)
```

![plot of chunk unnamed-chunk-23](VizGenomicsData-figure/unnamed-chunk-23-1.png)
Full description of the parameters


Putting track togethers - Axis and Data
========================================================

Now we have shown how to construct a data track and axis track we can put them together in one plot.

To do this we simply provide the GenomeAxisTrack and DataTrack objects as vector the **plotTracks()** function.



```r
plotTracks(c(accDT,genomeAxis),
           from=134887451,to=134888111,
           chromosome="chr5"
           )
```

![plot of chunk unnamed-chunk-24](VizGenomicsData-figure/unnamed-chunk-24-1.png)
Full description of the parameters

Putting track togethers - Ordering tracks in plot
========================================================

The order of tracks in the plot is simply defines by the order they are placed in the vector passed to **plotTracks()**



```r
plotTracks(c(genomeAxis,accDT),
           from=134887451,to=134888111,
           chromosome="chr5"
           )
```

![plot of chunk unnamed-chunk-25](VizGenomicsData-figure/unnamed-chunk-25-1.png)

Putting track togethers - Controling height of tracks in plot
========================================================

By default, Gviz will try and provide sensible track heights for your plots to best display your data.

The track height can be controlled by provided a vector of relative heights to the **sizes** paramter of the **plotTracks()** function.

If we want the axis to be 50% of the height of the Data track we specify the size for axis as 0.5 and that of data as 1.
The order of sizes must match the order of objects they relate to.



```r
plotTracks(c(genomeAxis,accDT),
           from=134887451,to=134888111,
           chromosome="chr5",
           sizes=c(0.5,1)
           )
```

![plot of chunk unnamed-chunk-26](VizGenomicsData-figure/unnamed-chunk-26-1.png)


Exercises
========================================================



Adding annotation to plots.
========================================================

Genomic annotation, such as Gene/Transcript models, play important part of visualisation of genomics data in context.

Gviz provides many routes for constructing Genomic Annotation using the AnnotationTrack() constructor function.

In contrast to the DataTracks AnnotationTracks allow specification for feature groups.

First lets create a GRanges object with some more regions


```r
toGroup <- GRanges(seqnames="chr5",
        IRanges(
          start=c(10,500,550,2000,2500),
          end=c(300,800,850,2300,2800)
        ))
names(toGroup) <- seq(1,5)

toGroup
```

```
GRanges object with 5 ranges and 0 metadata columns:
    seqnames       ranges strand
       <Rle>    <IRanges>  <Rle>
  1     chr5 [  10,  300]      *
  2     chr5 [ 500,  800]      *
  3     chr5 [ 550,  850]      *
  4     chr5 [2000, 2300]      *
  5     chr5 [2500, 2800]      *
  -------
  seqinfo: 1 sequence from an unspecified genome; no seqlengths
```

Adding annotation to plots. Grouping (part-1)
========================================================

Now we can create the AnnotationTrack object using the constructor.

Here we also provide a grouping to the **group** parameter in the AnnotationTrack function.


```r
annoT <- AnnotationTrack(toGroup,
                group = c("Ann1",
                          "Ann1",
                          "Ann2",
                          "Ann3",
                          "Ann3"))

plotTracks(annoT)
```

![plot of chunk unnamed-chunk-28](VizGenomicsData-figure/unnamed-chunk-28-1.png)


Adding annotation to plots.
========================================================

We can see we have got the samples grouped by lines.

But if we want to see the names we must specify the group parameter used using the **groupAnnotation** argument.


```r
plotTracks(annoT,groupAnnotation = "group")
```

![plot of chunk unnamed-chunk-29](VizGenomicsData-figure/unnamed-chunk-29-1.png)

Adding annotation to plots. Grouping (part-2)
========================================================

We can see we have got the samples grouped by lines.

But if we want to see the names we must specify the group parameter used using the **groupAnnotation** argument.


```r
plotTracks(annoT,groupAnnotation = "group")
```

![plot of chunk unnamed-chunk-30](VizGenomicsData-figure/unnamed-chunk-30-1.png)

Adding annotation to plots. Strands and direction.
========================================================

When we created the GRanges used here we did not specify any strand information.

```r
strand(toGroup)
```

```
factor-Rle of length 5 with 1 run
  Lengths: 5
  Values : *
Levels(3): + - *
```
When plotted annotation without strand is plotted as a box seen in previous slides

Adding annotation to plots. Strands and direction (part-2).
========================================================

Now we specify some strand information for the GRanges and replot.

Arrows now indicate the strand which the features are on.


```r
strand(toGroup) <- c("+","+","*","-","-")
annoT <- AnnotationTrack(toGroup,
                group = c("Ann1",
                          "Ann1",
                          "Ann2",
                          "Ann3",
                          "Ann3"))

plotTracks(annoT, groupingAnnotation="group")
```

![plot of chunk unnamed-chunk-32](VizGenomicsData-figure/unnamed-chunk-32-1.png)

Adding annotation to plots. Controlling the display density
========================================================

In the IGV course we saw how you could control the display density of certain tracks. 

Annotation tracks are often stored in files as the general feature format (see our previous course). 

IGV allows us to control the density of these tracks in the view options by setting to "collapsed", "expanded" or "squished".

Whereas "squished" and "expands" maintains much of the information within the tracks, "collapsed" collapases overlapping features in a single displayed feature.




Adding annotation to plots. Controlling the display density (part 2)
========================================================

In Gviz we have the same control over the display density of our annotation tracks.

By default the tracks are stacked using the "squish" option to make best use of the available space.




```r
plotTracks(annoT, groupingAnnotation="group",stacking="squish")
```

![plot of chunk unnamed-chunk-35](VizGenomicsData-figure/unnamed-chunk-35-1.png)


Adding annotation to plots. Controlling the display density (part 3)
========================================================




```r
plotTracks(annoT, groupingAnnotation="group",stacking="dense")
```

![plot of chunk unnamed-chunk-36](VizGenomicsData-figure/unnamed-chunk-36-1.png)



Adding annotation to plots. Feature types.
========================================================

AnnotationTracks may also hold information on feature types.

For GeneModels we may be use to feature types such as mRNA, rRNA, snoRNA etc.

Here in we can make use of feature types as well.

We can display any feature types within our data using the features() function. Here they are unset so displayed as unknown.



```r
feature(annoT)
```

```
[1] "unknown" "unknown" "unknown" "unknown" "unknown" "unknown"
```

Adding annotation to plots. Setting feature types.
========================================================

We can set our own feature types for the AnnotationTrack object using the same **feature()** function.

We can choose any feature types we wish to define.


```r
feature(annoT) <- c(rep("Good",4),rep("Bad",2))
feature(annoT)
```

```
[1] "Good" "Good" "Good" "Good" "Bad"  "Bad" 
```

Adding annotation to plots. Display feature types.
========================================================

Now we have defined our feature types we can use this information within our plots.

In GViz, we can directly specify the colours for the individual feature types within our AnnotationTrack.

Here we specify the "Good" features as blue and the "Bad" features as red.


```r
plotTracks(annoT, featureAnnotation = "feature",
           groupAnnotation = "group",
           Good="Blue",Bad="Red")
```

![plot of chunk unnamed-chunk-39](VizGenomicsData-figure/unnamed-chunk-39-1.png)


GeneModelTrack
========================================================





AlignmentTracks
========================================================




Bringing in External data.
========================================================




Styling plots.
========================================================



