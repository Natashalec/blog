---
layout: post
title: "Fluent Interfaces Are Bad for Maintainability"
date: 2018-02-28
place: Moscow, Russia
tags: java oop
description: |
  Fluent interface is a very popular and convenient
  design pattern, which, however, makes objects larger
  and less maintainable.
keywords:
  - fluent interface
  - fluent interface java
  - fluent interface is evil
  - fluent interfaces are evil
  - oop fluent interface
---

[Fluent interface](https://en.wikipedia.org/wiki/Fluent_interface),
first [coined](https://martinfowler.com/bliki/FluentInterface.html)
as a term by Martin Fowler, is a very convenient way of communicating
with objects in OOP. It makes their facades easier to use and understand.
However, it ruins their internal design, making them more difficult
to maintain. A few words were said about that by Marco Pivetta
in his blog post [Fluent Interfaces are Evil](https://ocramius.github.io/blog/fluent-interfaces-are-evil/);
Now I will add my few cents.

<!--more-->

Let's take my own library [jcabi-http](http://http.jcabi.com/),
which I created a few years ago,
when I thought that fluent interfaces were a good thing. Here is how you
use the library to make an HTTP request and validate it's output:

{% highlight java %}
String html = new JdkRequest("https://www.google.com")
  .method("GET")
  .fetch()
  .as(RestResponse.class)
  .assertStatus(200)
  .body();
{% endhighlight %}

This convenient [method chaining](https://en.wikipedia.org/wiki/Method_chaining)
makes the code short and obvious, right?
Yes, it does, on the surface. But the _internal_ design of library's
classes, including [`JdkRequest`](http://static.javadoc.io/com.jcabi/jcabi-http/1.17.2/com/jcabi/http/request/JdkRequest.html),
the one you see,
is very far from being elegant. The biggest problem is that
they are rather big and it's <del>difficult</del> impossible
to extend them without making even bigger.

For example, now [`JdkRequest`](http://static.javadoc.io/com.jcabi/jcabi-http/1.17.2/com/jcabi/http/request/JdkRequest.html)
has methods
[`method()`](http://static.javadoc.io/com.jcabi/jcabi-http/1.17.2/com/jcabi/http/Request.html#method%28java.lang.String%29),
[`fetch()`](http://static.javadoc.io/com.jcabi/jcabi-http/1.17.2/com/jcabi/http/Request.html#fetch%28%29),
and a few others. What happens when a new functionality is required?
The only way to add it would be to make the class bigger, by adding new methods to it,
which is how we jeopardize its maintainability.
[Here](https://github.com/jcabi/jcabi-http/pull/142/files),
for example, we added
[`multipartBody()`](http://static.javadoc.io/com.jcabi/jcabi-http/1.17.2/com/jcabi/http/Request.html#multipartBody%28%29)
and [here](https://github.com/jcabi/jcabi-http/pull/99/files) we added
[timeout()](http://static.javadoc.io/com.jcabi/jcabi-http/1.17.2/com/jcabi/http/Request.html#timeout%28int,%20int%29).

I always feel scared when I get a new feature request in jcabi-http.
I understand that it most probably means adding new methods to
[`Request`](http://static.javadoc.io/com.jcabi/jcabi-http/1.17.2/com/jcabi/http/Request.html),
[`Response`](http://static.javadoc.io/com.jcabi/jcabi-http/1.17.2/com/jcabi/http/Response.html),
and other already bloated interfaces and classes.

I actually tried to do something in the library in order to solve this problem
but it wasn't easy. Look at that
[`.as(RestResponse.class)`](http://static.javadoc.io/com.jcabi/jcabi-http/1.17.2/com/jcabi/http/Response.html#as%28java.lang.Class%29)
method call. What it does is decorating a `Response` with
[`RestResponse`](http://static.javadoc.io/com.jcabi/jcabi-http/1.17.2/com/jcabi/http/response/RestResponse.html),
in order to make it method-richer. I just didn't want to make
[`Response`](http://static.javadoc.io/com.jcabi/jcabi-http/1.17.2/com/jcabi/http/Response.html)
contain 50+ methods, like many other libraries do. Here is what it
does (this is pseudo-code):

{% highlight text %}
class Response {
  RestResponse as() {
    return new RestResponse(this);
  }
  // Seven methods
}
class RestResponse implements Response {
  private final Response origin;
  // Original seven methods from Response
  // Additional 14 methods
}
{% endhighlight %}

As you see, instead of adding all possible methods to `Response` I
placed them in supplementary decorators
[`RestResponse`](http://static.javadoc.io/com.jcabi/jcabi-http/1.17.2/com/jcabi/http/response/RestResponse.html),
[`JsonResponse`](http://static.javadoc.io/com.jcabi/jcabi-http/1.17.2/com/jcabi/http/response/JsonResponse.html),
[`XmlResponse`](http://static.javadoc.io/com.jcabi/jcabi-http/1.17.2/com/jcabi/http/response/JsonResponse.html),
and
[others](http://static.javadoc.io/com.jcabi/jcabi-http/1.17.2/com/jcabi/http/response/package-frame.html).
It helps, but in order to write these decorators with the central
object of type `Response` we have to use that "ugly" method
[`as()`](http://static.javadoc.io/com.jcabi/jcabi-http/1.17.2/com/jcabi/http/Response.html#as%28java.lang.Class%29),
which heavily depends on Reflection and
[type casting]({% pst 2015/apr/2015-04-02-class-casting-is-anti-pattern %}).

In other words, fluent interfaces mean large classes or some ugly workarounds.
I mentioned this problem earlier, when I [wrote]({% pst 2017/oct/2017-10-10-streams-vs-decorators %}) about
[Streams API](http://www.oracle.com/technetwork/articles/java/ma14-java-se-8-streams-2177646.html)
and their interface
[Stream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html),
which is perfectly fluent. There are 43 methods!

That is the biggest problem with fluent interfaces&mdash;they _make_ objects huge.

Fluent interfaces are perfect for its users, since all methods
are in one place and the amount of classes is very small.
It is easy to use them, especially with
[code auto-completion](https://www.jetbrains.com/help/idea/auto-completing-code.html)
in most IDEs. They also make client code more readable, since "fluent" constructs
look similar to plain English (aka [DSL](https://en.wikipedia.org/wiki/Domain-specific_language)).

That is all true! However, the damage they cause to objects design
is the price, which is too high.

What is the alternative?

I would recommend to use decorators and
[smart objects]({% pst 2016/apr/2016-04-26-why-inputstream-design-is-wrong %})
instead. Here is how I would design jcabi-http, if I would do it now:

{% highlight java %}
String html = new BodyOfResponse(
  new ResponseAssertStatus(
    new RequestWithMethod(
      new JdkRequest("https://www.google.com"),
      "GET"
    ),
    200
  )
).toString();
{% endhighlight %}

This is the same code as in the first snippet above, but it is much
more object-oriented. The obvious problem with this code, of course, is that
the IDE won't be able to auto-complete almost anything. Also, we will
have to remember many names of the classes. Also, the construct looks
rather difficult to read for those who are used to fluent interfaces. Also,
it's very far away from the DSL idea.

But here is the list of benefits. First, each object is small, very cohesive and
they all are losely coupled, which are obvious merits in OOP. Second,
adding new functionality to the library is as easy as creating a new class;
no need to touch existing classes. Third, unit testing is simplified,
since classes are small. Forth, all classes can be immutable, which also
is an [obvious merit]({% pst 2014/jun/2014-06-09-objects-should-be-immutable %}) in OOP.

Thus, it's seems like a conflict between usefulness and maintainability.
Fluent interfaces are good for users, but bad for library developers. Small
objects are good for developers, but difficult to understand and use.

It seems to be so, but only if you are used to large classes and procedural
programming. To me, big amount of small classes seem to be an
[advantage]({% pst 2017/feb/2017-02-28-too-many-classes %}),
not a drawback. The libraries that are clear, simple, and readable inside
are much easier to use, even when I don't know exactly which classes out there
are the most suitable for me. Even without the code-auto-complete I can figure
it out myself, because the code is clean.

Also, I very often find myself interested in extending an existing funcionality
either inside my code base or via a pull request to the library. I would
be much more
[interested]({% pst 2015/dec/2015-12-22-why-dont-you-contribute-to-open-source %})
to do that if I know that the changes I introduce
are isolated and easy to test.

Thus, no fluent interfaces anymore from me, only objects and decorators.
