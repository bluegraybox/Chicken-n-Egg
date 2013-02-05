h1. Chickens and Eggs

Full disclosure: I've been an Erlang hobbyist for the last year and a half or so, but my paid, professional experience with it is really minimal - maybe a couple of hours. I'm not speaking from a deep well of practical experience here, but I've gotten over that first hump, where I'm comfortable with the language, and I'd be willing and interested to use it for real.

h2. Intro

Erlang has this chicken-and-egg problem. The core of it is a variant on the old, "You need the experience to get the job, but you need the job to get the experience," conundrum. There are two things that make this more acute: The kind of systems that Erlang is particularly good for are exactly the kind of systems that you don't want to be building if you don't know what you're doing; and Erlang is not like the other kids in the trailer park. It looks kinda funny. It doesn't have C-style syntax. It has immutable variables and no @for@ loops. That throws a lot of people.

So what can I do in half an hour to help you break out of this catch-22? The first part is to convince you that Erlang is worth learning even if you never use it professionally; it's its own reward and time well spent. The second part is to give you a running start at the Erlang learning curve, to help get you over the hump. There are a few concepts in Erlang in specific - and functional programming in general - that you need to wrap your head around. Hopefully, there will be an "Ah-ha!" moment or two for you in here somewhere. The third part is giving you a few ideas about what you can work on to get some practical experience with Erlang.

h2. Why?

Learning a new programming language is like going to a foreign country. It's not just the language, it's the culture that goes with it. They do things differently over there. If you just drop in for a day trip, it's going to be all weird and awkward; but if you stick around a bit, you start getting used to it; and when you go home, you find that you've brought some of it back with you, and there are other parts that you miss.

There's also a sort of meta-learning, because then when you go to a different country, it's not as jarring; you adapt more quickly. I found that once I'd gotten used to Erlang's syntax, other languages - Coffeescript and Scala - didn't look so weird. At work the other day, someone was doing a demo of iPhone development, and some of my co-workers were really thrown by Objective-C's syntax. I was just like, "Oh yeah, now that you mention it, it does have an odd mix of Lisp-style bracket grouping and C-style dot notation. Whatever. It's code."

I come from a Java/object-oriented background, so aside from the syntax, the things about Erlang that are different are: immutable variables, the focus on simple data structures, and functional language features like recursion and closures. These aren't entirely separate things. They all work together and add up to a different way of solving problems.

A good example of this is the bowling game program. Those of you who saw my talk last year heard all about this, so let me just recap it quickly. It's a standard programming challenge: Calculate the score for a game in bowling. It's fairly straightforward, but there are a bunch of tricky edge cases. The first time I did it was in Python as a pair programming exercise, and at the end I was pretty happy with the results. It came out to 53 lines of code. Then about a year later, we did the same thing at one of the Erlang meetups, and the solution Rusty turned in was about ten lines of code. Ten lines of clean, elegant code, not like a Perl one-liner. That blew my mind.

I went back and looked at the Python code and realized how much of it was OO modeling that doesn't actually help solve the problem. In fact, it creates a bunch of its own problems. Obviously, you need a Game class and a Frame class, and the Game keeps a list of Frames. Then you very quickly get into all these metaphysical questions around whether a Frame should just be a dumb data holder, or whether it should be a fully self-actualized and empowered being, capable of accessing other frames to calculate its score and detect bad data. Putting all the smarts in the Game may be the easiest thing, but that brings up historical echoes of failed Soviet central planning, and just doesn't feel very OO. And once you've got these classes, you start speculating about possible features: What if you want to be able to query the Game for a list of all the rolls - does that change how you store that info? In short, you can get really wrapped around the axle with all these design issues.

The Erlang solution sidesteps that whole mess. It just maps input to output. The input is a list of numbers, the output is a single number. That sounds like some kind of fold function. With pattern matching, you write that as one function with four clauses: End of game, strike frame, spare frame, normal frame.

The thing is, once I'd seen the solution in Erlang, I was able to go back and implement it in Python; it came out to roughly the same number of lines of code, and was about as readable. That transfers, that way of solving problems. Instead of thinking, "What are the classes I need to model this problem domain?" start with, "What are my inputs and outputs? What's the end result I want, and what am I starting from? Can I do that with simple data structures?"

So now when I write Python code, I use list comprehensions a lot more; @for@ loops feel kinda sketchy - clumsy and error-prone. Modifying a variable instead of creating a new one sets off this tiny warning bell. I use annotations and lambda functions more often, and wish I had tail recursion and pattern matching. I do more with lists and dictionaries. Defining classes for everything now feels like boilerplate code.

