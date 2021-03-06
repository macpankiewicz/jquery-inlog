jQuery-inlog (v 1.0.0)
======

See what your jQuery code does inside like a boss.
jQuery-inlog is a passive plugin, which injects (js ftw!) console.log calls inside your jQuery core.
It makes debugging of selectors and chains a breeze. No more alert($obj.length) and stuff...

I call it black-box magic. Or short: black magic. Or even shorter: js

The original idea was taken from https://github.com/fmsf/jQueryLog

Works in any Browser with a decent console (Opera for example doesn't know about "%o" format string).
I personally think the output reads best on Firebug.


If you use jQuery-inlog I'd be very interested to hear from you! https://twitter.com/Prinzhorn


Example
========

Imagine the following markup

```html
<body>
	<div class="bar">
		<div class="bar">
			<div id="foo"></div>
		</div>
	</div>
	<div id="bacon"></div>
</body>
```

and the following jQuery calls

```javascript
$l(true);
$("#foo").parents(".bar").next().prev().parent().fadeOut();
$l(false);
```

will result in the following output with default settings

```
$("#foo") ↷ [div#foo]
parents(".bar") ↷ [div.bar, div.bar]
next() ↷ [div#bacon]
prev() ↷ [div.bar]
parent() ↷ [body]
fadeOut() ↷ [body]
```

or if you want to know what's really happening, set ```maxDepth: -1``` (last branch expanded exemplarily)

```
+ $("#foo") ↷ [div#foo]
+ parents(".bar") ↷ [div.bar, div.bar]
+ next() ↷ [div#bacon]
+ prev() ↷ [div.bar]
+ parent() ↷ [body]
- fadeOut() ↷ [body]
	- animate(Object { opacity="hide"}) ↷ [body]
		- queue("fx", g()) ↷ [body]
			- each(function()) ↷ [body]
				- is(":hidden") ↷ false
				- matchesSelector(<body style="opacity: 0.920047;">, ":hidden") ↷ false
					  isXML(<body style="opacity: 0.920047;">) ↷ false
					  isXML(Document index.html) ↷ false
					- filter(":hidden", [body]) ↷ []
						isXML(<body style="opacity: 0.920047;">) ↷ false
```

or even verbose with ```thisValue: true```

```
+ (Window index.html).$("#foo") ↷ [div#foo]
+ ([div#foo]).parents(".bar") ↷ [div.bar, div.bar]
+ ([div.bar, div.bar]).next() ↷ [div#bacon]
+ ([div#bacon]).prev() ↷ [div.bar]
+ ([div.bar]).parent() ↷ [body]
- ([body]).fadeOut() ↷ [body]
	- ([body]).animate(Object { opacity="hide"}) ↷ [body]
		- ([body]).queue("fx", g()) ↷ [body]
			- ([body]).each(function()) ↷ [body]
				- ([body]).is(":hidden") ↷ false
				- (function()).matchesSelector(<body style="opacity: 0.920047;">, ":hidden") ↷ false
					  (function()).isXML(<body style="opacity: 0.920047;">) ↷ false
					  (function()).isXML(Document index.html) ↷ false
					- (function()).filter(":hidden", [body]) ↷ []
						(function()).isXML(<body style="opacity: 0.920047;">) ↷ false
```

or if you're hardcore, you can set ```rawOutput: true``` to get the raw stackt race object created by the plugin

```
arguments
	[".bar"]

function
	"parents"

this
	[div#foo]

sub
	[Object { function="matches", arguments=[2], sub=[2], more...}, Object { function="pushStack", this=[1], arguments=[3], more...}]

return
	[div.bar, div.bar]
```


Documentation / Reference
========

Include jquery.inlog.js after jQuery core and enable it by calling $l(true) or $l(options).

Note: ```$l``` is an alias for ```jQuery.inlog```. If for any reason window.$l is already defined, it won't be overwritten.

Options:

```
var defaults = {
	enabled: false,//Enable logging
	thisValue: false,//Output this-value
	returnValue: true,//Output return-value
	indent: true,//Indent nested calls (makes sense for maxDepth !== 0)
	maxDepth: 0,//Max depth of nested calls
	rawOutput: false//If true, the raw stack trace objects will be printed (thisValue, returnValue and indent are all included for free)
};
```
