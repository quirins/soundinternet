# Sound Engineering and Analysis of Internet Measurements



## Thoroughly think through Ethical Implications



cite durumeric, allman, et al. 

- get IRB approval, or at least discuss with a capable colleague.
- maintain a blacklist + a whitelist
- control rate
- rDNS + website



## Engineering



### Understand your bias



What is the population you are measuring? Does it have a bias? How can you control it?

E.g., Alexa, Umbrella, certain domain lists such as .nl

### Longitudinal

One-off measurements may be significantly off due to a plethora of effects, such as congestion, packet loss, BGP or Routing events. Only the periodic repetition of measurements grants a minimal level of repeatbility for measurement campaigns. 

Studies should be run at least several weeks, at different times of day, to counter various effects: diurnal effects, public holidays, school holidays are just few of the effects that can distort measurement results. 



### Make them robust

When running repeated measurement campaigns, a key question is whether you daily take a new sample, freeze the sample at the beginning of the study, or do a mix. The answer to this question also depends on the sampling method: A random sample of .com domains offers no compelling reason to resample every day, while sampling based on popularity does offer such reasons. 

If re-sampling and with measurement budget permitting, a high priority should be given to also include old samples: A continuous measurement of a domain over a month can be a valuable data point.



### Keep data in mind and compress from the beginning

Continuous measurements have a somewhat predictable requirement of storage space over time. 

We recommend to minimize that storage by deploying compression from the very beginning, i.e., have measurement raw data directly written into compressed formats, and develop analysis tools with the capability to work on compressed raw data. 

We also recommend to allocate enough storage budget, i..e, for a minimum of 1 year of measurements.



### Use expressive naming with UTC timestamps



Name your files expressively (consider naming a cheap way of storing meta data) and always use UTC timestamps in the file name.



### Store inputs



Measurement input always should be stored specifically. 

Later analysis will critically rely on knowing which version of the blacklist, the input file, and the scanner configuration has been used. 



### Automatically update your scanning environment

Make it a habit for your scanning tool to update itself and its parameters (blacklist, …) using a version control system before each run. This provides continuous integration and the fear of never touching the ancient measurement system. 

### Monitor effectively

Effective monitoring is key to maintaining longitudinal measurements. 

We have observed multiple measurement campaigns fail beyond recovery due to underestimation of this aspect, at times resulting in Terabytes of useless measurement data spanning over a year of measurements, during which the output was never inspected or monitored.

There are three very effective means to avoid such a situation:

1) Conduct automated measurement results provessing directly after each measurement, and have 2-3 key metrics (such as the count of successfuly TLS-scanned hosts) e-mailed to you. These e-mails must be brief, clutterless, and permit to assess beforementioned 2-3 metrics within 1 second (ideally from the preview window). After 1-2 weeks, these key metrics should be known to you by heart and comparison come flawless. It does not work to receive exhaustivingly long e-mails. Never have such e-mails automatically sorted into some folder, this will lead to not being read.

2) Very specifically watch error cases you have encountered before. Typical error cases may be "too many open files" or "address already in use", when running out of sockets. Post-processing of scans should assert the absence of any such errors.

3) For mature measurement campaigns, a website can be set up that continuously plots measurement results. This will make any dramatic malfunction quickly obvious, but may hint to more subtle errors only very vaguely.

## Analysis



### Raw data is sacrosanct (cf. Matthew Roughan)



All your analysis must be anchored into the raw data produced by scanning tools. 

That raw data must never be altered, no columns dropped, no rows removed. 

Your analysis scripts should do the following things to be efficient and strongly reproducible:

1) have a version string, and meticulously encode it in resulting outputs

2) read a checksum of input data, and store it with results as meta-data

These two simple steps can greatly enhance caching effects: If the same version of the analysis script is applied to the same input data, the script run can be skipped. 

This can used to build a meticulously documented, yet very efficient end-to-end analysis platform. 



### Best practice in setting up a continuous measurement campaign with a live website



When running measurements every day, the pipeline mentioned above can be used to process all results. 

Already processed results will simply be skipped. 

We have good experience in extracting key metrics from raw data and storing them to both in long format (e.g., the list of domains that have certain properties) and in short format (e.g., the count of domains that have certain properties) as JSON files. 

Using highcharts on github, automatied deployment of updated JSON files can provide a stable hosting environment outside the university. 



engineering good internet messurements

\- make them longitudinal (for repeatability)

\- make them robust (use yesterdays file)

\- dont drop domains, always include yesterdays data

\- compress right from the beginning

\- use expressive, UTC naming

\- make consumption of 2-3 data points per scan a habit (in addition or instead of monitoring)

\- pull blacklist and measurement code from git

\- store your inputs

\- watch typical error cases and set boundaries (too many open files / name resolution error / scan not finished in time)

Live website:

​	* do measurements every day

​	* evaluate measurements every day, store results in JSON, cache if raw file processed

​	* convert JSONs to highcharts json