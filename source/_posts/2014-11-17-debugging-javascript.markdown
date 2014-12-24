---
layout: post
title: "Debugging JavaScript"
date: 2014-11-17 23:29:52 -0500
comments: true
categories: 
---
##The Problem
Ever since my class at [The Flatiron School](http://flatironschool.com/) took a detour into JavaScript from Ruby and Rails, I've found it harder to debug my code. I think this has happened for a couple of reasons. First, JavaScript has a fussier syntax than Ruby, and, as a beginner, I don't always know where to put my semicolons, curly braces, `var` keywords, etc. Second, JavaScript is highly permissive, and, as a result, its error messages tend to be less helpful than those of Ruby.<!--more--> Take, for example, the following Ruby code:

```ruby
def my_method(arg1, arg2)
	arg1 + arg2
end

my_method("Hello world!")
```

If I type this into irb, I get the following error message:
```
ArgumentError: wrong number of arguments (1 for 2)
	from (irb):1:in `my_method'
	from (irb):5
	from /Users/don/.rvm/rubies/ruby-2.1.2/bin/irb:11:in `<main>'
```
This is a helpful error message. It tells me that I supplied the wrong number of arguments when I called `my_method` in line 5. Specifically, I supplied one argument where `my_method` requires two.

Contrast this with the equivalent in JavaScript:
```JavaScript
function myFunc(arg1, arg2) {
	return arg1 + arg2;
}

myFunc("Hello world!");
```
When I run this code, I don't get an error message. Instead, I get:
```
"hello worldundefined"
```
Huh?

As I wrote above, JavaScript is very permissive. If there's any way JavaScript can make do with what the programmer provides, JavaScript tries to do it. In this case, it simply inserted the default value `undefined` in place of the argument I failed to supply.

This aspect of JavaScript gives it the potential to be highly expressive, but it also complicates things for JavaScript novices. Once JavaScript programs become even mildly complex, small errors like the one above can easily break programs, and JavaScript does not offer much help in diagnosing the problem. At best, it supplies a cryptic error message like `'undefined' is not a function`; at worst, it cheerfully returns `undefined`, as though everything went according to plan.

Given these (seeming) issues, I decided to research tools that could help me debug my JavaScript code.

##Solutions
###Syntax
As I mentioned above, I think JavaScript's fussy syntax is one of the things that makes it difficult to debug. Especially for beginners, it's easy to break programs by mistakenly omitting a semicolon or committing some other seemingly minor transgression. Fortunately, this problem turns out to have an easy fix: [JSHint](http://jshint.com/). JSHint is a multi-platform tool that identifies syntax errors and other code quality issues in JavaScript code. Their web interface works quite well for casual experimentation, but their Sublime Text plugin (also available for [other platforms](http://www.jshint.com/install/)) is more practical for day-to-day use. Here's how I installed it (for Sublime Text 3):

1. **[Install Package Control](https://sublime.wbond.net/installation)**, which makes it easy to add and remove Sublime Text packages.
    - Copy the appropriate block of Python code from the [Package Control installation page](https://sublime.wbond.net/installation)
    - Switch from your web browser to Sublime Text
    - Press <code>ctrl + `</code> to bring up the Sublime Text console
    - Paste the Python code into the console and press enter
    - Restart Sublime Text
2. **[Install SublimeLinter](http://sublimelinter.readthedocs.org/en/latest/installation.html)**, a framework for language-specific packages that [lint](http://en.wikipedia.org/wiki/Lint_(software) code within Sublime Text.
    - In Sublime Text, press `command + shift + p` to bring up the command palette
    - Begin typing "Package Control: Install Package", and select that option once it's visible
    - Begin typing "SublimeLinter", and select that option once it's visible
    - Restart Sublime Text
3. **[Install Node.js](http://nodejs.org/)**, a run-time environment for JavaScript. The easiest way is to click "Install" button on the homepage and then follow the installer's instructions.
4. **[Install JSHint](http://jshint.com/)** for Node.js using npm (Node Package Manager).
    - Open Terminal and type `npm install -g jshint` (the -g flag makes JSHint available "globally" - i.e., in all directories on your computer)
    - If this fails (as it did on my computer), issue the same command with elevated permissions: `sudo npm install -g jshint`
5. **[Install SublimeLinter-jshint](https://github.com/SublimeLinter/SublimeLinter-jshint)**, which allows SublimeLinter to work with JavaScript code.
    - In Sublime Text, press `command + shift + p` to bring up the command palette
    - Begin typing "Package Control: Install Package", and select that option once it's visible
    - Begin typing "SublimeLinter-jshint", and select that option once it's visible
    - Restart Sublime Text

After completing these steps, SublimeLinter-jshint should automatically point out JavaScript syntax errors in Sublime Text.

###Debugging
Having eliminated one class of errors (syntax errors), I turned my attention to finding JavaScript equivalents of [irb](http://en.wikipedia.org/wiki/Interactive_Ruby_Shell), a command-line tool that makes it easy to test fragments of Ruby code, and [pry](https://github.com/pry/pry/wiki), a tool that allows programmers to step through Ruby code at specified breakpoints in a program.

####Replacing irb
irb is the easier of the two tools to replace. Most Mac users probably already have two tools installed that provide similar functionality.

#####jsc
The first is **[jsc](https://trac.webkit.org/wiki/JSC)**, which provides an irb-like interface in the terminal. It actually comes pre-installed on OS X, although it's not in your path by default. To add it to your path, simply open Terminal and run the following command from any directory:
```
sudo ln -s /System/Library/Frameworks/JavaScriptCore.framework/Versions/Current/Resources/jsc /bin/jsc
```
Then, you can launch jsc by typing `jsc` in Terminal. jsc is good for investigating small JavaScript quirks (e.g., what does `typeof(null)` return?), but in my experience it doesn't accept multi-line blocks with the same ease as irb. As a result, you can't simply copy your code from a text editor and run it in jsc. This makes it a non-ideal replacement for irb.

#####Chrome DevTools
Fortunately, many Mac users already have another JavaScript tool installed that handles multi-line pasting with ease: [Chrome DevTools](https://developer.chrome.com/devtools), "a set of web authoring and debugging tools built into Google Chrome." DevTools can be launched by pressing `command + option + j` from within Google Chrome.

{% img center /images/debugging-javascript/chromedevtools.png %}

Chrome DevTools are amazing, and I'm confident I'll find myself using them more as I gain experience. As a novice, however, they feel mildly like overkill in situations when I simply want to test a single function or chunk of code. In addition, using DevTools for small tests requires me to copy code from my text editor and paste it into DevTools (or else edit my code directly in DevTools, missing out on JSHint, syntax highlighting, and other Sublime Text goodness). This takes time and can introduce additional errors (e.g., forgetting to copy a variable declaration that is relied upon in the code I do copy). For these reasons, I decided to look for something simpler.

#####Creating a JavaScript Console in Sublime Text
I ended up finding the answer on [wikihow.com](http://www.wikihow.com/Create-a-Javascript-Console-in-Sublime-Text), of all places. In the article, user Anson S describes a process for writing a Sublime Text plugin that uses Node.js (which we installed earlier) to build and run JavaScript code directly in Sublime Text. Assuming you already have Node.js installed, the process is actually pretty simple:

1. Go to `Tools > Build System > New Build System...` in Sublime Text 3 (this may or may not work in Sublime Text 2).

2. In the new tab opened by Sublime Text, paste the following code:
        ```
        {
        "cmd": ["node", "$file"],
        "selector": "source.js"
        }
        ```

3. Press `ctrl + s` and save the file as `node.sublime-build` in the folder that Sublime Text brings up by default.

Then, when you have a JavaScript file open in Sublime Text and want to run it, you can just press `command + b`. (If this doesn't work, make sure the sublime-build you just created is selected by going to `Tools > Build System` and selecting `node`). Sublime Text will build the code using Node.js and then display the result in a small window at the bottom of the editor.

{% img center /images/debugging-javascript/js_console_in_sublime_text.png %}

####Replacing pry
As far as I've been able to tell, replacing pry is a bit more difficult than replacing irb. The best tool I've come across is in - surprise! - DevTools, specifically under the `Sources` tab. If you open DevTools on a page with the script you want to inspect, you can use the `Sources` tab to step through and debug the code. As in pry, this allows you to pause execution at specified breakpoints in a program and investigate the environment, including, e.g., the current values of all variables in scope. The main drawback of this function of DevTools is that it's somewhat annoying to get a one-off script (i.e., a script not yet integrated into a minimally functioning webpage) loaded into the tool. As far as I can tell, you can't just open a local file and use the tool on it.

There is a kind of hacky way to sidestep this issue: simply save your JavaScript program as an HTML file, and then enclose all of the JavaScript code between `<script>` tags:

```
<script>

function myFunc(){
  var test1 = "Hello world!";
  debugger;
  var test2 = "Goodbye world!";
}

myFunc();

</script>
```
Then, you can open the file in Chrome, press `command + option + j` to open DevTools, click on the `Sources` tab, and press `command + r` to refresh and inspect your code.

{% img center /images/debugging-javascript/devtools_sources.png %}

In this example, we can see that the `debugger` statement acts as a breakpoint - code execution is paused, and we can examine the current state of each variable under the "Scope Variables" window. Right now, `test1` has a value of "Hello world!", but `test2` is still `undefined` because the `debugger` statement paused code execution. If we click twice the button to step into the next function call...

{% img center /images/debugging-javascript/devtools_sources_button.png %}

...we'll see that `test2` is now assigned the value of `"Goodbye world!"`.

{% img center /images/debugging-javascript/devtools_sources_2.png %}

If you don't like inserting `debugger` statements into your code, you can also create a breakpoint by clicking the left margin of the center pane in the DevTools debugger, which will create a blue arrow indicating a breakpoint at the line selected.

Eventually, I'd like to encapsulate this process into a Sublime Text plugin that, when prompted with an appropriate keyboard shortcut, automatically wraps a `*.js` file being edited in HTML and launches it in a browser for debugging. For now, though, the manual strategy should save me some frustration!