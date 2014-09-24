title: Producer-Consumer in JavaScript
tags: javascript
url: producer-consumer-in-javascript
date: Wed Sep 24 2014 00:00:00 GMT-0400 (EDT)
------------------------
Couple of month ago, I wrote a XMPP-bot which acts like a chat-room. It simply sends the messages that it received to the other people that have added the bot. There is already online services for this purpose like [PartyChat](http://partych.at), but as I wanted something more customized I started to write my own.

I used [NodeJS](http://www.nodejs.org) to write bot. using [Node-XMPP](https://github.com/node-xmpp/node-xmpp) module made the whole process very easy. The only problem was when a lot of users try to send messages. First, I had used a simple logic to broadcast messages to the other users, which was fine when there was no rush in incoming messages:
```javascript
xmpp.on('chat', function (from, body) {
	users.forEcah(function (user) {
		xmpp.send(user, '[' + from + ']: ' + body);
	});
});
```
When a lot of messages came at the same time, the not all of them had  sent to the other users. So I needed to change the broadcast strategy.

I needed to schedule sending out messages in a way that there will be enough delay between each `xmpp.send` call. This is a kind of [Producer-Consumer Problem](http://en.wikipedia.org/wiki/Producer%E2%80%93consumer_problem).

I wanted a module that I can work with it like this:

```
var queue = new Queue(consumerFunction, delay);
queue.push(something);
queue.push(somethingElse);
```
And it automatically feed the input into the `consumerFunction` with the specified amount of delay.

First we should create a `Queue.js` file in our project and fill it with necessary lines:
```
var Queue = function (fn, delay) {
}
module.exports = Queue;
```
Every `Queue` instanced that will be `new`ed is a new queue. We have to keep the consumer function and the delay for each created queue in itself. And also we need an array in which we keep our stuff to be consumed later:
```
var Queue = function (fn, delay) {
	this.fn = fn;
	this.delay = delay;
	this.Q = [];
}
module.exports = Queue;
```
As we've seen before, we also need a `push` function on our `Qeueu`. This function should first see if there is a consumption job going on already or not. If there is, then we can just simply push the thing into the array and let the consumer consume it. If there isn't, we should add the item into the array and then notify the consumer to process that stuff. We can add a flag to our `Queue` which keeps track of business of the queue.
```
var Queue = function (fn, delay) {
	this.fn = fn;
	this.delay = delay;
	this.Q = [];
	this.busy = false;
	this.push = function (item) {
		this.Q.push(item);
		if (!this.busy) this.notify();
	}
	this.notify = function () {
		this.busy = true;
		var item = this.Q.shift();
		this.fn(item);
		this.busy = false;
	}
}
module.exports = Queue;
```
Now the `notify` function should do the rest of the job. It should consume the first element of array by feeding it into the consumer function (which named `this.fn` here), and then to schedule the next call of itself to see if there is any job remains.

In order to schedule the next call, we should use `setTimeout` function. As you may know, `this` pointer does not preserved in nested function calls. So we should store our `this` into another variable to access `this.fn` into a nested function. Here I used the variable name `self` which is more convenient.
```
var Queue = function (fn, delay) {
	this.fn = fn;
	this.delay = delay;
	this.Q = [];
	this.busy = false;
	this.push = function (item) {
		this.Q.push(item);
		if (!this.busy) this.notify();
	}
	this.notify = function () {
		this.busy = true;
		var item = this.Q.shift();
		this.fn(item);
		var self = this;
		setTimeout(function () {
			self.notify();
		}, this.delay);
		this.busy = false;
	}
}
module.exports = Queue;
```
But wait! We haven't manipulate the `busy` flag very well. And what will be happen when the array become empty? We have to check first if the array is empty or not. If it is empty, then we are finished for now and we can unset the `busy` flag and rest. And if the array isn't empty, just peek an item, consume, then schedule next consumption.
```
var Queue = function (fn, delay) {
	this.fn = fn;
	this.delay = delay;
	this.Q = [];
	this.busy = false;
	this.push = function (item) {
		this.Q.push(item);
		if (!this.busy) this.notify();
	}
	this.notify = function () {
		if (this.Q.length == 0) {
			this.busy = false;
			return;
		}
		this.busy = true;
		var item = this.Q.shift();
		this.fn(item);
		var self = this;
		setTimeout(function () {
			self.notify();
		}, this.delay);
		this.busy = false;
	}
}
module.exports = Queue;
```
Well, that's all. Another thing is when we call the `fn` function in this way: `this.fn(item)` if user uses the `this` keyword in the `fn` function, their `this` will become our `this`. It is safer if we call it this way: `this.fn.call(null, item);`. Now `fn`'s `this` is going to be bound to `null`.

Here is the final code. This is probably not the best way to do it, but it was fun and very easy to implement.

How do you test this kind of modules? Tell me in the comments and we can have a post in future about testing these stuff.
```
var Queue = function (fn, delay) {
	this.fn = fn;
	this.delay = delay;
	this.Q = [];
	this.busy = false;
	this.push = function (item) {
		this.Q.push(item);
		if (!this.busy) this.notify();
	}
	this.notify = function () {
		if (this.Q.length == 0) {
			this.busy = false;
			return;
		}
		this.busy = true;
		var item = this.Q.shift();
		this.fn.call(null, item);
		var self = this;
		setTimeout(function () {
			self.notify();
		}, this.delay);
		this.busy = false;
	}
}
module.exports = Queue;
```
