---
layout: post
title: "Actor model"
date: 2014-12-30 19:19:30
categories: scala
---

Explanation from [Programming in scala][pro-in-scala]
---
Actors are concurrency abstractions that can be implemented on top of
threads. They communicate by sending messages to each other. An actor can
perform two basic operations, message send and receive. The send operation,
denoted by an exclamation point (!) , sends a message to an actor. Here’s an
example in which the actor is named recipient :
recipient ! msg
A send is asynchronous; that is, the sending actor can proceed immediately,
without waiting for the message to be received and processed. Every actor
has a mailbox in which incoming messages are queued. An actor handles
messages that have arrived in its mailbox via a receive block:

{% highlight scala %}
receive {
  case Msg1 => ... // handle Msg1
  case Msg2 => ... // handle Msg2
  // ...
}
{% endhighlight %}

A receive block consists of a number of cases that each query the mailbox
with a message pattern. The first message in the mailbox that matches any of
the cases is selected, and the corresponding action is performed on it. If the
mailbox does not contain any messages that match one of the given cases,
the actor suspends and waits for further incoming messages.

As an example, here is a simple Scala actor implementing a checksum
calculator service:

{% highlight scala %}
actor {
  var sum = 0
  loop {
    receive {
      case Data(bytes)
      => sum += hash(bytes)
      case GetSum(requester) => requester ! sum
    }
  }
}
{% endhighlight %}
This actor first defines a local variable named sum with initial value zero. It
then repeatedly waits in a loop for messages, using a receive statement. If it
receives a Data message, it adds a hash of the sent bytes to the sum variable.
If it receives a GetSum message, it sends the current value of sum back to the
requester using the message send requester ! sum . The requester field
is embedded in the GetSum message; it refers usually to the actor that made
the request.

Reference
---
* [Programming in Scala][pro-in-scala]

[pro-in-scala]:      http://www.r-5.org/files/books/computers/Martin_Odersky_Lex_Spoon_Bill_Venners-Programming_in_Scala-EN.pdf