In the last year, I've also done a bunch of rich browser client Javascript programming with jQuery and Backbone.js. That's a very functional style of programming. It's all widget callbacks and event handling - lots of closures. (I don't know who originally said it, but Javascript has been described as "Lisp with C syntax".) Actually, I was coding in Coffeescript and debugging in Javascript. Coffeescript is essentially a very concise and strongly functional macro language for generating Javascript. So it was a really good thing to have the experience with Erlang going into that.

The other little plug I'll make is for the Erlang community. It's still small enough to be awesome. Just lurk on the erlang-questions mailing list, and you can learn a ton. There are some really sharp people on it, and the discussions are a fascinating mix of academic and practical. You see threads that wander from theoretical computer science to implementation details to performance issues.

h2. What you need to wrap your head around

Ok, you're sold, you're going to sink a chunk of time into learning Erlang.
Again, Erlang is pretty foreign, especially if it's your first functional language.
It's not that it's really more complicated than other languages; it's just a different way of doing things.
So what are some of the stumbling blocks, the cultural differences that you need to wrap your head around?

h3. Syntax

I'll start with the syntax, which is probably the least significant weirdness, but it's the first thing that people tend to get hung up on. They look at Erlang code, and they're all like, "Where are the semicolons? What are all these commas doing here? Where are the curly braces?" It all seems bizarre and arbitrary. It's not. It's just not like C.

What helped me get used to Erlang's syntax was realizing that what it looks like is English. Erlang functions are like sentences: You have commas between lists of things, semicolons between clauses, and a period at the end. Header statements like @-module@ and @-define@ all express a complete thought, so they end with a period. A function definition is one big multi-line sentence. Each line within it ends with a comma, function clauses end with a semicolon, and there's a period at the end. @case@, @if@, and @fun@ blocks are like mini function definitions: They separate their conditions with semicolons and end with @end@. @end@ *is* the puctuation; you don't put another semicolon before it. After @end@, you put whatever punctuation would normally go there.

Here's a cheat sheet:
```erlang
go_shopping(hardware) ->
    hammer,
    nails;
go_shopping(groceries) ->
    cheese,
    case percent(milk) of
        0 -> "Bleah";
        2 -> "Just right";
        4 -> "Bleah"
    end,
    peanut_butter.
```

```erlang
-module(my_module).

my_func([]) ->
    Value = get_value(),
    Response = case other_func() of
        ok -> "We're good!";
        _ -> "Oh noes!"
    end,
    {Response, Value};
my_func([Value]) ->
    {"We're good!", Value};
my_func(Values) ->
    Fold = fun (X) ->
        Inc = X + 1,
        Inc * 2
    end,
    Value = lists:foldl(Fold, Values),
    {"We're good!", Value}.
```

h3. Recursion

Recursion is not something you use much in OO languages because (a) you rarely need to, and (b) it's scary - you have to be careful about how you modify your data structures. Recursive methods tend to have big warning comments, and nobody dares touch them. And this is self-reinforcing: Since it's not used much, it remains this scary, poorly-understood concept.

In Erlang, recursion takes the place of all of the looping constructs and iterators that you would use in an object-oriented (OO) language. Because it's used for everything, there are well-established patterns for writing recursive functions. Since you use it all the time, you get used to it.

This is also where Erlang's weirdnesses start working together. Immutable variables actually simplify recursion, because they force you to be clear about how you're changing your data at each step of the recursion. Pattern matching and guard expressions make recursion more powerful and expressive, because they let you break out the stages of a recursion in a very declarative way. Let's look at the basics of recursion with a very simple example: munging a list of data.

Like a story, a recursive function has a beginning, a middle, and an end. The beginning and end are usually the easiest parts, so let's tackle those first. The beginning of a recursion is just a function that takes the input, sets up any initial state, ouput accumulators, etc., and recurses. In this case, we take an input list and set up an empty output list.

```erlang
    %% beginning
    func(Input) ->
        Output = [],
        func(Input, Output).
```

The end stage is also easy to define. We pattern-match on an empty input list, and return our output list.

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

As a coda to this, it's worth mentioning that this is essentially what Erlang's @lists:map/2@ function does, so you could replace all the forgoing with something like:

```erlang
    lists:map(fun my_module:munge/1, Input)
```

The @lists@ module has a number of other functions for doing simple list munging like this.

