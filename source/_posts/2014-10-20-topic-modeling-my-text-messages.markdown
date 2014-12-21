---
layout: post
title: "Topic Modeling My Text Messages"
date: 2014-10-20 07:48:26 -0400
comments: true
categories:
---
Today marks exactly one year since my lovely girlfriend and I began dating. In honor of that occasion (and inspired by posts from [Kana Abe](http://blog.kanaabe.com/blog/2014/10/16/the-nokogiri-saw/) and [Alice Zhao](http://adashofdata.com/2014/10/14/how-text-messages-change-from-dating-to-marriage/)), I decided to try building a [topic model](http://en.wikipedia.org/wiki/Topic_model) of all the text messages we've sent to each other using [Google Takeout](https://www.google.com/settings/takeout), Ruby, [Nokogiri](http://www.nokogiri.org/) (an HTML parser), and [MALLET](http://mallet.cs.umass.edu/) (a Java-based command line program for natural language processing). The idea was that I might be able to extract some insight about what we've been talking about during our relationship and how it has changed over time, much like Professor Robert K. Nelson did for the Civil War in his project [Mining the Dispatch](http://dsl.richmond.edu/dispatch/).

###Step One: Download Texts from Google Takeout
Since I use Google Voice for texting, my first step was to head over to [Google Takeout](https://www.google.com/settings/takeout) and download an archive of our texts. Google Takeout is a great feature that allows you to export data from most Google services, including Gmail, Calendar, Contacts, and Hangouts. For this project, however, I was only interested in texts, so I left all boxes unchecked except for Google Voice.

A few minutes later, I received a download link to a ZIP archive of all my Google Voice data. I unpacked the archive and found two files and two folders.

+ **Bills.html**: An HTML-format page detailing my Google Voice credit purchase history
+ **Phones.vcf**: A [vCard](http://en.wikipedia.org/wiki/VCard)-format archive of my Google Voice contacts
+ **Greetings**: A folder containing an mp3-format copy of my customized voicemail greeting
+ **Calls**: A folder containing HTML-format pages with details about all my calls and texts

For the purposes of this project, all I needed was the **Calls** folder. Since the texts themselves were somewhat oddly embedded in 246 HTML files that held as many as 100 texts each, my next step was to parse the HTML files. Enter Nokogiri...

###Step Two: Extract Text Messages from Archive
[Nokogiri](http://www.nokogiri.org/) is a Ruby gem that makes it easy to parse HTML and XML files into usable Ruby objects. In order to use it, I created a new Ruby file called `topic-modeling-texts.rb` and typed `require 'nokogiri'` on the first line. This code lets the Ruby interpreter know that it should load the files needed to make Nokogiri run.

Next, I looped through the **Calls** folder and built an array of individual text messages:

```ruby
require 'nokogiri'

messages = []

Dir.foreach("Voice/Calls/") do |file|
  if file.include?("Text") && (file.include?("5555551234") || file.include?("Jane Doe"))
    current_file = File.open("Voice/Calls/#{file}")
    page_to_parse = Nokogiri::HTML(current_file)
    current_file.close    
    page_to_parse.css("div.message q").each do |x|
      messages << x.content
    end
  end
end
```
In this code, I first create a `messages` array and set it equal to an empty array.

```ruby
messages = []
```

Then, I loop through the `Voice/Calls/` directory and look for files that include the word "Text" and my girlfriend's name or phone number (name and number changed to protect the innocent!).

```ruby
Dir.foreach("Voice/Calls/") do |file|
  if file.include?("Text") && (file.include?("5555551234") || file.include?("Jane Doe"))
```

Then, I open each matching file, assign it to a variable called `page_to_parse`, and tell Nokogiri to treat the object as HTML.

```ruby
    current_file = File.open("Voice/Calls/#{file}")
    page_to_parse = Nokogiri::HTML(current_file)
    current_file.close    

```
Finally, the magic happens: Nokogiri's CSS selectors automagically extract all the content between `<q>` tags in the `message` class (where the text messages happened to be stored in the HTML pages I downloaded from Google Takeout) and shovel each text message into my `messages` array.

```ruby
    page_to_parse.css("div.message q").each do |x|
      messages << x.content
    end
```
This left me with a nice array of 10,404 text messages. The next challenge was to convert those text messages into a format that MALLET understands.

###Step Three: Preparing the Text Messages for MALLET
According to [MALLET's website](http://mallet.cs.umass.edu/import.php), users can provide input to the program in two ways:

+ In a single text file, with each line corresponding to a different "document"
+ In multiple text files in a single directory, with each file corresponding to a different "document"

Since I was dealing with a large number of "documents" (text messages), I decided it would be simpler to go with the first option. Back to Ruby!

```ruby
File.open("mallet-2.0.7/texts.txt", "w+") do |x|
  messages.each_with_index do |message, index|
    x.puts("#{index} texts #{message}")
  end
end
```
In this code, I create a new text file called `texts.txt` in the same directory as the MALLET program files. Then, I iterate through the `messages` array and write each element to a new line in `texts.txt`, with an index number and the word "texts" appended to the front.

For example, a single line in `texts.txt` might read "83 texts How are you today?" MALLET requires the "83 texts" part because it needs an "instance name" (in this case, 83) and a "label" (in all these cases, "texts") to use in its output files. MALLET construes the remainder of each line as the actual content.

Once I had my `texts.txt` files prepared, it was time to fire up Terminal and begin interacting with MALLET.

###Step Four: Processing the Text Messages in MALLET
After [installing MALLET](http://mallet.cs.umass.edu/download.php), my first step in interacting with the program was to navigate to its directory.

```
cd mallet-2.0.7
```

Next, I had to import my `texts.txt` file and convert it into a form that MALLET can handle. I did this with the `import-file` command:

```
bin/mallet import-file --input texts.txt --output texts.mallet --keep-sequence --remove-stopwords
```
This command has several parameters:

+ `--input` specifies the input file, `texts.txt`
+ `--output` specifies the output file, `texts.mallet`
+ According to the MALLET website, `--keep-sequence` "preserves the document as a sequence of word features, rather than a vector of word feature counts." I don't really know what this means, but MALLET yelled at me when I tried to use a file imported without this parameter, so in it went!
+ `--remove-stopwords` filters the input file and removes common words, like "a," "an," "the," etc. This helps filter noise from the model.

After running the command, I ended up with a `texts.mallet` file, and I was ready to generate some models. I did this with the `train-topics` command:

```
bin/mallet train-topics --input texts.mallet --num-topics 25 --output-state texts-state.gz --output-topic-keys texts_keys.txt --output-doc-topics texts_compostion.txt
```

Again, this command includes several parameters:

+ `--input` specifies the input file
+ `--num-topics` specifies the number of topics MALLET should train. Which number is appropriate depends upon the input material. If the number 5 is used for a highly diverse set of documents, the resulting topics will be overly general and unlikely to correspond to well-defined real-world topics. By contrast, if the number 50 is used for a highly uniform set of documents, then MALLET will search for topics that aren't there and return a lot of "false positives" (topics that do not correspond to anything in the real world). I went with 25 for the initial run based entirely on gut feeling.
+ `--output-state` specifies the file in which MALLET should store a compressed text file containing all words in all documents along with their assignments to specific topics
+ `--output-topic-keys` specifies the file in which MALLET should store a text file that lists the top words for each topic
+ `--output-doc-topics` specifies the file in which MALLET should store a text file that describes the topic composition of each input document

I ran this command several times using different numbers for the `num-topics` parameter until the keywords looked like they aligned reasonably well with real world topics.

###Step Five: A Brief Analysis
In the end, 25 topics seemed to provide a decent balance between generality and specificity. In looking at the key words identified in the `text-keys.txt` file, I can see several topics that could plausibly correspond to real-world concepts. Here are the top words for a few sample topics:

+ **Career**: job school project team deloitte offer business high axiom company opportunity booz market afraid entire higher role firm consulting
+ **Plans with Friends**: people weekend travel agree friend making asked plans question forgot taylor made talked discuss questions friends apt date
+ **Everyday Planning and Coordination**: home back dinner call meet leave email place thinking eat leaving hey work food coming late office hour
+ **Travel**: bus interview sunday saturday afternoon dc monday ny prep care st city evening side airport uh final dryer ticket
+ **Budgeting and Investing**: number message basically books account service text texts suit costs gvoice takes silly lot homeless ira transfer set
+ **Sharing Links**: http www html amazon dp utm uber mobile youtube pe ref medium em articles nytimes utf ho strangers apps

In order to get these results, I ended up including another parameter called "hyperparameter optimization," which allows some topics to be more prominent than others and thus sometimes improves results.

###Conclusions
Although I had fun reliving the first year of my relationship through a computational lens, I think text messages are ultimately poorly suited to topic modeling. Having used MALLET in the past, I know the software achieves more compelling results with content like newspaper articles. My speculations about why that might be is perhaps a topic for another post!