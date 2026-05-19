---
tags: [basics, tool, scanning]
---



This tool can be used for some vulnearability scanning. It has slowly become one of the standard tools to use. 

```bash

# Nuclei example which targets multiple pages, has 100 parallel routines, bulk size of 50, a rate limit req/s set to 500 and a timeout of 5 seconds. No retries and the output gets formated as json and piped into the results.json file
nuclei -target http://dev.devvortex.htb,http://devvortex.htb -c 100 -bs 50 -rl 500 -timeout 5 --retries 0 -jsonl -o results.json



```