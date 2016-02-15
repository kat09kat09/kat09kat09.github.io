---
layout: post
title: Deploying on Heroku, With MongoLabs
excerpt: "Just about everything you'll need to style in the theme: headings, paragraphs, blockquotes, tables, code blocks, and more."
modified: 2016-01-10
tags: [deployment, mongoose, mongoDB]
comments: true
share: false
image:
  feature: sample-image-5.jpg
  credit: WeGraphics
  creditlink: http://wegraphics.net/downloads/free-ultimate-blurred-background-pack/
---

Are you are looking to deploy your app and have no idea where to start? Are you using MongooseJS? You're in luck! This article will provide a step by step guide that will make your deployment process smooth and painless. 

## Add mongolabs to your heroku app


Install <span><a href="https://toolbelt.heroku.com/">Heroku Toolbelt</a></span> 

Log into Heroku

{% highlight bash %}
heroku login
{% endhighlight %}

Add MongoLab as a Heroku Add-On
This is the free sandbox plan: 
{% highlight bash %}
heroku addons:create mongolab
{% endhighlight %}

Your Heroku environment now contains an additional config variable: MONGOLAB_URI
Check it out:
{% highlight bash %}
heroku config | grep MONGOLAB_URI
{% endhighlight %}

##Prep your server file for deployment

Find the file where you defined your PORT and MONGOOSE connection. In my case it's in this file: 
{% highlight js %}
server.js
{% endhighlight %}

Change your port to listen to the process.env PORT
{% highlight js %}
var port= process.env.PORT || 3000;
{% endhighlight %}

Change your mongoose connection to the process.env MONGOLAB_URI
{% highlight js %}
mongoose.connect("process.env.MONGOLAB_URI")
{% endhighlight %}

## Setup the dependencies

Now let's make sure you have a file that declares your app dependencies. Check if you have a file called:
{% highlight js %}
package.json
{% endhighlight %}

If you don't, run: 
{% highlight js %}
cd 
npm init
{% endhighlight %}

Install and save your dependencies using:
{% highlight js %}
npm install <pkg> --save
{% endhighlight %}

See all your missing dependencies by running:
{% highlight js %}
rm -rf node_modules
npm install --production
heroku local web
{% endhighlight %}

Now, find your version of node:
{% highlight js %}
node --version  
{% endhighlight %}

Add node under the "engines" property in your package.json
{% highlight js %}
"engines": {
  "node": "<your version number here>"
}
{% endhighlight %}

#Setup the Procfile

The next step is to create a Procfile. Heroku will use your Procfile to start your app. Check to see that you have a file called:

{% highlight js %}
Procfile
{% endhighlight %}
Inside your procfile, specify the file that contains your server logic: 
{% highlight js %}
web: node server.js
{% endhighlight %}
Let's check to see if your heroku app will run correctly locally: 
{% highlight js %}
npm install
heroku local web
{% endhighlight %}

#Deploy to Heroku
Ok final step! Let's deploy your app to Heroku!
You must commit your changes to git first: 

{% highlight js %}
git add <your files>
git commit 
{% endhighlight %}

Log into heroku: 
{% highlight js %}
heroku login
{% endhighlight %}

Create your app with a name (you can change this name later): 
{% highlight js %}
heroku create <your app name>
{% endhighlight %}

Push to heroku master:
{% highlight js %}
git push heroku master
{% endhighlight %}

See your app in the browser:
{% highlight js %}
heroku open
{% endhighlight %}

Congrats! You have deployed your application to Heroku!

