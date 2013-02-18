# Amateur Erlang

I don't actually make my living programming Erlang, so I'm still a beginner in a lot of ways.  I've been tinkering with it for the last year and a half or so, and in short, it's been awesome. I've had a lot of fun; I've learned a ton, and what I've learned has been more broadly useful than I might have expected; and overall it's definitely made me a better programmer.

So I'm going to talk about that experience: what you learn when you learn Erlang; some of the "ah-ha!" moments I've had - things that will give you a running start at the Erlang learning curve; and how to get some practical experience with Erlang before you dive into writing distributed, high-availability systems.


## Foreign Travel

Learning a new programming language is like going to a foreign country. It's not just the language, it's the culture that goes with it. They do things differently over there. If you just drop in for a day trip, it's going to be all weird and awkward; but if you stick around a bit, you start getting used to it; and when you go home, you find that there are things you miss and things that you've brought home with you.

There's also a sort of meta-learning, because then when you go to a different country, it's not as jarring; you adapt more quickly. I found that once I'd gotten used to Erlang's syntax, other languages - Coffeescript and Scala - didn't look so weird. At work the other day, someone was doing a demo of iPhone development, and some of my co-workers were really thrown by Objective-C's syntax. I was just like, "Oh yeah, now that you mention it, it does have an odd mix of Lisp-style bracket grouping and C-style dot notation. Whatever. It's code."

Working with Erlang also teaches you a fundamentally different way of solving problems, especially if, like me, you're coming from an object-oriented (OO) background like Java or Python. It has functional language features like recursion and closures. It focuses on simple data structures, and gives you powerful tools for working with them. And it's all about concurrency. Those all add up to something more than the sum of their parts. They're also things that translate to other languages: You'll see Erlang-style concurrency in Scala, and functional programming is showing up all over the place these days.

### Bowling

A good example of this is the bowling game program. I've [written about this before](http://www.bluegraybox.com/blog/2011/11/25/elegant-bowling/), so let me just recap it quickly. It's a standard programming challenge: Calculate the score for a game in bowling. It's fairly straightforward, but there are a bunch of tricky edge cases. The first time I did it was in Python as a pair programming exercise, and at the end I was pretty happy with the results. It came out to 53 lines of code. Then about a year later, we did the same thing at one of the Erlang meetups, and the solution that one of the experienced Erlang programmers turned in was about ten lines of code. Ten lines of clean, elegant code, not like a Perl one-liner. That blew my mind.

I went back and looked at the Python code and realized how much of it was OO modeling that doesn't actually help solve the problem. In fact, it creates a bunch of its own problems. Obviously, you need a Game class and a Frame class, and the Game keeps a list of Frames. Then you very quickly get into all these metaphysical questions around whether a Frame should just be a dumb data holder, or whether it should be a fully self-actualized and empowered being, capable of accessing other frames to calculate its score and detect bad data. Putting all the smarts in the Game may be the easiest thing, but that brings up historical echoes of failed Soviet central planning, and just doesn't feel very OO. And once you've got these classes, you start speculating about possible features: What if you want to be able to query the Game for a list of all the rolls - does that change how you store that info? In short, you can get really wrapped around the axle with all these design issues.

The Erlang solution sidesteps that whole mess. It just maps input to output. The input is a list of numbers, the output is a single number. That sounds like some kind of fold function. With pattern matching, you write that as one function with four clauses: End of game, strike frame, spare frame, normal frame.

```erlang
score(Rolls) -> frame(Rolls, 1, 0).

 %% Game complete.
frame(_BonusRolls, 11, Score) -> Score;

 %% Strike.
frame([10|Rest], Frame, Score) ->
    frame(Rest, Frame + 1, Score + 10 + strike_bonus(Rest));

 %% Spare.
frame([First,Second|Rest], Frame, Score) when (First + Second == 10) ->
    frame(Rest, Frame + 1, Score + 10 + spare_bonus(Rest));

 %% Normal.
frame([First,Second|Rest], Frame, Score) ->
    frame(Rest, Frame + 1, Score + First + Second).

 %% spare & strike bonus calculations.
spare_bonus([First|_Rest]) -> First.
strike_bonus([First,Second|_Rest]) -> First + Second.
```

### Bringing it Home

The thing is, once I'd seen the solution in Erlang, I was able to go back and implement it in Python; it came out to roughly the same number of lines of code, and was about as readable. That transfers, that way of solving problems. Instead of thinking, "What are the classes I need to model this problem domain?" start with, "What are my inputs and outputs? What's the end result I want, and what am I starting from? Can I do that with simple data structures?"

