---
layout: post
title:  Representing Users and Friends in Mongoose
tags: [MongooseJS, MongoDB]
comments: true
share: false
---

Have you reached the point where you need to add the ability for users to friend each other in your application? In this post, we'll go over how to implement the schema for friends in MongooseJS. Let's say you have a barebones User schema that looks something like this: 
{% highlight js %}
var UserSchema = new mongoose.Schema({
  // _id:    mongo creates this
  username: String
});
{% endhighlight %}

What if you wanted to add friends? Friends are users and users can have many friends. Does thinking about that make your head hurt? Well, today's your lucky day! We're going to go over how to establish a simple friend relationship in Mongoose. To be able to keep track of a user's friends, you would simply add a reference to the user model: 
{% highlight js %}
var UserSchema = new mongoose.Schema({
  username: String,
  friends:  [{ type: mongoose.Schema.Types.ObjectId, ref: 'User' }]
});
{% endhighlight %}

The friends field in the User model is an array of User ids. Adding a friend to a particular user is straightforward. 

{% highlight js %}
//Find the user in question
User.findOne(userId)
    .then(function (dbResults) {  
      //add the friend's id to the user's friends field
      dbResults.friends.push(friendId);
      //save the changes 
      dbResults.save(callbackFunction);  
{% endhighlight %}

Removing a friend follows a similar pattern.
{% highlight js %}
//Find the user in question
User.find(userId) 
    .then(function (dbResults) {
      //remove the friend's id from the user's friends field
      dbResults[0].friends.pull(friendId); 
      //save the changes
      dbResults[0].save(callbackFunction)
    })
{% endhighlight %}


There you have it! You've now successfully represented the relationship between a user and his/her friends. 


