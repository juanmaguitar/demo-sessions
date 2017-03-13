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

So, to add items to the cart...
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

And to check the content of the cart...
```
$ http --session=my localhost:4000/cart
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
