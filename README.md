#redwrap

##Description

Redwrap is a node.js module that simplifies Reddit API requests through jquery style chaining methods.  

##How to use

Start by requiring the redwrap module

```javascript
reddit = require('redwrap');
```

This gives us access to 3 types of requests. 

###1.The basic user request

Redwrap uses the request module to issue requests behind the scenes.  If you are familiar with request, you will notice the arguments for all of our callbacks in redwrap are the same.

```javascript
reddit.user('username', function(err, res, body){
  console.log(body); //outputs json for the requested username
});
```

###2.The basic subreddit request


```javascript
reddit.r('WTF', function(err, res, body){
  console.log(body); //outputs json for first page of WTF subreddit
});	
```

###3.The basic list request

With the list method the first argument is optional, and we will see why in a moment, but let's include one for now.

```javascript
reddit.list('hot', function(err, res, body){
	console.log(body); //json for the front page of reddit w/ 'hot' filter
});
```

That's cool, but we can make it even easier by chaining other methods onto our request.  So let's look at how to format our previous request, using chaining this time.

```javascript
reddit.list().hot().exe(function(err, res, body){
	console.log(body);
});
```
Take note of the .exe() method.  This is an optional method that can be placed at the end of our chain which takes a callback as its only argument.  You might use this as a way of breaking up long chains into a more readable structure.  For shorter chains, we can just add our callback to the last method in the chain.

###Filters

Filters in redwrap are methods we can chain to our requests.  These filters correspond exactly with the filters you see on the reddit site. In the last example, we saw our first use of a filter, when we chained .hot() to our request.  Below is a list of possible filters.  You can find 


User filters
 
* overview
* comments
* submitted
* liked
* disliked
* hidden
* saved
* about
 
Subreddit filters

* hot
* new
* controversial
* top

###Queries

Queries are applied to our request chain in the same way filters are.  They allow us to make a more targeted request from the Reddit API.  The main difference between filters and queries is that queries require an argument when called. In this next example we will be requesting the 'top' '100' comments from this 'year', for the given username.

```javascript
reddit.user('username').sort('top').from('year').limit(100, function(err, res, body){
	console.log(body); //top 100 comments this year for username
});
```
Here is a list of the possible queries along with acceptable arguments. You can learn more about the arguments from the Reddit API documentation.

* sort() 
	Possible arguments: 'hot', 'top', 'new', 'controversial' 
* from()
	Possible arguments: 'all','day','week','month','year'
* limit()
	Possible arguments: Any integer from 1 - 100;
* after()
	Possible arguments: a post ID
* before()
	Possible arguments: a post ID
* count()
	Possible arguments: An integer from 1 - 100;

###Advanced - Making multiple requests with the .all() method.

**Use this at your own risk** 

Before using this feature it is important that you be aware of the rules found in the Reddit API documentation.  Currently, there is no throttle on the number of requests per min the .all()  method makes.  It will continue to make requests until it completes, or until Reddit cuts you off. Also note that the default limit is set to 100.  You can change this by adding your own limit query to the request chain, but it isn't recommended.  I would like to expand on this feature in the near future, so please, if you have any feature requests let me know.

Here is the basic pattern for making multiple requests with redwrap.

```javascript

reddit.user('username').comments().sort('top').all(function(res) {

	res.on('data', function(data) {
		console.log(data);
	});
	res.on('error', function(e) {
		console.log(e);
	});
	res.on('end', function(data){
		console.log(data);
	});
	
});

```

If you have experience with the http module in node, then this should look very familiar.  When you use the .all method, it passes back an event emitter object that you can attach your event listeners to. The standard data, error, and end events are possible. (The 'end' event is actually bugged at the moment. If you have a fix let me know.)

That's all for now. My goal is to expand the features of redwrap to cover as much of the Reddit API as is needed by devs, so let me know if there is a feature you would like to see included. Enjoy!
