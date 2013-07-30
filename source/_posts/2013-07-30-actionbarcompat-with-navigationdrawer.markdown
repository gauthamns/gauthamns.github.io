---
layout: post
title: "ActionBarCompat with NavigationDrawer"
date: 2013-07-30 17:15
comments: true
categories: 
---

ActionBarSherlock was one of the most popular libraries for Android which emulated the ActionBar functionalities for lower versions of Android. Now with Google releasing Compatibility library, we can use ActionBarActivity to have actionbar in our app instead of using ActionBarSherlock.

I believe every app (majority of the cases) will have ActionBarCompat and NavigationDrawer. If the app is simple enough, there is no need for a NavigationDrawer. So how well do they both play with each other? Pretty well actually. I did not have any problems integrating both even though the Navigation Drawer example given by Google uses native ActionBar and for API levels higher than 14.

Github repo: https://github.com/gauthamns/blog-actionbarcompat.git