h3. More OO than OO

The next thing is Erlang process spawning and inter-process communication. Again, this is one of those things that in normal languages is rarely used and fraught with peril. In Java, multithreaded applications involve a lot of painstaking synchronization, and you still often get bit by either concurrent modification errors or performance issues from overly aggressive locking. I haven't had to mess with it in Python, but it seems to be much the same, maybe worse.

In Erlang of course, you do it all the time. Understanding why requires a bit of background. The original concept of object oriented programming was that objects would be autonomous actors rather than just data structures. They interact with each other by sending messages back and forth. You see artifacts of this like Ruby's @send@ method.

In a sense, Erlang is more truly object oriented than OO languages, but you come to it by a roundabout way. Since even complex data structures are immutable, updating your data creates a new reference to it. So the only way to have something like global, mutable data is to have that reference owned by a single process and managed like so: 

```erlang
    loop(State) ->
        receive Message ->
            NewState = handle_message(Message, State),
            loop(NewState)
        end.
```

(You wouldn't literally have code like this, but it's effectively what you're doing.) @State@ is any data structure, from an integer to a nested tuple/list/dictionary structure. You'd spawn this loop function as a new process with its initial state data. From then on it would receive messages from other processes, update its state, maybe send a respose, and then recurse with the new state. The key here is that it's a local variable to this function; there's no way for any other process to mess with it directly. If you spawn another process with this function, it will have a separate copy of the @State@, and any updates it makes will be completely independent of this. The simplest example I can think of would be an auto-incrementing id generator:

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

So anything that would be an object in an OO language is a process in Erlang. I hadn't realized quite how true that was until I was messing around in the Erlang shell, and opened a file. @file:open/2@ says it returns @{ok, IoDevice}@ on success. Let's take a look at that:

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

As with recursion and the @lists@ module, Erlang's @gen_server@ module gives you a more formal and standard way to do this sort of thing. It add a lot of process management on top of this basic communication, so I won't get into the details here, but check it out.

h2. Getting experience

Ok, so once you've gotten past the language concepts, how can you actually get some practice with it? Something a little more low-key than massively distributed high-availability systems?

Probably the easiest way to start, if you just want to get comfortable with the language, is shell scripting. @escript@ lets you use Erlang as a scripting language.

```erlang
    #!/usr/local/bin/escript

    main(Args) ->
        io:format("Hello world!~n\t~p~n", [Args]).
```

Here's a simple way to grab web pages (like @curl@ but without all the options):

```erlang
#!/usr/local/bin/escript

main([Url]) ->
    inets:start(),
    {ok, {Status, _Header, Content}} = httpc:request(Url),
    io:format("Response (~p):~n~s~n", [Status, Content]).
```

In fact, looking at the @httpc:request@ docs, I think it'll let you do most of the things you can do with @curl@. Need to make an PUT request over SSL and set a cookie? No problem.
I started with this and built out an automated testing tool for a web service I was writing. 

You can do all sorts of little, useful things like this. They're a way to get used to Erlang's idioms, and you can gradually build in more complexity as you go. You have the ease of scripting, with full access to Erlang's libaries. Furthermore, you can set a name or sname in your script, and then it can connect to other Erlang nodes.

```erlang
    #!/usr/local/bin/escript
    %%! -sname my_script

    -mode(compile).  % to speed things up
```

```
	gaining experience
		use on personal projects
			simple web apps
				ChicagoBoss, nitrogen, spooky, webmachine
		non-critical apps
			escript
			testing tools: web services
			mock services
		read other people's source
			I've contributed *minor* patches to both Spooky and Erlydtl
			odds are you'll find something you need to fix or extend
		There are two sides to learning Erlang: the language itself, and building and deploying complex applications.
			Honestly, I haven't cracked the second half of that problem.
```


```erlang
score(Rolls) -> frame(1, 0, Rolls).

frame(11, Score, _BonusRolls) -> Score;

frame(Frame, Score, [10|Rest]) ->
    frame(Frame + 1, Score + 10 + strike_bonus(Rest), Rest);

frame(Frame, Score, [First,Second|Rest]) when (First + Second == 10) ->
    frame(Frame + 1, Score + 10 + spare_bonus(Rest), Rest);

frame(Frame, Score, [First,Second|Rest]) ->
    frame(Frame + 1, Score + First + Second, Rest);

spare_bonus([First|_Rest]) -> First.
strike_bonus([First,Second|_Rest]) -> First + Second.
```