# Introduction
- challenges of lots of data
- UX and performance.
- adopting an event-driven approach, streams
- Oboe.js all javascript

# Me
- NodeSchool NationJS
- full-stack dev at Fluencia, SpanishDict.com
- NodeSchoolDC and NovaNode

# Server load
- more and more data to be loaded into them.
- data to the front end is hard
- server wait = bad, users are not patient
- Amazon 100ms of load time

# Big AJAX
- minimal page, asynchronous request
- improved performance
- client-side slower

# Small AJAX

- lots of small AJAX requests
- incremental loading, good UX!
- latency bottleneck
- latency * operations = expensive
- more complex serverside

# Oboe.js
- To the rescue
- one function, the oboe function
- parses response, lets you interact
- SAX for JSON

# listeners
- node = pattern + listener
- specify property metadata. or metadata.date
- pixels[] - each object
- pixels[].color
- {feathers wings quacksLikeADuck}

# On the Fly

- interact on the fly
- OOP
- new Person(firstName, lastName)
- array.forEach
- listener/person instantiate and return
- done with instantiated objects

# Filtering
- array outside listeners and push
- separate blue and green people
- memory management
- too much data
- this.drop
- done doesn't have

# Lost connection

- using data as you go, don't lose everything
- lose connection 30%
- normal ajax, sorry!
- Oboe response fails, 30% on map

# server side
- response as stream.
- node easy, writeable stream
- readable.pipe(response).
- any large JSON response as a stream
- +1 static file.

# In the shell
- explain pipes and streams
- Node great with these
- aws datapipeline list-pipelines | node rerun-pipelines.js
- wait, parse the result, select, use
- oboe, exact data, no JSON.parse, no wait

# Demo

- Jenn Schiffer make8bitart
- cats picture
- export to JSON
- show file with jq
- additional properties

## code
- static html at /home, grid on it
- /data, 700 kb
- css grid and colors
- js, oboe {x y color}
- find cell add color
- fast, no change
- slow, pixel by pixel

# footprint
- 5k min.gz, jQuery/6.
- cross-browser, old IE ES5 polyfill
- dependency, Clarinet built-in and slimmed

# Not?
- small data, little benefit
- extra js, maybe slow
- engines are fast, network slow,
- small effort, big benefit

# Recap
- big data !== UX suffer
- Oboe.js = JSON data before load
- simple API, small investment
- optimize slow networks, dropped connections, memory limitations.
