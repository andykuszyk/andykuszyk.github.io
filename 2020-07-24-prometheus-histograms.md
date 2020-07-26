# Prometheus Histograms. Run that past me again?
I recently found myself in the position where I needed to do some detailed analysis of how long things were taking in a distributed software system. This is a solved problem from the community's point of view with tools like [Prometheus](https://prometheus.io/).

This blog post assumes you are familiar with the different [types of metric](https://prometheus.io/docs/concepts/metric_types/) Prometheus has to offer and are aware of the fact that real-time, dynamic values can be measured using [histograms or summaries](https://prometheus.io/docs/practices/histograms/).

Having read about the difference between [histograms and summaries](https://prometheus.io/docs/practices/histograms/), I found myself thinking, "run that past me again?". In other words, I still had no idea what the difference was and was very much in the dark when it came to making an enlightened choice about how to instrument my application and start querying the results.

This post is a summary (excuse the pun) of what I subsequently learnt about histograms and how they can be used. If you're already a Prometheus black-belt, read no further. If you still don't know what I'm talking about, read on!

## What is a histogram anyway?
Before we talk about histograms in Prometheus, let's re-cap histograms in general. I think it's normal at times like this to quote the [wikipedia definition](https://en.wikipedia.org/wiki/Histogram), but in this case I'm not sure that's helpful.

A histogram is a way of summarising (not in the Prometheus sense!) how some data is distributed - how many of the values were high, how many were low and how many were somewhere in between.

Take a look at this histogram:

<iframe width="600" height="371" seamless frameborder="0" scrolling="no" src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTZRwsKfQVttZ1VCzT9lClNqbuij0v9iuiZUXsVUBsP16n4juzgk2i3UyKvXEBu32Gb-RDZdWPEwe_b/pubchart?oid=1197197263&amp;format=image"></iframe> 

It shows the distribution of a dataset. Some values are low (<=10), some are medium (>10 and <=100) and some are high (>100 and <=1000). The histogram groups the data into buckets based on these ranges and counts how many values are in each bucket. This gives us some insight into how the data is distrubuted across its range of values. When deciding how to draw a histogram, you normally choose bucket ranges that are sensible for the data and meaningful to the analysis.

## What about Prometheus histograms?
Now that we know what a histogram is, let's talk about Prometheus histograms. Prometheus histograms are a little different to the above example in three ways:

1. The buckets are cumulative - that is to say that each bucket contains values less than or equal to the bucket's upper threshold.
2. A Prometheus histogram metric is also a timeseries - the example we say above can be thought of a simple example of a Prometheus histogram at an instant in time. But, Prometheus records these histograms over time so things are a little more complicated when you start writing queries.
3. The time series itself is cumulative - the buckets in the histogram are always increasing so that the most recent instance of the histogram shows the total values for each of the buckets since the metric was first recorded.

Let's focus on each of these differences in turn.

### 1. The buckets are cumulative
In the above example, each bucket was exclusive of the values on either side. The values that were less than or equal to 10 only appeared in the <=10 bucket and not in any of the others.

Prometheus histograms are cumulative. In Prometheus, our example above would have different buckets: <=10, <=100 and <=1000. Let's see how this would look:

<iframe width="600" height="371" seamless frameborder="0" scrolling="no" src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTZRwsKfQVttZ1VCzT9lClNqbuij0v9iuiZUXsVUBsP16n4juzgk2i3UyKvXEBu32Gb-RDZdWPEwe_b/pubchart?oid=830654358&amp;format=image"></iframe>

You can see that now, each bucket is bigger than the one before and contains a sum of all values up until the bucket's threshold.

### 2. Prometheus histograms are timeseries
Prometheus scrapes metrics from a process at intervals. Each time it scrapes a histogram metric, it will receive a histogram similar to the one above - a cumulative histogram with "less than or equal to" buckets.

What's important to understand is that when you're querying the histogram, you're suddenly dealing with a timeseries of histograms. The histogram metric itself contains a range of values--one for each point in time that a scrape occured--and each value represents a histogram like the one above.

Each histogram value--scraped at a scrape interval--summarises the distribution of values recorded by the process since the last scrape.

### 3. The time series itself is cumulative
Each time the histogram is scraped by Prometheus, the values are not reset. This means that the counts in each bucket are cumulative over the lifetime of the metric (at least in the memory of each process) and that it's really the *change* in each bucket's values the tells us the distribution of observations since the last scrape.

## An example
Let's put all of these ideas into practice. The examples below can all be found [here](https://github.com/andykuszyk/prometheus-histogram-example) along with a Docker Compose file for running a sample application, Prometheus and Grafana.

### Example application
Let's start with an example application, written in Go. To begin with, let's configure the application to listen for HTTP requests on port 8080 and handle Prometheus scrapes on the `/metrics` route:

```go
package main

import (
    "log"
    "net/http"
    "github.com/prometheus/client_golang/prometheus/promhttp"
)

func main () {
    http.Handle("/metrics", promhttp.Handler())
    log.Fatal(http.ListenAndServe(":8080", nil))
}
```

Now, let's create a histogram metric with some predefined buckets:

```go
package main

import (
    "log"
    "net/http"
    "github.com/prometheus/client_golang/prometheus"
    "github.com/prometheus/client_golang/prometheus/promhttp"
    "github.com/prometheus/client_golang/prometheus/promauto"
)

func main () {
    histogram := promauto.NewHistogram(prometheus.HistogramOpts{
        Name:    "histogram_metric",
        Buckets: []float64{1.0, 2.0, 3.0, 4.0, 5.0},
    })
    http.Handle("/metrics", promhttp.Handler())
    log.Fatal(http.ListenAndServe(":8080", nil))
}
```

Finally, let's run a function in the background to record values (or observations) in the histogram:

```go
func main () {
    histogram := promauto.NewHistogram(prometheus.HistogramOpts{
        Name:    "histogram_metric",
        Buckets: []float64{1.0, 2.0, 3.0, 4.0, 5.0},
    })
    go func() {
        for {
            histogram.Observe(rand.Float32() * 5.0)
            time.Sleep(1 * time.Second)
        }
    }()
    http.Handle("/metrics", promhttp.Handler())
    log.Fatal(http.ListenAndServe(":8080", nil))
}
```

See [here](https://github.com/andykuszyk/prometheus-histogram-example/blob/master/application/main.go) for the full file, but note that all we're doing in this application is observing a random number between 0 and 5 once every second. The random number is a float, so each value will likely be different. We're expecting the histogram to count the observations that fall into each of the buckets, which are seperated by a value of 1.

> Bucket thresholds are floats too, but in this example I've chosen integers to try to make things simpler.

