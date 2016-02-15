---
layout: post
title:  Debugging a BookshelfJS Error On Many To Many Model Definition
tags: [MySQL, BookshelfJS]
comments: true
share: false
---


Let's say you're setting up the schema for a many-to-many relationship. If you're here, you've probably encountered this error: 

{% highlight js %}
Unhandled rejection Error: A valid target model must be defined for the performances belongsToMany relation
{% endhighlight %}

What exactly is causing this error? In the following example, we'll explore this problem further. Let's say we have a many-to-many relationship between *Performances* and *Tags*. 

Here we setup our Performance model: 

{% highlight js %}
//performance.js
var db = require('../config');
var Tag = require('./tag');

var Performance = db.Model.extend({
  ... 
  tags: function() {
    return this.belongsToMany(Tag); 
  },
  ...
});
module.exports = Performance;
{% endhighlight %}

And here we setup our Tag model:

{% highlight js %}
//tag.js
var db = require('../config');
var Performance = require('./performance');

var Tag = db.Model.extend({
  ...
  performances: function() {
     return this.belongsToMany(Performance);
  }
  ...
});
module.exports = Tag;
{% endhighlight %}


What we have here, is a circular reference. The *performance.js* file depends on the *tag.js* file, and vice versa. That's causing the error. 

The quick fix is simply to pass in the dependency directly into the *this.belongsToMany*  function. 

{% highlight js %}
//performance.js
var db = require('../config');
//var Tag = require('./tag');

var Performance = db.Model.extend({
  ... 
  tags: function() {
    //return this.belongsToMany(Tag); 
    return this.belongsToMany(require('./tag')); 
  },
  ...
});
module.exports = Performance;
{% endhighlight %}

And here we setup our Tag model:

{% highlight js %}
//tag.js
var db = require('../config');
//var Performance = require('./performance');

var Tag = db.Model.extend({
  ...
  performances: function() {
     return this.belongsToMany(require('./performance'));
  }
  ...
});
module.exports = Tag;
{% endhighlight %}

Voila! Error fixed! 

P.S. It's worth checking out the <a href= "https://github.com/tgriesser/bookshelf/wiki/Plugin:-Model-Registry">Registry Plugin</a> for Bookshelf.js. Here is a brief description from Bookshelf.js website: 

> Register models in a central location so that you can refer to them using a string in relations instead of having to require it every time. Helps deal with the challenges of circular module dependencies in Node.



