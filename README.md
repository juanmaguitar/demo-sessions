# Demo sessions 

This repo contains a demo to show how sessions work in express

It uses `session-file-store` to persist the session data (server) into a file. With this implementation we can:
- review the content of this session data managed by the server 
- reload the server w/ `nodemon` without problems as the session data is persisted in a file, and will be available in different processes

## Running the app

To run the app you can just do `npm run dev`

## Testing the app

We have two endpoints:

- `[POST]` `/api/cart` → Add items to cart
- `[GET]` `/api/cart` → Show content of the cart

### With `http`

To add items to the cart...
```
$ http --session=my --form POST localhost:4000/api/cart product='macbook' quantity=5
HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 13
Content-Type: application/json; charset=utf-8
Date: Mon, 13 Mar 2017 21:12:25 GMT
ETag: W/"d-LKL24uBkvpQmtBXYEcv4Y7bwHp8"
X-Powered-By: Express
set-cookie: jm-server-session-cookie-id=s%3A-H13v7AzBurlDdYPVEJPnjaRjUBJaXVI.gmXqHuAUCf4JujW5eSydpKxOPLXQvrSrmWsWmt7eOpk; Path=/; HttpOnly

{
    "macbook": 5
}

$ http --session=my --form POST localhost:4000/api/cart product='macbook' quantity=5
HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 14
Content-Type: application/json; charset=utf-8
Date: Mon, 13 Mar 2017 21:12:51 GMT
ETag: W/"e-77i/S6awzf3I22Muyy2wYJBt3wk"
X-Powered-By: Express

{
    "macbook": 10
}
```

To check the content of the cart...
```
$ http --session=my localhost:4000/api/cart
HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 14
Content-Type: application/json; charset=utf-8
Date: Mon, 13 Mar 2017 21:13:32 GMT
ETag: W/"e-77i/S6awzf3I22Muyy2wYJBt3wk"
X-Powered-By: Express

{
    "macbook": 10
}
```

### With `curl`

To add items to the cart...
```
$ curl --cookie-jar mycookie --data "product=macbook&quantity=5" localhost:4000/api/cart
{"macbook":5}

$ curl --cookie mycookie --data "product=macbook&quantity=5" localhost:4000/api/cart
{"macbook":10}

$ curl --cookie mycookie --data "product=macbook&quantity=5" localhost:4000/api/cart
{"macbook":15}
```

To check the content of the cart...

```
$ curl --cookie mycookie localhost:4000/api/cart
{"macbook":15}
```

If we check the file stored in the folder `sessions` we can see...

```
{
  "cookie": {
    "originalMaxAge": null,
    "expires": null,
    "httpOnly": true,
    "path": "/"
  },
  "cart": {
    "macbook": 15
  },
  "__lastAccess": 1489440572520
}
```

If we don't specify any cookie we cannot access the session info

```
$ curl localhost:4000/api/cart
```

(nothing is returned)

### From browser

#### Step 1: First GET to `/cart`

Request Headers
```
GET /cart HTTP/1.1
Host: localhost:4000
...
```

**Response** Headers
```
HTTP/1.1 200 OK
...
set-cookie: jm-server-session-cookie-id=s%3ASYQXdeouoy6XWukqgE8hri3C2G1UC3n5.11TB3gofVIHTUdEq5ic1SRcUyZT7nUVLQkY%2Bh9gYMr8; Path=/; HttpOnly
...
```

Content of file in `sessions` folder
```
{
  "cookie": {
    "originalMaxAge": null,
    "expires": null,
    "httpOnly": true,
    "path": "/"
  },
  "cart": {
    "_locals": {}
  },
  "__lastAccess": 1489443467063
}
```

#### Step 2: POST to `/cart`

**Request** Headers
```
POST /api/cart HTTP/1.1
Host: localhost:4000
...
Cookie: jm-server-session-cookie-id=s%3AlVOfNnRhYjirh2_jHmmdEaD2S6V0L7Sq.ph8qE2RNYHuDi3QtJJh8e5%2BEIxFFa1on4sSlIxxDOmM
...
```

Form Data
```
product:coke
quantity:8
```