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


# Upload Type :star::star::star:

Looking at the HTTP requests when submitting a complaint (with a file attached) reveals a `POST` request to upload the file:
```http
POST /file-upload HTTP/1.1
```

I crafted a request in `Postman` with a `file` key & `.png` file value pair in the request body.