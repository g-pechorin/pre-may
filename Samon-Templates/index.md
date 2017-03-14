---
title: Samon Templates
date: 2016-03-12
---

[Samon][hgSamon] is the name I've given my-very-own [static text-template engine][wikiTemplateProcessor].
I had been working with [Jamon][orgJamon] but kept running into obstacles related to my reliance on Scala features.

# Background

(What I call) [Text-Template Engines][wikiTemplateProcessor] solve a pretty simple problem with to "buts"

* the problem isn't "problematic" in simple applications
* you need some basic programming knowledge before they make sense

Confused? Terrified?

Let's say that we have "SomeThing" with a `name` that's a `String` and a `count` (of sub-things) that's an `Int`.
Naturally - you'd use a class for this which I'll express in Scala;

	class SomeThing
	(
		val name: String,
		val cout: Int
	)

  

You could write the equivalent class as C++ like this;


	class SomeThing
	{
	public:
		const std::string _name;
		const int _count;
		
		...
	}


... but that gets looooong and boring.
So I'm sticking with Scala.

I want to turn instance of this `SomeThing` into some HTML.
(Peter doesn't care much about HTML, but it's an easy example)


	...
	val someThing: SomeThing = ...
	var htmlString = ""
	
	htmlString = htmlString + "<p>Name:<b>" + someThing.name + "</b>"
	htmlString = htmlString + "<ul>"
	for(i <- (0 until someThing.count))
	{
		htmlString = htmlString + "<li>" + i + "</li>"
	}
	htmlString = htmlString + "</ul></p>"
	...


That's ... not ... not too horrid.
I mean - it's horrid but for reasons I'll explain.

But if this is all your project needs - fine, you're done; get on with your life.
But what if the count isn't "enough" for you?
Now you need SubThing's labels printed out instead of a number?

	class SomeThing
	(
		val name: String,
		val subThings: Array[SubThing]
	)


	...
	val someThing: SomeThing = ...
	var htmlString = ""
	
	htmlString = htmlString + "<p>Name:<b>" + someThing.name + "</b>"
	htmlString = htmlString + "<ul>"
	someThing.subThings.foreach {
		case subThing =>
			htmlString = htmlString + "<li>" + subThing.label + "</li>"
	}
	htmlString = htmlString + "</ul></p>"
	...





```
$def someThing: SomeThing 
<p>Name:<b>$( someThing.name )</b>
<ul>
$each someThing.subThings
$case subThing
<li>$( subThing.label )</li>
$end
```

# Shootout

Several Scala-based template engines exist.
They are more mature and offer better tool support than mine.
Mine, however, is

* statically typed and compiled - performance advantage!
* predictable W.R.T. line spacing - aesthetic advantage!


I am disinclined to use dynamically typed things.
When emitting source-code, it's quite nice to get predictable line spacing since it makes comparisons to expectations easier.

Several tools exist, but either don't handle Scala's `match` or insist on writing out XML tags - no good when you want to emit C++/GLSL/Scala source-code.
SSP and Twirl both handled `match` and let me emit text.
I worked with them long enough to adopt them before losing my patience.
When an SSP template was loaded, it had to be compiled then and there.
With several SSP templates several copies of the Scala compiler had to be spun up and set loose on these dynamic constructs.
SSP's line endings were all over the place.
Twirl frustrated me in the same way - slow re-testing made development work hard.

Spending 65 seconds waiting for a UnitTest to re-run really cuts into your development time.

Getting the same results in 4 seconds (or less if using SBT's daemon) is effectively uninterrupted.
I translate a template file into lines, arrange those lines into some trees, then write a `.scala` file based on those definitions which outputs something meaningful.
I used [Li Haoyi][ioLiHaoyi]'s [FastParse][ioFastParse] for digesting lines themselves - I'm eager to use this tool on more projects.
The whole process is pretty quick and SBT's incremental compiler produces a result from there.
I've packed my stuff into an SBT plugin and attached it to the `compile` thing; so it's ID-10T compliant.

# Results

The driving use was my [ScaD40][hgScaD40] project.
A usage  [example](https://bitbucket.org/g-pechorin/dukscala/src/7d4a3fdfcfb5e9dba8c3cbe8ba158b21371bd48b/scad40/src/main/samon/com/peterlavalle/SJS.samon?at=diskio&fileviewer=file-view-default) is a 77 line file capable of transforming a case-class/construct-object into a Scala-JS source module.
(At the time of this writing - the example has at least one missing case I need to fix)
The files (which I write) tend to be mostly control code.

My original design was pretty much a translation of Jamon.
The current iteration was supposed to be stateless and accept only a single object as a template attribute.
This has ... sort of fallen away - it's not interesting to me anymore.
I made `sub` templates and `alt` templates with semantics related to how they handled the parameters of whatever they were inside.
I keep all control expressions on their own lines (always!) which drastically simplifies the process of parsing and constructing the templates.
Text lines (or whatever we're calling the write-stuff-out bits) use simple substitutions reminiscent of Scala's string interpolation.
To avoid any need for parsing Scala - I made `${}` `$<>` and `$()` work as substitution delimiters.
Thus far I haven't needed to use all three symbols in the "insert text here" chunks.
If I had the time, I'd like to add a `#pragma` like directive to allow swapping which char to use for `$` and support `$[]` and `$//` as delimiters.

Can it help you?

[ioLiHaoyi]: http://www.lihaoyi.com/
[hgSamon]: https://bitbucket.org/g-pechorin/samon2
[orgJamon]: http://www.jamon.org/
[ioFastParse]: https://lihaoyi.github.io/fastparse/
[wikiTemplateProcessor]: https://en.wikipedia.org/wiki/Template_processor
