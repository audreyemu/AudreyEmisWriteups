# Directory (web) from Angstrom CTF 2023

We look at the webpage and see that access to each directory has the following format:
“https://directory.web.actf.co/<number>.html” with <number> being a number between 1 and 5000

I created a web scraper in python to traverse through all these websites quicker than I could click on each one

```
import requests

for x in range(5000):
  link = 'https://directory.web.actf.co/'
  link += str(x)
  link += '.html'
  
  r = requests.get(link)
  if('{' in r.text):
    print(r.text)
```

I check if the text has a bracket, because that indicates that the website holds the flag, so I print it out
