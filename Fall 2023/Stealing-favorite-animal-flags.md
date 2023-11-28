# Stealing Favorite Animal Flags (web) from Cyber Academy Fall 2023

We are presented with a website and source code. By looking in the source code, we see that anything submitted into the username query can be executed. Given the wording of this challenge, I thought LFI would be the answer. However, we are unable to have a username containing '.' or '/' which meant I would have to specify the username in a different way. We are able to specify the username query in the URL. 

```
https://favorite-animal.acmcyber.com/?username=../../../../../../flag.txt
```

The URL ends up returning the flag, which is cyber{ch3ck_y0ur_f1le_pa7h2}
