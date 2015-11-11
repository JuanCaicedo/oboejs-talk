# What are we going to learn
Hi everyone! Thank you all for coming. We're going to talk about the challenges of writing web applications that require big amounts of data. We're particularly going to focus on the tradeoff we often have to make between UX and performance.

Then we're going to talk about how and adopting an event-driven approach and viewing our data as a stream gives us huge benefits in our web applications and how Oboe.js let us do that in a simple fully-javascript approach.

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

# Oboe.js
This is where Oboe.js comes to the rescue, letting us completely sidestep these complications.

Oboe is a library that gives you one function, the oboe function. The oboe function takes a url and makes a request to it. It then goes through and parses that response as coming in to the browser, but it lets you interact with the response before the parsing is all done. It's basically a SAX parser for JSON and the web, letting you attach event listeners when different parts of the response are ready, and then letting you use that data while it continues parsing.

Attaching a listener is also extremely simple. All you do is call a function called node, which takes a pattern to recognize what part of the json to look for, and then a function to call once it matches that pattern.

The pattern syntax is incredibly powerful while being really concise. The simplest form is to simply specify property you want to listen for. Here your callback will fire when oboe finds loads a property called person. You can even listen further down and get an event when a name property is found inside of the person property.

If you know that the property is going to be an array, then you can specify the property name with an array syntax. Now the callback will fire for each object inside of that array instead of just once when the whole array has loaded.

You can also combine these two, by specifying an array, then a property inside each of its objects. Here, oboe is going to fire the callback it find the name property for each object inside the people array.

My favorite way to use oboe is to use duck typing. You can tell it to look for any object that has certain properties. Here, if it has feathers, it has wings, and it has quacksLikeADuck, then it's a duck, and you're going to fire the duck callback.

# On the Fly data transformation

This type of approach is really awesome because it allows you to interact with the data on the fly, you can work with it while it;s still being loaded into the application.

For example, if you want to work with your data in an Object Oriented Model. You have a JSON of people coming in and you need to call the Person constructor with each person's first name and last name. Instead of needing to get the whole array in memory and then iterate through it, you can instead fire a listener each time that a person is parsed, instantiate an object and pass it back. Oboe will remember that value and at the end give you back an object of the JSON you parsed in, but the people array will not be an array of instantiated Person objects.

# Filtering
Another great way to use oboe is as a filtering library. You can scope an array outside of your oboe listeners and then have your listeners conditionally push to that array. Say you're loading in people and you want to go ahead and skip anyone that has the name Roger Rabbit, because they're not people. Your array will only have the people who are not Roger Rabbit.

This leads into another cool feature of Oboe which is its memory management. One concern you could have is that maybe you need to find all of the Roger Rabbits in one array, but you're going to be loading in millions of people, and holding all of them in memory is going to be taxing or maybe impossible for your application. You can pass oboe a function in your listener which is this.drop. It tells Oboe to simply forget about the data it has just parsed. With our Roger Rabbit, we can do whatever we want with actual people and then drop the node. Only if we find Roger Rabbit will we keep the node. When our done listener fires, the final object it fires with will still have a people property, but now it will only have Roger Rabbits.

# Lost connection

This event-driven approach also has another nice side-effect of letting you deal with lost connections much more effectively. Since you are using the data as you go, if something fails while you're getting the response for any reason, you don't lose everything. Say you lose connection 30% of the way through getting an array of shops to display on a map, your user still gets a map with 30% of shops on it. Instead of telling the user, sorry you lost connection and we don't have any shops for you, try again, with Oboe by the time the response fails, the user already has a map with 30% of shops on it, and they just don't get any more after that.

# server side
Oboe looks really great on the client-side, but now what does it take on the server-side to enable oboe to work its magic? It turns out, not a whole lot. All you need to do is make sure that the response you're sending from the server to the client is a stream.

In node, this is incredibly easy because the response is already a writeable stream. This means that you can get your data as a readable stream and then just pipe it into the response.

A reaIf you happen to think that's too much work though, here is a really cool feature. It turns out that you can actually view any large JSON response as a stream. Because the data isn't all downloaded to your browser at once, it actually arrives package by package into the browser until it's all done, it means that Oboe will function the same way even if you're requesting a static file.

Therefore Oboe can give you benefits when applied to any REST end point.

