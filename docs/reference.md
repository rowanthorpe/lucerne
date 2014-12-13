---
title: Reference
layout: docs
---

# Applications

## `<app>`

The `<app>` class is the basis of all Lucerne applications. This holds the route
mapping of a particular app.

## `not-found`

The `not-found` method describes what happens when the router can't match a
request to a view. By default, it simply returns "Not found" (`text/plain`) and
the 404 error code.

You can override this method to respond with a custom error page or redirect the
user to the homepage, for example.

## `defapp`

The primary way to define an application is with the `defapp` macro. The first
argument is the name of the application.

Examples:

~~~lisp
(defapp myapp)
~~~

# Views

# Starting and Stopping

# Responding and Redirecting

## Responding

The `respond` function is the easiest way to compose a Clack response. The first
argument is the body of the response.

Two optional keyword arguments, `:type` and `:status`, can be used to specify the
content type and status code. By default, the content type is
"text/html;charset=utf-8" and the status code 200.

Examples:

~~~lisp
@route app "/"
(defview index ()
  (respond "Hello, world!"))

;; Overriding the not-found method
(defmethod not-found ((app <app>))
  (respond (my-404-template) :status 404))
~~~

## Redirecting

The `redirect` function can be used to redirect the client to another URL. A
keyword argument, `:status`, controls the status code of the response, which by
default is 302.

Examples:

~~~lisp
@route app "/user/:name"
(defview profile-redirect (username)
  ;; Redirect users away from the legacy URL
  (redirect (concatenate 'string "/@" username)))
~~~

# Sessions and Parameters

## Session Data

Session data in a request can be accessed with the `session` macro. Inside a view, `(session req)` will return the session hash table.

The `lucerne-auth` contrib is an example of storing data in the session hash:

~~~lisp
(defun get-session (req)
  (getf (env req) :clack.session))

(defun get-userid (req)
  (gethash :userid (get-session req)))

(defun login (req userid)
  (setf (gethash :userid (get-session req))
        userid))

(defun logout (req)
  (remhash :userid (get-session req)))
~~~

## Accessing form Parameters

The `with-param` macro allows you to extract parameters from a request and use
them within its body.

~~~lisp
(with-params req (name username password password-repeat)
  (if (equal password password-repeat)
      ...))
~~~

Symbols are converted to strings and downcased.

# Templates

By default, Lucerne uses [Eco][eco] for its template engine. Eco is similar in
design and syntax to [eRuby][eruby], and its templates compile to plain Common
Lisp functions. The goal is to be simple and fast.

[eco]: https://github.com/eudoxia0/eco
[eruby]: https://en.wikipedia.org/wiki/ERuby
