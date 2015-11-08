# What are we going to learn
Hi everyone! Thank you all for coming. We're going to talk about the challenges of writing web applications that require big amounts of data. We're particularly going to focus on the tradeoff we often have to make between UX and performance.

We're going to cover the basics of Unix streams and why they are so beneficial in the back end.

And then we're going to talk about how bringing that streaming philosophy into web applications gives us huge benefits and how Oboe.js let us do that in a simple fully-javascript approach.

# Who am I, and why you should listen to me
My name is Juan Caicedo and you probably shouldn't. Just a short year ago, I was a java developer, but I fell in love with Node at last year's NodeSchool workshop at NationJS. Now I work as a full-stack dev at Fluencia in Rosslyn, where we make the biggest and best Spanish-English online dictionary, SpanishDict.com.

# Server load
Now let's get back to our problem. As web apps become more and more powerful, they start to need more and more data to be loaded into them.

This poses a big challenge to developers, because getting lots of data to the front end is hard. It's not usually a great idea to wait for this huge amount of data to be loaded in the server and before you send an HTML response because users are not patient. Amazon did a study where they added 100ms of load time, and they lost 1% of sales. To put that in context, that's a fourth of the time it takes you blink your eyes, and it equates to millions in loss of revenue for a company like Amazon.

This is why AJAX became such a big thing, because you can instead give minimal page back to the user as fast as possible and then make an asynchronous request to get that data and then give it back to the user when it's ready giving them the perception of improved performance.

# Big AJAX
However, moving that operation of getting data to the client-side doesn't make it any fast. In fact, it probably makes it slower, because odds are you're now doing it from a much less powerful device, like a dinky android device on 2g instead of your beefy AWS server.


# Small AJAX

The solution could then be to break that up, so that instead of doing one big AJAX request, you do a lot of small AJAX requests, and each time one of them finishes, you take that data and give it to the user. This is what's known as incremental loading, and it does improve UX!

The thing is that the biggest bottleneck when it comes to web performance is latency, or how long it takes to go from the client side, to the server, and then make it back to the client side. Now you're multiplying the cost of that latency times the number of requests you want to make, which gets very expensive very quickly.

It also requires more complex server-side code since now instead of hitting an endpoint once, you have to hit an endpoint multiple times and have logic for offsetting which data you're requesting and a way of aggregating it on the client.

# OBOE
This is where Oboe.js comes to the rescue, letting us completely sidestep these complications.

Oboe is a library that gives you one function, the oboe function. The oboe function takes a url and makes a request to it. It then goes through and parses that response as coming in to the browser, but it lets you interact with the response before the parsing is all done. It's basically a SAX parser for JSON and the web, letting you attach event listeners when different parts of the response are ready, and then letting you use that data while it continues parsing.

Attaching a listener is also extremely simple. All you do is call a function called node, which takes a pattern to recognize what part of the json to look for, and then a function to call once it matches that pattern.

The pattern syntax is incredibly powerful while being really concise. The simplest form is to simply specify property you want to listen for. Here your callback will fire when oboe finds loads a property called person. You can even listen further down and get an event when a name property is found inside of the person property.

If you know that the property is going to be an array, then you can specify the property name with an array syntax. Now the callback will fire for each object inside of that array instead of just once when the whole array has loaded.

You can also combine these two, by specifying an array, then a property inside each of its objects. Here, oboe is going to fire the callback it find the name property for each object inside the people array.

My favorite way to use oboe is to use duck typing. You can tell it to look for any object that has certain properties. Here, if it has feathers, it has wings, and it has quacksLikeADuck, then it's a duck, and you're going to fire the duck callback.

# On the Fly OOP

This type of approach is really awesome because it allows you to interact with the data you want while it's still in the middle of being loaded.

For example, if you need to follow an Object Oriented model and instatiate an object with each person you find, a normal AJAX approach would need you to get the whole response and then iterate through it to call your constructor with each piece of JSON.

With oboe, you can instead instantate each of those as soon as the data for it is available.

# Filtering
Another great way to use oboe is as a filtering library.

This gets to another pretty cool feature of Oboe which is its memory management.

The whole response object is still aggregated for you and available inside of the done function.

However you can use another function called oboe.drop which tells the library to just forget about the part of the response it just parsed.

You can get what you need out of the response while you're receiving it, do what you need with it like maybe write it to the dom, then let it go and claim back your resources.

In theory this allows you to load data that is bigger than the available memory, because it is never all in memory at the same point.

# Lost connection

There's some built-in fool-proofing you get from this event-based approach as well.

Since you are using the data you need as you go, if something fails when you're getting the response for any reason, not all is lost.

Say you lose connection 30% of the way through getting an array of shops to display on a map, your user still gets a map with 30% of shops on it, instead of not getting any and having to reinitiate the request.

# server side
Now what does it take on the server-side to enable oboe to work its magic?

It turns out not a whole lot. All you need to do is make sure that the response you're giving back to the client is a stream.

In node, this is almost trivial because the response is already a writeable stream.

This means that you can get your data as a readable stream and then just pipe it into the response.

# All JSON
If you happen to think that's too much work though, here is a really cool feature.

It turns out that you can actually view any large JSON response as a stream because the data isn't all downloaded to your browser at once.

Therefore oboe can give you some benefits when applied to any REST end point.

# Middle tier
A really cool opportunity comes in how you use the middle tier.

If you have a really resource intensive request that needs to happen, but you know that your users are going to be using a pretty weak client like an old phone on a bad network, you can have the request go to a middle server, have that server which is a beefy EC2 instance make a request to get your data, then pipe that response to the client.

Even better, you could pass the response of that request into oboe, filter the data down and then write that back to your response.

Or you can make requests to a few different servers, then combine their responses into a single stream, and have oboe go through that.

# In the shell
The oboe function takes any readable stream, so you can write a script that gets a big piece of data piped into it and do all these oboe things with process.stdin.

You don't even hace to call JSON.parse. That's actually a real use case in our adops ETL process, where we have a shell script read all the details of out datapipelines and pipe them into a script that uses that data to rerun those pipelines each morning.

# footprint
Using oboe.js is really simple. Programmatically, there's not all that much going on.

As far as its footprint, minified it's about one eigth of the size of jquery minified.

As far as cross-browser compatiblity, it needs ES5, so if you want to support IE 8 you need a polyfill, but that's about it.

# Not?
When should you not use oboe? I guess if you're loading a really small amount of data, there's probably not a lot of benefit over just doing a jQuery ajax request.

For example, when we're loading megaexamples client-side in neodarwin, it takes longer for the server to figure out those few examples and given them back than it does to parse that data when it's ready, so there's probably not a huge benefit in adding a few extra lines of code to progressively render them.

However, I would love it if when I search for stores in google maps it started putting down pins one at a time instead of waiting to have them all ready.

Thanks!
