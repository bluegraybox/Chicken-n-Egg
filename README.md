# Amateur Erlang

Another introductory presentation on Erlang, focusing on programming idioms, thinking in Erlang, and getting started with simple projects.

It was originally called "Chicken-n-Egg", and the focus was how to get around the catch-22 of needing Erlang experience to get an Erlang job, but needing the job to get the experience; and the related circular problem of companies not wanting to do projects in Erlang until they have experienced Erlang developers. Eventually I realized that that was a downer of a way to start the talk, and really, it's beside the point. Even if I never use Erlang professionally, I'll consider the time I've spent learning it time well spent. It's been flat-out fun, and what I've learned has been useful in other languages. That's really what I want to get across. So I rewrote the intro and renamed the talk. The core content is the same; it's just the framing that shifted.

## Tools

Presented using S9/Slideshow.

To install S9/Slideshow and the S6 theme:

    sudo gem install slideshow
    slideshow -f http://github.com/geraldb/slideshow-s6-syntax-highlighter/raw/master/s6syntax.txt

To generate the slides:

    slideshow -t s6syntax.txt slides

This will generate a slides.html file which you can just open in a browser.