So now when I write Python code, I use list comprehensions a lot more; `for` loops feel kinda sketchy - clumsy and error-prone. Modifying a variable instead of creating a new one sets off this tiny warning bell. I use annotations and lambda functions more often, and wish I had tail recursion and pattern matching. I do more with lists and dictionaries; defining classes for everything feels like boilerplate.

In the last year, I've also done a bunch of rich browser client Javascript programming with jQuery and Backbone.js. That's a very functional style of programming. It's all widget callbacks and event handling - lots of closures. (I don't know who originally said it, but Javascript has been described as "Lisp with C syntax".) Actually, I was coding in Coffeescript and debugging in Javascript. Coffeescript is essentially a very concise and strongly functional macro language for generating Javascript. So it was a really good thing to have the experience with Erlang going into that.

### Community

The other thing about foreign travel is the people you meet. I'd like to make a little plug for the Erlang community. It's still small enough to be awesome. Just lurk on the [erlang-questions mailing list](http://erlang.org/mailman/listinfo/erlang-questions), and you can learn a ton. There are some really sharp people on it, and the discussions are a fascinating mix of academic and practical. You see threads that wander from theoretical computer science to implementation details to performance issues.


## Ah-ha! Moments

Like I said, Erlang has a different way of doing things. It's not that it's all that more complicated than other languages, but it's definitely different. So I'm going to talk about some of the ah-ha! moments - the conceptual breakthroughs - that made learning it easier.

### Syntax

I'll start with the syntax, which is probably the least important difference, but it's the first thing that people tend to get hung up on. They look at Erlang code, and they're all like, "Where are the semicolons? What are all these commas doing here? Where are the curly braces?" It all seems bizarre and arbitrary. It's not. It's just not like C.

What helped me get used to Erlang's syntax was realizing that what it looks like is English. Erlang functions are like sentences: You have commas between lists of things, semicolons between clauses, and a period at the end. Header statements like `-module` and `-define` all express a complete thought, so they end with a period. A function definition is one big multi-line sentence. Each line within it ends with a comma, function clauses end with a semicolon, and there's a period at the end. `case` and `if` blocks are like mini function definitions: They separate their conditions with semicolons and end with `end`. `end` *is* the puctuation; you don't put another semicolon before it. After `end`, you put whatever punctuation would normally go there.

You also have to realize that all the things you think of as control structures - `case`, `if`, `receive`, etc. - are functions.

Here's a cheat sheet:

```erlang
-module(my_module).

my_func([]) ->
    Value = get_default_value(),          % comma
    Response = case other_func(Value) of
        ok -> "We're good!";              % semicolon
        _ -> "Oh noes!"                   % nothing!
    end,                                  % comma
    {Response, Value};                    % semicolon
my_func([Value]) ->
    {"We're good!", Value};               % semicolon
my_func(Values) ->
    IncDbl = fun (X) ->
        Inc = X + 1,                      % comma
        Inc * 2                           % nothing!
    end,                                  % comma
    Value = lists:map(IncDbl, Values),    % comma
    {"We're good!", Value}.               % period
```

Even with that, it's still pretty idiosyncratic. You'll find yourself making a bunch of syntax mistakes at first, and that'll be frustrating. Let me just say that you'll get used to it faster than you expect. After a couple weekends hacking on Erlang code, it'll start to look normal.

### Recursion

Recursion is not something you use much in OO languages because (a) you rarely need to, and (b) it's scary - you have to be careful about how you modify your data structures. Recursive methods tend to have big warning comments, and nobody dares touch them. And this is self-reinforcing: Since it's not used much, it remains this scary, poorly-understood concept.

In Erlang, recursion takes the place of all of the looping constructs and iterators that you would use in an OO language. Because it's used for everything, there are well-established patterns for writing recursive functions. Since you use it all the time, you get used to it, and it stops being scary.

This is also where Erlang's weirdnesses start working together. Immutable variables actually simplify recursion, because they force you to be clear about how you're changing your data at each step of the recursion. Pattern matching and guard expressions make recursion more powerful and expressive, because they let you break out the stages of a recursion in a very declarative way. Let's look at the basics of recursion with a very simple example: munging a list of data.

Like a story, a recursive function has a beginning, a middle, and an end. The beginning and end are usually the easiest parts, so let's tackle those first. The beginning of a recursion is just a function that takes the input, sets up any initial state, ouput accumulators, etc., and recurses. In this case, we take an input list and set up an empty output list.

```erlang
%% beginning
func(Input) ->
    Output = [],
    func(Input, Output).
```

The end stage is also easy to define. When the input is an empty list, just return the output list.

```erlang
%% end
func([], Output) -> lists:reverse(Output).
```

The middle stage defines what we do with any single element in the list, and how we move on to the next one. Here, we just pop the first element off the input list, munge it to create a new element, push that onto the output list, and recurse with the newly-diminished input and newly-extended output. (And note that we add our new element at the beginning of the list, rather than the end - it's an efficiency thing.)

```erlang
%% middle
func([First | Rest], Output) ->
    NewFirst = munge(First),
    func(Rest, [NewFirst | Output]);
```

That's all there is to the basics of recursion. You may have multiple inputs and outputs, and there could be multiple middle and end functions to handle different cases (and we'll see a more interesting example in a minute), but the basic pattern is the same.

As a coda to this, it's worth mentioning that this is essentially what Erlang's `lists:map/2` function does, so you could replace all the forgoing with something like:

```erlang
lists:map(fun my_module:munge/1, Input)
```

The `lists` module has a number of other functions for doing simple list munging like this.

### More OO than OO

The next thing is Erlang process spawning and inter-process communication. Again, this is one of those things that in normal languages is rarely used and fraught with peril. In Java, multithreaded applications involve a lot of painstaking synchronization, and you still often get bit by either concurrent modification errors or performance issues from overly aggressive locking. In Erlang of course, you do it all the time. Understanding why requires a bit of background.

The original concept of object oriented programming was that objects would be autonomous actors rather than just data structures. They would interact with each other by sending messages back and forth. You see artifacts of this like Ruby's `send` method. (Rather than invoking a method directly, you call `send` on the object with the method name as the first argument.) In practice, objects in OO languages are little more than data structures with function definitions bolted on. They're not active agents; they're passive, waiting around for a thread of execution to come through and do something to them.

In a sense, Erlang is more truly object oriented than OO languages, but you come to it by a roundabout way. Since even complex data structures are immutable, updating your data always creates a new reference to it. If you pass any data structure to a function, as soon as it modifies it, it's dealing with a different data structure. So the only way to have something like global, mutable data is to have that reference owned by a single process and managed like so: 

```erlang
loop(State) ->
    receive Message ->
        NewState = handle_message(Message, State),
        loop(NewState)
    end.
```

(You wouldn't literally have code like this, but it's conceptually what you're doing.) `State` is any data structure, from an integer to a nested tuple/list/dictionary structure. You'd spawn this loop function as a new process with its initial state data. From then on it would receive messages from other processes, update its state, maybe send a respose, and then recurse with the new state. The key here is that it's a local variable to this function; there's no way for any other process to mess with it directly. If you spawn another process with this function, it will have a separate copy of the `State`, and any updates it makes will be completely independent of this. The simplest example I can think of would be an auto-incrementing id generator:

```erlang
loop(Id) ->
    receive
        {Pid, next} ->
            NewId = Id + 1,
            Pid ! NewId,
            loop(NewId)
    end.
```

You could start it up and get new ids like so:

```erlang
Pid = spawn(fun() -> loop(0) end),
Pid ! {self(), next},
Id = receive Resp -> Resp end.
```

So anything that would be an object in an OO language is a process in Erlang. I hadn't realized quite how true that was until I was messing around in the Erlang shell, and opened a file. `file:open/2` says it returns `{ok, IoDevice}` on success. Let's take a look at that:

```
1> file:open("test.txt", [write]).
{ok,<0.35.0>}
```

Hey, wait! That's a process id. See?

```
2> self().
<0.32.0>
```

So when you open a file, you don't actually access it directly; you're spawning off a process to manage access to it.

As with recursion and the `lists` module, Erlang has modules like `gen_server` and `gen_event` which gieve you a more formal and standard way to do this sort of thing. They add a lot of process management on top of this basic communication, so I won't get into the details here, but check it out.

## Getting Practice

Ok, so once you've gotten past the language concepts, how can you actually get some practice with it? Something a little more low-key than massively distributed high-availability systems?

### Scripting

Probably the easiest way to start, if you just want to get comfortable with the language, is shell scripting. `escript` lets you use Erlang as a scripting language.

```erlang
#!/usr/local/bin/escript

main(Args) ->
    io:format("Hello world!~n\t~p~n", [Args]).
```

That's pretty cool. You have the ease of scripting, with full access to Erlang's libaries. Furthermore, you can set a node name or sname in your script, and then it can connect to other Erlang nodes. (The special `%%!` comment says to pass the rest of the line through as parameters to `erl`, the Erlang emulator.)

```erlang
#!/usr/local/bin/escript
%%! -sname my_script
```

For example, here's a simple way to grab a web page:

```erlang
#!/usr/local/bin/escript

main([Url]) ->
    inets:start(),
    {ok, {{_Proto, Code, _Desc}, _Hdr, Content}} = httpc:request(Url),
    io:format("Response (~p):~n~s~n", [Code, Content]).
```

That's actually pretty handy because you can fetch data from web services that way. I started with this and built out a really simple automated testing tool for a web service I was writing, in about 20 lines of code. You can do all sorts of useful little things like this. They're a good way to get used to Erlang's idioms, and you can gradually build in more complexity as you go.

### Testing Tools

In fact, testing tools are another way to get in some real experience with Erlang. You could do something simple to test web service functionality, or something more complicated and concurrent for load testing.

You could also mock out back-end web services for testing. I was doing some browser-side Javascript development last summer, and didn't have access to the server I'd be talking to. (It was running on an embedded device.) So I faked it up in Erlang with [Spooky](https://github.com/flashingpumpkin/spooky), which is a simple Sinatra-style framework. It went something like this:

```erlang
-module(my_web_service).
-behaviour(spooky).
-export([init/1, get/2]).

init([])-> [{port, 8000}].

get(Req, [])->
    Req:ok("Default response");
%% http://localhost:8000/path/to/resource
get(Req, ["path", "to", "resource"])->
    Req:ok("Canned response for resource");
get(Req, ["path", "to", "other-resource"])->
    Req:ok("Canned response for other resource").
```

### Web Apps

If you're coming from a web background, that's another good place to start tinkering with Erlang. Instead of trying to think up an Erlang project, just do your next personal web app in Erlang. Erlang has a range of web application frameworks, so you can decide how much of the heavy lifting you want to do. As you saw, Spooky lets you simple stuff easily, but it's fairly low-level.

[ChicagoBoss](http://www.chicagoboss.org/) is a richer, Django-like framework. It has an ORM, URL dispatching, and page templates (with Django syntax, no less). Wait, _Object_-Relational Mapper? What's that doing in a functional language? Yeah, ok, really they're proplists with a parameterized module and a bunch of auto-generated helper functions wrapped around them. They're still immutable; don't freak out. More experienced developers may argue about whether that's the right way to do things, but it certainly makes ChicagoBoss more beginner-friendly.  It also gives you some enticing extras like a built-in message queue and email server. The ChicagoBoss tutorial is really concise and well-written, so I'll leave it at that.

If you want to get into the nuts and bolts of proper HTTP request handling, take a look at [WebMachine](https://github.com/basho/webmachine/wiki). Most web frameworks leave out or gloss over a lot of the richness of the HTTP protocol. WebMachine not only gives you a lot of control over every step of the request handling, but actually forces you to think through it. It's not the most intuitive for beginners, but it's an education.

Those are the ones I've played with a bit, but there are lots more.


### Contributing

One of the things I've run across with these, as with most open-source tools, is that there are "opportunities to contribute." We'd love it if all of our software tools worked perfectly all the time, but the next best thing is if the source is on GitHub. Working with Spooky, I tripped over an odd little edge case. It turned out to be a simple fix - half a dozen lines of code. I forked it, fixed it, and put in a pull request. Had a similar experience with the ChicagoBoss templating code. They were both tiny contributions, but you still get a warm fuzzy feeling doing that. Throw in a few extra unit tests if you really want to make the owners happy.

Even if you're unlucky, and the code works perfectly, almost every piece of software out there could benefit from better documentation. Take advantage of your newbie status; write a tutorial. The people who wrote the software know it inside and out; it helps to have beginners writing for beginners. I can vouch that a great way to learn something is to try to explain it to someone else.


### Adventure Awaits!

What I hope I've left you with is a sense that Erlang is worth learning in its own right, that it'll teach you new things about programming that you can apply in any language; that while it'll be strange at first, it's totally learnable; and that there are any number of low-intensity ways to get started using it. Most importantly, though, I want to leave you with the sense that this is fun. Learning a new language, new problem-solving tools, new ways of expressing ideas, that's all fun. You've got an adventure ahead of you.
