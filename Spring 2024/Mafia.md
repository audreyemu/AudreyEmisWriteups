# Mafia (web) from xss.pwnfunction.com

Our goal is to pop an alert(1337) on this page, by using xss. We are given the following html code within the webpage.

```
/* Challenge */
mafia = (new URL(location).searchParams.get('mafia') || '1+1')
mafia = mafia.slice(0, 50)
mafia = mafia.replace(/[\`\'\"\+\-\!\\\[\]]/gi, '_')
mafia = mafia.replace(/alert/g, '_')
eval(mafia)
```

We see that our input must be 50 characters or under, cannot contain `'"+-!\[] (will get turned in to underscores), and cannot contain the word "alert" (will get turned into underscores also). 

I learned about hash in a URL. This hash is not sent to the server, but we can still access it after the page is loaded. If we have www.exampleurl.com#hash and we run the command location.hash in the console, '#hash' will be returned

We can use this by evaluating the hash (we must use slice(1) to extract the hash that we want (the javascript slice function can take in two parameters -- one for start, one for end -- )

Our final url is
```
https://sandbox.pwnfunction.com/warmups/mafia.html?mafia=eval(location.hash.slice(0))#alert(1337)
```
