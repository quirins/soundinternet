# Sound Engineering and Analysis of Internet Measurements

This document aims to provide a go-to collection of best practices, tips and tricks, DO's and DONT's for anyone engaging in Internet measurement. Some of this may seem obvious to people experienced in Internet measurements, but other people have repeatedly asked for advice such as this. 

Comments, changes, and additions very welcome! 

There is a body of highly recommended related work: 

- The PAM'17 keynote by Matthew Roughan on the "10 commandments of Internet measurements", of which many points overlap with this collection [1]
- Important insights for scanning, ethical and operational guidelines [2]
- Standard works in ethical guidelines for Internet scanning/Computer science [3-6]



## Thoroughly think through Ethical Implications

Four point seem utterly important, which we repeat from the related work above. Internet measurement studies should:

- get IRB approval, or, where no IRB exists, at least discuss measurement campaigns with capable colleagues
- maintain a blacklist and a whitelist (a whitelist can avoid sending packets into unrouted space)
- control sending rate to a sane minimum, e.g. few thousand packets per second
- set up rDNS pointers and a website on the measurement machines 



## Engineering

The next few point describe typical caveats with regards to the *engineering*, i.e., setup and operation of Internet measurements. 

### Understanding Bias of the Measured Population Sample

Typically, it is only possible to measure a sample of the Internet, largely because the entire Interent is not known. This leads to measuring samples of the Internet, which may be constructed with significant bias [7].

It is important to thoughtfully consider this bias, think of ways to control it, or at least acknowledge that it exists and of what nature it likely is. 

### Conducting Longitudinal Measurements for Repeatability

One-off measurements may suffer from significant influences due to a plethora of effects, such as congestion, packet loss, BGP or Routing events. Only the periodic repetition of measurements grants a minimal level of repeatbility for measurement campaigns. 

Studies should be run at least several weeks, at different times of day, to counter various effects: diurnal effects, public holidays, school holidays are just few of the effects that can distort measurement results. 

### Using Robust Measurement Target Lists

When running repeated measurement campaigns, a key question is whether measurement targets should be sampled ever day, frozen at the beginning of the study, or chosen by a mix of the two combinations. 

The answer to this question also depends on the sampling method: A random sample of .com domains offers no compelling reason to resample every day, while sampling based on popularity (e.g., when using Top Lists) does offer such reasons. 

If the measurement campaign desires re-sampling, and with measurement budget permitting, a high priority should be given to also include old samples. This means that every measurement also contains all previous measurement targets. This makes the target list robust to fluctuations and provides more robust data points. It obviously comes with the downside of target lists growing over time. 

### Effective Storage Management and Compression of Raw Data from the Start 

Continuous measurements have a somewhat predictable requirement of storage space over time. We  recommend to allocate a storage budget sufficient for at least 1 year of measurements. 

We recommend to minimize that storage need by deploying compression from the very start, i.e., have measurement raw data directly written into compressed files formats, and develop analysis tools with the capability to work on compressed raw data. 

### Expressive Naming and Use of UTC Timestamps in Raw Data

Name your files expressively (consider naming a cheap way of storing meta data) and always use UTC timestamps in the file name. An expressive name should contain the name of the target list (e.g., alexa), the version of the scanning tool, and the measurement start time in UTC. 

If your scanning tool can write meta data separately, e.g., to a json file, this is even better. 

### Input Storage 

Often, measurement targets are taken from rotating lists, after applying rotating filters (such as blacklists). We highly recommend to store the final list of targets being scanned to a specific file. Later reconstruction of target lists is tedious and error-prone. 

### Continuous Integration

Scanning tools and environment should be continually and automatically updated before each run. This provides continuous integration, early detection of errors, and minimizes the fear of conducting changes in the measurement system. 

### Effective Monitoring

Effective monitoring is key to maintaining longitudinal measurements. 

We have observed multiple measurement campaigns, in parts running for over a year before notice, fail beyond recovery due to underestimation of monitoring. 

There are three very effective means to avoid such a situation:

