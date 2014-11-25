---
layout: post
title: "A look at Ember Data"
date: 2013-08-28 08:38
comments: true
categories: ember.js
---

I'm learning Ember.js to use it for Fansy.in website and it has been a wonderful experience so far.

Why client side MVC?
Better user experience. A game like Fantasy Cricket has to have a dynamic website to keep the gamers interested. And as I understand, Javascript MVC's make it very easy to build dynamic web apps.

I chose Ember because I want to integrate Discourse to Fansy.in (still a few months away!!) so choosing Ember was an obvious choice. I'm loving the framework and the benefits look obvious. There is discourse project to look for some inspiration.

But Ember-Data!! while ember-data looks cool from the outside, it is still raw and too restrictive to fit to anyone's needs (according to me atleast.). Today's websites are complicated and cannot fit into one model per page category. 

For example, in Fansy.in, Tournaments have Matches and Tournament user rankings. It is expensive and unnecessary to load all the ids of the hasMany relationships when loading tournaments in the tournaments page where we do not need them. 
And then, when we go to a Tournament's page where we will need Matches and User's rankings, two requests are made to fetch the relationships and increases with the number of relationships for a model.

I've been thinking about how to handle it so that the number of json requests do not shoot up and also we are fetching only the data we need. My thought process as of now:

1. From the server, Send all the data required for displaying the particular page.
2. For relationships, side load relationships and never send only ids. (This will make sure extra calls are not made for fetching the those objects later. Yes it is expensive to send all the data. Will handle it in the next step.)
3. Send only the data required for displaying the particular page. If matches are not required while showing list of tournaments, do not send down the ids of the matches too. (This will break the ember-data loading part but will handle it in the next step.)
4. In the setupController, reload the model if loaded already. This way, we will make sure that the model is reloaded and the server will send all the request necessary to show the particular page.

``` coffeescript
	setupController: (controller, model) ->
		if model.get('isLoaded')
			model.reload()
		controller.set('model', model)
```