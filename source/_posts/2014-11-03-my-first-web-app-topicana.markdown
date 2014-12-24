---
layout: post
title: "My First Web App: Topicana"
date: 2014-11-03 23:57:35 -0500
comments: true
categories: 
---
##Introduction
After writing my [last post](http://doneallison.com/blog/2014/10/20/topic-modeling-my-text-messages/), I wanted to explore topic modeling and the [MAchine Learning for LanguagE Toolkit (MALLET)](http://mallet.cs.umass.edu/) a bit further. Specifically, I wanted to explore the possibility of liberating MALLET from several usability issues.<!--more--> Here are a few of the issues I had in mind:

+ **Preparing Data**: It takes a long time to convert source documents into a format that MALLET can understand and analyze, and the transformations are amenable to automation.

+ **Environment Setup**: Setting up MALLET to work properly can be a fussy process, especially for Windows users.

+ **Clunky CLI**: Although MALLET does have an independently developed [GUI](https://code.google.com/p/topic-modeling-tool/), most people interact with MALLET through its somewhat clunky CLI. For certain advanced features, the CLI is the only way to interact with MALLET.

+ **Files, files, and more files**: The only output MALLET provides are large text files that are difficult to evaluate quickly and intuitively. As a result, many users end up importing MALLET output files into Excel _every time_ they train a topic model in order to evaluate the suitability of the model. This long feedback cycle aligns poorly with the iterative, intuitive process required to identify a good topic model.

Since I had been learning Sinatra at [The Flatiron School](http://flatironschool.com/), I decided to try coding a simple, incomplete solution to these problems. Being a sucker for puns, I called that solution [Topicana](https://topicana.herokuapp.com/).

##A Brief Introduction to Topicana
Topicana is a simple web form that allows users to extract topics from collections of documents. Users can either provide their own documents (in the form of plaintext or URLs) or ask Topicana to retrieve documents based on keywords. Topicana also allows users to choose whether to enable [hyperparameter optimization](http://en.wikipedia.org/wiki/Hyperparameter_optimization) and [stopword removal](http://en.wikipedia.org/wiki/Stop_words).

When a user submits a query, Topicana processes the documents provided into MALLET's internal format and then navigates the MALLET CLI to train a topic model based on those documents and other parameters provided by the user. Once MALLET finishes training the topic model, Topicana converts its raw output into simple tables. One table shows the top keywords in each topic identified, while multiple tables show the topic composition of each article provided to Topicana.

Although Topicana is based on and ultimately reducible to MALLET, it relies on several technologies to automate the tasks of retrieving, parsing, and preparing inputs and formatting and displaying outputs:

+ **Nokogiri**: Nokogiri parses content retrieved from URLs entered by users.

+ **[webhose.io](https://webhose.io/)**: At the moment, Topicana uses webhose.io to retrieve content relevant to keywords provided by users. In the future, I'll consider other services such as the [Google Custom Search API](https://developers.google.com/custom-search/json-api/v1/overview), the [Bing Web Search API](https://datamarket.azure.com/dataset/8818F55E-2FE5-4CE3-A617-0B8BA8419F65), and the [Faroo Free Search API](http://www.faroo.com/hp/api/api.html) (1,000,000 free transactions each month!), which require a bit more configuration.

+ **Sinatra**: Sinatra is a lightweight alternative to heavier frameworks like Rails.

+ **Heroku**: Heroku is a service that allows push-button deployment of web apps directly from Git. After a small amount of setup, you can deploy to Heroku by typing `git push heroku master` from the command line. Amazing!

##Next Steps
Although Topicana is very basic and still unstable, I was excited to get a proof of concept working. I think algorithms based on topic modeling have the potential to help researchers and others navigate, organize, and consume information more efficiently. In the future, I can imagine extending Topicana in a number of ways:

+ **Improve Stability and Scalability**: Right now, Topicana is kind of shaky on Heroku - sometimes it works, sometimes it doesn't. I suspect at least part of the problem is that I haven't yet implemented background processes, which means that all the text processing takes place within a single request/response cycle. That's frankly pretty nutty and would be one of the first things I'd want to fix. I'd also want to experiment with different APIs to find the best source from which to fetch content.

+ **Add Persistence**: Topicana does not currently remember anything from one request to the next. I'd like for users to be able to save past searches and perhaps to look at them side-by-side with current searches. I think this could help users determine the sort of model that works best for their data.

+ **Improve Appearance/UI**: Topicana does not even have basic CSS yet, and I'd at least like to add some basic styling.

+ **Easy File Import**: Eventually, I'd like Topicana to have the ability to directly ingest mixed collections of DOC, TXT, PDF, and other files. I'd also like to write a Chrome extension or bookmarklet that users can push to save a page for later processing.