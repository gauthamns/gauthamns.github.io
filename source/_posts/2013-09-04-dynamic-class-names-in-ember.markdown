---
layout: post
title: "Dynamic class names in Ember"
date: 2013-09-04 11:13
comments: true
categories: ember.js
---

One of the strong points of ember is binding. It is so easy to update values without having to write observers and doing ugly stuff on DOM (I won't pretend I know much about Javascript).

As I'm rewriting my website Fanzy.in in Ember, I'm facing lot of minor issues which tell me either Ember has a steep learning curve, or docs aren't good enough (both are not good). 

While displaying player's scores in Fanzy, players in the gamer's(Gamers are users who are playing Fanzy) team should be highlighted, along with special highlighting of captain and vice captain. So my html is like this:

``` html
<table class="table table-bordered">
	<tr>
		<td>James Anderson</td>
		<td>Points: 251</td>
	</tr>
	<tr class="info">
		<td>Peter Siddle</td>
		<td>Points: 146</td>
	</tr>
	<tr class="success">
		<td>Ashton Agar</td>
		<td>Points: 251</td>
	</tr>
```

James Anderson is not in the gamer's team. Peter Siddle is in the team and hence ```class="info"```. Ashton Agar is either captain or vice captain and is shown by ```class="success"```

Surprisingly, doing this turned out to be hard in Ember. (Because I just started and couldn't find how to do it) and finally used a custom view to acheive this.



``` coffeescript
Fansy.ScheduledMatch = Fansy.Model.extend
	matchPlayerScores: DS.hasMany("App.MatchPlayerScore")
	matchPlayerSelection: DS.belongsTo("App.MatchPlayerSelection")

Fansy.MatchPlayerSelection = Fansy.Model.extend
	matchPlayerScores: DS.hasMany("App.MatchPlayerScore")
	captainId: DS.attr("number")
	viceCaptainId: DS.attr('number')

Fansy.MatchPlayerScore = Fansy.Model.extend
	totalPoints: DS.attr('number')
	name: DS.attr('name')
	matchPlayerSelection: DS.belongsTo('App.MatchPlayerSelection')
	scheduledMatch: DS.belongsTo('App.ScheduledMatch')
```

Every match is modeled as ```ScheduledMatch``` and players scores as ```MatchPlayerScore```. ```MatchPlayerSelection``` represents the team of the current logged in gamer.
So ScheduledMatch might have around 30 players(MatchPlayerScore) but MatchPlayerSelection will exactly have 11 players(that is how many a gamer is allowed to select in his team.)

Captain and vice captain are just ids for simplicity. Code is below:

Template code:
{% highlight html %}
{% raw %}
<table class="table table-bordered">
	{{#each matchPlayerScore in matchPlayerScores}}
		{{#view Fansy.MpsTrView mpsIdBinding="matchPlayerScore.id" matchPlayerSelectionBinding="matchPlayerSelection"}}
			<td>{{matchPlayerScore.name}}</td>
			<td>{{matchPlayerScore.totalPoints}}</td>
		{{/view}}
	{{/each}}
</table>
{% endraw %}
{% endhighlight %}

Custom View:
``` coffeescript
Fansy.MpsTrView = Em.View.extend
  mpsId: null
  matchPlayerSelection: null
  tagName: 'tr'
  classNameBindings: ['selectionClass']

  selectionClass: (->
    mpsId = parseInt(this.get('mpsId'))
    matchPlayerSelection = this.get('matchPlayerSelection')
    selectionScores = matchPlayerSelection.get('matchPlayerScores.content')
    if mpsId == matchPlayerSelection.get('captainId')
      return "success"
    else if mpsId == matchPlayerSelection.get('viceCaptainId')
      return "success"
    else
      for selectionScore in selectionScores
        if parseInt(selectionScore.id) == mpsId
          return "info"
      return null
  ).property('mpsId', 'matchPlayerSelection')
```

We write our own custom view ```Fansy.MpsTrView``` which will render each ```MatchPlayerScore```. The view will have the <tr> tag ```tagName: 'tr'```. Also, we bind className to ```selectionClass``` function. So in the selection class function, we return ```"info"``` or ```"success"``` as appropriate.

The view will render the handlebars block within {% raw %} {{#view}} {{/view}} {% endraw %} and we provide our table columns there. Since we have declared <tr> tag, handlebars block will be surrounded with <tr>

