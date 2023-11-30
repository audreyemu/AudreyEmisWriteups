# Stealing Favorite Animal Flags (web) from Cyber Academy Fall 2023

We are presented with a website and source code. By looking at index.jade in the source code, we see that anything submitted into the username query can be executed. 

```
extends layout

block content
  //- I wonder what the difference between !{} and #{} is in pugjs...
  h1 Hello !{user}!
  p It is now !{time}
  p Your favorite animal is !{animal}
```

In index.js, we see how the program gets the username and we find out that we can use the ?username query parameter to specify the username.

```
/* GET home page. */
router.get('/', function(req, res, next) {
  // Get the username using the ?username query parameter
  let user = req.query.username
  if (user === undefined) {
    // If query parameter doesn't exist, look for it in the cookies
    user = req.cookies["username"]
    if (user === undefined) {
      // if still not found, have the user log in
      return res.redirect("/login");
    }
  }
  // Try and find the user's data file using the format db/USERNAME
  fs.readFile(`db/${user}`, function(err, data) {
    res.setHeader("Content-Security-Policy", "default-src * 'unsafe-inline' 'unsafe-eval';") // Who needs security >:( let the <script> tag be free!
    return res.render('index', { user: user, time: Date().toString(), animal: data, title: `Hello, ${user}!` });
  });
});
```

I thought LFI would be the answer to this challenge, but it's more complicated because we are unable to have a username containing '.' or '/'. 

```
if (username.includes(".") || username.includes("/")) {
    res.status(400);
    res.send("Invalid username");
    return;
```

This meant I would have to specify the username by setting the username query in the URL. 

```
https://favorite-animal.acmcyber.com/?username=../../../../../../flag.txt
```

The URL ends up returning the flag, which is cyber{ch3ck_y0ur_f1le_pa7h2}
