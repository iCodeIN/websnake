# Websnake

Asynchronous http/https requests in Python.

It is possible to fire multiple http/https requests asynchronously with Websnake. 

# Features

- **Http/Https**

- **Basic Authentication support**

- **Token Authentication support**

- **Automatic Content Decoding**

- **Anti Throttle Mechanism**
    
- **Response Size Limit**

- **Non-blocking I/O**

In Websnake HTTP response's codes turn into events, in this way it is possible to keep track of what is going
on with your request in an simple manner. The fact of being capable of mapping a handle to a specific
HTTP response it makes applications on top of Websnake more modular.

### Multiple Requests

The example below shows how to peform multiple requests. When all requests are finished
it calls handle_empty handle.

~~~python
from websnake import Get, ResponseHandle, core, RequestPool, die

def handle_empty(pool):
    print('All requests done!')

    for ind in pool.responses:
        print('Code:', ind.code)
    die()

if __name__ == '__main__':
    urls = ('https://en.wikipedia.org/wiki/Leonhard_Euler', 
    'https://www.google.com.br/','https://facebook.com/') 

    pool = RequestPool()
    pool.add_map(RequestPool.EMPTY, handle_empty)

    for ind in urls:
        request = Get(ind, pool=pool)
    core.gear.mainloop()
~~~

That would output:

~~~
All requests done!
Code: 200
Code: 200
Code: 200
~~~

### Basic GET 

The following example just makes a request and wait for the response to be printed.

~~~python
from websnake import Get, ResponseHandle, core, die

def handle_done(request, response):
    print('Headers:', response.headers)
    print('Code:', response.code)
    print('Version:', response.version)
    print('Reason:', response.reason) 
    print('Data:', response.content())
    die('Request done.')

if __name__ == '__main__':
    request = Get('https://www.google.com.br/')
    
    request.add_map(ResponseHandle.DONE, handle_done)
    core.gear.mainloop()
~~~

Websnake requests are event emitters, you can bind a status code like '400' to a handle
then getting the handle executed when the response status code is '400'.

In the above example it could be done.

~~~
    request.add_map('400', handle_done)
~~~

or 

~~~
    request.add_map('200', handle_done)
~~~

### Basic POST 

The example below creates a simple gist on github.

~~~python
from websnake import Post, ResponseHandle, core, die, JSon, TokenAuth

def handle_done(con, response):
    print('Headers:', response.headers.headers)
    print('Code:', response.code)
    print('Version:', response.version)
    print('Reason:', response.reason) 
    print('Data:', response.content())
    die()

if __name__ == '__main__':
    data = {
    "description": "the description for this gist1",
    "public": True, "files": {
    "file1.txt": {"content": "String file contents"}}}

    request = Post('https://api.github.com/gists', args = {'scope': 'gist'},
    payload=JSon(data), auth = TokenAuth('API_TOKEN'))

    request.add_map(ResponseHandle.DONE, handle_done)
    core.gear.mainloop()
~~~

# install

**Note:** Websnake should work with python3 only.

~~~
pip install -r requirements.txt
pip install websnake
~~~