# In the shell
I find myself using oboe a lot in the shell. In the shell you have to pipe data from one program into another, which means that the data flows out of one program as a standardout sream and into another as a standardin stream. Node is great at working with these as it can access them directly. You can run one command and then pipe it into a node script. Normally you would then have to wait for that stream to finish, parse the result, and then select and use the data you want.

With oboe, you can instead change this around, and use exactly the data you need, without having to call JSON.parse and without having to wait until the end.

# Demo

Now everyone likes to see things in action, so I've but together a demo so we can see it.

I came up with this idea seeing a talk by Jenn Schiffer at JSConf in Colombia. She has this application called make8bitart, which you can use to draw 8 bit art. Of course, no programming presentation is complete unless it has something having to do with cats, so I used make 8 bit art to make this.

That in itself is cool, but what's even cooler is that then you can export those images to a JSON format. So I can get that picture of a cat into a file. I'm going to dig in and show you a bit of what it looks like. If you've never seen jq before, it's a command line utility for working with JSON, it's way nice. Here I'm filtering what I show you, because the file wasn't quite big enough. I wanted a much fatter file, so I went through and added 30 useless properties to every object in the pixels array.

Then what I made here is an node server that serves a static html at /home. It gives you back a page, with a grid on it. And because it's a static file, it can do that really fast. The server also has a route /data, which pipes the file into memory and the pipes it to the front end. Now the file isn't gigantic, it's around 700 kb, but that's already far more than you want to be sending over the network.

On the front end, we have some css that just just in charge of drawing that grid for us, but then we also have these extra rules which are going to enable us to give color to a cell. Then we have a javascript file that makes a request to our server with oboe. We tell it to listen to all objects that have an x, y and color property. Then when we find those, we execute some small functions to find the cell at that x and y position, and then add a class for the name of that color. And once we're all done we'll change that message at the top to say we're done.

If I demo this for you, you can't really see anything all that cool. The data loads quickly, becauase we're on one of the most awesome connections you can have, localhost. But, if I turn on Chrome dev tools and tell it to emulate having a slower connection, you can see how it smoothly renders a few pixels at a time.

Imagine if you're way out of town, exhausted after a day of hiking and just want to find the nearest restaurant to have a beer and a burger, you don't care about all the 100 different options. Or you're twitter and you want to cut down the time it takes a user to see their first tweet, you don't want to have to wait for the other 20, especially if they're on a terrible connection.

# footprint
Using oboe.js, you can easily pull out ahead, in all of those situations. It's extremely simple, adding almost no code complexity compared to an alternative with Jquery or even native XHR.

As far as its footprint, Oboe is tiny. It comes out to just under 5k minified and gzipped. That's about an 6th of the size of jQuery.

And for cross-browser compatiblity, Oboe only depends on ES5, so if you want to support older versions of IE 8, you'll need to include a polyfill. But that's it. It's only other dependency, which is a SAX based parser called Clarinet, is built into the library and slimmed down to only it's most essential funcitons.

# Not?
Now you wouldn't trust me if I told Oboe is perfect for every situation. When should you not use oboe? I guess if you're loading a really small amount of data, you're probably not going to see much benefit over just doing an ajax request natively or with jQuery. You're adding javascript functions that have to execute, so for small enough data it's probably going to be a little slower. But javascript compilers and engines are really fast, particularly compared to anything that involves the network, so I think the point at which you start to see a benefit is pretty early. Particularly for the fact that with no extra effort it makes your application accessible to bad network connections and lower end devices.

# Recap
To recap, needing to load a big amount of data into a web application is no reason to let your UX. With Oboe.js, you can start using part of JSON data in your application before it has all been loaded into memory. Using a really simple javascript API, with very little overhead, you can listen in to any part of the response and then do whatever you want with it. In doing so, you effortlessly optimize for slow networks, dropped connections and memory limitations.

Oboe is on Github and if you have any questions or problems, I check in on the issues pretty regularly. It also has really great docs explaining why you should use it and examples of how to do it. Plus it has some great animations.

# Questions
I've posted up here a bunch of resources relating to this talk, including these slides. That's all I have for you tonight, but I'd love to answer any questions if you have them.

cat points.json | jq '.pxif.pixels | map({x:.x, y:.y, color:.color})'  | less
