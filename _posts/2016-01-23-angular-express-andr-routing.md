---
layout: post
title:  Angular Routing and Express Routing... How do they interact?
tags: [AngularJS, ExpressJS, Routing]
comments: true
share: false
---

If you're using Angular and Express, you've probably wondered how the routing configuration within Express works with the routing in Angular. Inside of your routing.js file, you may have a line that that looks like so: 
{% highlight js %}
app.use(express.static(path.join(__dirname, "./client)));
{% endhighlight %}

When your application loads, Express will return the static files contained in the directory specified above.You may be tempted to add some logic to tell Express to load your index.html file, but it's unnecessary.  
{% highlight js %}
app.get('*', function(req, res) {
  res.sendfile('./client/index.html'); 
});
{% endhighlight %}

Why? Angular will look within those static files for a the index.html file and will load that file, and use it as its base url. In other words, your base url implicitly looks like:
{% highlight js %}
http://thebestappever.com/index.html
{% endhighlight %}

Within your index.html file, you'll have a line that says: 
{% highlight js %}
<div ui-view></div>
{% endhighlight %}

Routes that you define on your $stateProvider will be attached to that base url, and rendered within the ui-view div: 
{% highlight js %}
http://thebestappever.com/index.html#aboutme
http://thebestappever.com/index.html#projects
{% endhighlight %}

When a user navigates to any of these routes with a #, it doesn't hit the server to ask for a file. Angular's ui-router will take a look at the portion after the #, and simply render the template you configured for that route. So if your configuration looks like: 
{% highlight js %}
$stateProvider
    .state('aboutme', {
      url:'/aboutme',
      templateUrl: '/views/aboutme.html'
    })
    .state('projects', {
      url:'/projects',
      templateUrl: '/views/projects.html'
    })
{% endhighlight %}

Navigating to
{% highlight js %}
http://thebestappever.com/index.html#aboutme
{% endhighlight %}
will render the aboutme.html template (within the static files). 







