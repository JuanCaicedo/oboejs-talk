# What are we going to learn
We're going to talk about the challenges of having web apps that need lots of data

particularly in the tradeoff between UX and performance.

We're going to talk about how the streaming approach to data transfer gives us huge benefits

and how Oboe.js lets us apply that easily in javascript.

# Server load
As web apps become more and more powerful, they start to need more and more data to be loaded

???.

This poses a big challenge to developers. It's not always a great idea to load all of the data in the server and send an HTML response with all of it already in there because users don't want to wait a millisecond more than they have to.

This is why we use AJAX, so that we can load a for the user as quickly as possible to improve the perception of performance, and then go out and then go out and fetch the data.

# Big AJAX
But just moving the operation to the client side doesn't make the operation any faster.

If you have to load a piece of data that's say quite a few megabytes, having the user wait until all that data is in memory is still going to make for a bad user experience,

even if they get to look at a spinning loading icon while they're waiting.

# Small AJAX

The solution could then be to execute many small AJAX requests and progressively make their results available to the user.

This improves UX, but it results in a much more network intensive app, since going back and forth over a network is an expensive operation.

It also requires more complex server-side code since instead of hitting an endpoint once, you have to hit an enpoint multiple times with a way of retrieving data with offsets.

# OBOE
Oboe.js lets you completely sidestep these complications

by parsing an http response and letting you interact with data points as soon as they are available.

The whole library only exports a single function, oboe, which takes a URL that it makes a request to.

The pattern syntax they offer is incredibly powerful while being extremely simple.

The most common way to use it is to specify where in the tree you're going to find the data you want and then fire your listener each time it goes off.

An even more powerful way to do it is to just specify what the object that you're looking for is going to look like.

You can then register event listeners that will fire when different parts of that response are availabe.

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
