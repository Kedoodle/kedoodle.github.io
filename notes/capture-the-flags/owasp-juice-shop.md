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