1. **Automatic Processing:** After each measurement run, results should be automatically processed, and  2-3 key metrics should be sent to **one** person in charge. These e-mails must be brief, clutterless, and permit to assess beforementioned 2-3 metrics within 1 second (ideally from the preview window). After 1-2 weeks, these key metrics should be known to the person in charge by heart and comparison come effortless. At all costs, it should be avoided to receive exhaustivingly long e-mails, or sort these e-mails automatically into a subfolder. Both **will** lead to not reading those e-mails. Metrics could be, e.g., the number of successfully scanned TLS hosts for TLS scans, or the number of domains returning A/AAAA records for DNS scans.
2. **Error Case Monitoring:** Specific error cases should be very specifically warched. These error cases could, e.g., be "too many open files" when hitting `ulimit` [8] or "address already in use", when running out of sockets. Post-processing of scans should assert the absence of any such errors.
3. **Dashboard:** An ongoing graphical representation of measurement results can amend monitoring. This can, for example, be a website with live charts that are frequently being updated. Dashboard make any dramatic malfunction quickly obvious, but do not permit to see more subtle errors (such as a few "too many open files"), hence they should not be fully relied on. 

## Analysis

The following points aim to give hints on how to run sound analysis on Internet measurements. 

### Raw data is sacrosanct (cf. Matthew Roughan [1])

All your analysis must be anchored into the raw data produced by scanning tools. That raw data must never be altered, no columns dropped, no rows removed. 

### From Prototype to Stable Version

Before breakdown to precise metrics, Internet measurement typically starts out with exploratory data analysis, i.e., "playing with the data", which later leads to the definition of precise metrics. 

Hence, a good practice is to do first analysis in an exploratory and interactive environment, such as Jupyter Notebook. 

Once a certain metric or feature is considered stable, Jupyter Notebooks can be converted to standalone scripts with few simple adaptations. 

### Maintaining Reproducibility Despite Evolving Analysis

Analysis evolves over time: Metrics are being added, and existing metrics maybe carefully modified. To maintain reproducibility and efficiency along these evolutions, analysis scripts should do the following things to be both efficient and strongly reproducible:

1) Scripts should have a version string, and meticulously encode it in resulting outputs

2) Scripts should read a checksum of input data, and always store it with results as meta-data

These two simple steps can greatly enhance caching effects: If the same version of the analysis script is applied to the same input data, the script run can be skipped. 

This can be used to build a meticulously documented, yet very efficient end-to-end analysis platform. 

For a checksum, a cryptographic checksum such as SHA512 is desirable, but in practice the precise file size in bytes is typically also a strong indicator that the file has not been overwritten by new measurements, truncated, or other accidental major changes have happened.

### Setting up a Dashboard

When running measurements every day, the pipeline mentioned above can be used to process all results, as already processed results will simply be skipped. 

We have good experience in extracting key metrics from raw data and storing them to both in long format (e.g., the list of domains that have certain properties) and in short format (e.g., the count of domains that have certain properties) as JSON files. 

Using HighCharts and hosting on github.io, automated deployment of updated JSON files can provide a stable and independent hosting environment. 



# Interesting Links, Tools, and Resources

Live websites:

https://caastudy.github.io

https://toplists.github.io

https://http2.netray.io/stats.html

# References

[1] The 10 Commandments of Internet Measurement, [Passive and Active Measurements Keynote](https://research.csiro.au/pam2017/), Sydney, Australia, March 20th, 2017 [PDF](http://www.maths.adelaide.edu.au/matthew.roughan/talks/pam_2017.pdf)

[2] Durumeric, Zakir, Eric Wustrow, and J. Alex Halderman. "ZMap: Fast Internet-wide Scanning and Its Security Applications." *USENIX Security Symposium*, 2013.

[3] Bailey, M., Dittrich, D., Kenneally, E., & Maughan, D. "The Menlo Report." *IEEE Security & Privacy* , 2012

[4] Vern Paxson. Strategies for Sound Internet Measurement. IMC 2004

[5] Mark Allman. On Changing the Culture of Empirical Internet Assessment. ACM SIGCOMM CCR, 2013

[6] Mark Allman, Robert Beverly, and Brian Trammell. Principles for measurability in protocol design. ACM SIGCOMM CCR, 2017

[7] Scheitle, Quirin, et al. "A Long Way to the Top: Significance, Structure, and Stability of Internet Top Lists." *arXiv preprint arXiv:1805.11506* (2018).

[8] Even better, have your measurement script assert that ulimit is set sufficiently high before starting the measurements.