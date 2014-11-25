---
layout: post
title:  “Retrieving data from a server with React and Flux”
date:   2014-11-25 11:33:00
categories: react, flux, ajax, javascript
---

I recently converted a React application to use the Flux architecture pattern. Most of the pieces are well documented.  However, it can be tricky to know where to do any communication with the server (ajax, websockets, etc.).  I thought that I would outline the strategy that I've been using as it's been working fairly well.  For all of these examples I’m going to be using reflux.  However, it should be relatively easy to translate them into whatever Flux implementation that you like.  Also worth noting is that this technique is likely to change with the forthcoming updates to the react-router.

## Example: The Blog

We’re going to assume that we’re building a blog.  That blog will display a list of Posts.  We’re going to pull that list of posts from a server and display them on our site.

## Components
	
We have two important components: The PostList component and the Post component.

		Code goes here

The PostList is going to initiate our Ajax request.  It does this by calling the `PostActions.getAllPosts()` function.  That action looks like this:

		Action Code

Since the action is just calling the service it’s possible for us to call the service directly from the component.  However, in more complex actions you might be doing more then just calling a service.  In that case having the extra decoupling is worth while.  Personally, I think it’s good for all of the components to communicate through actions.

## Services

This is where we’re going to do our ajax call.

		Service Code

I prefer to use the reqwest[MAKE LINK] library in my react projects but you could use whatever suits your fancy.  On a success we call back to our `ServerActions.receiveAllPosts()`.  The great thing about this setup is that we could call the receiveAllPosts() action from anywhere.  Other ajax calls, web sockets, etc.  This separation also allows us to explicitly handle success and error cases.

## Stores

Now that we have our data coming back from the server we need to  get it into our store.  This is straightforward as well:

		Store code

This simply keeps an internal object with all of the posts in it.  We can return that object to the PostList component like so:

		Component Code

This retrieves the data from the store and listens for any updates.  We can then pass that data down into the Post component.

## Conclusion

I hope that helps clarify some of the confusion around where servers fit into the flux pattern.  I’ve been really happy with the results so far.  If you have questions or critiques let me know on twitter @ChrisKeathley.