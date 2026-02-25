# Elastic Transcoder and AWS Elemental MediaConvert

- MediaConvert a new AWS product replacing Elastic Transcoder
- MediaConvert is a superset of features provided by Transcoder
- Both of the systems are file based video transcoding systems
- Serverless - pay for resources used
- For both we add jobs to pipelines (ET) or queues (MC)
- Files are loaded from S3, processed and stored back to S3
- MC supports EventBridge for job signalling => processes in MC can generate an event in an EventBridge bus
- ET/MC architecture example:

![ET/MC architecture](images/ElasticTranscoder&MediaConvert.png)

- We use these products when we need to convert media in a serverless, event-driven media processing pipeline

## Choosing between ET and MC

- ET is legacy, by default we should chose MC
- MC supports more codecs, design for larger volume and parallel processing
- MC supports reserved pricing
