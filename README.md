
##Introduction

Connecting apps to other apps 

This document discusses four different strategies for integrating apps with other apps and building app 'ecosystems':
 1. 'Piecemeal Integration',
 2. 'Central Hub',
 3. 'Intergration as a Service', and
 4. 'Open Vocab'

We argue that 'Open Vocab' Strategy promotes more democratic and scalable app 'ecosystems', compared to the other options.

Next we discuss how app developers can progresively implement the Open Vocab strategy. 

Finally, we make specific API reccomendations for app's that wish to integrate using Loomio, Cobudget and DemcracyOS as examples.  


##Strategies for App Ecosystems

###Piecemeal Integration

The main problem for any integration strategy is that developers inevitably represent the same or similar data in their apps in different ways. Developers call these representations 'models'. Where developers follow a 'Piecemeal Integration' strategy they will write specific *translation layers* to transform models in one app into models in another *one at a time*. 

For example, user account in Loomio is stored in a model known as a 'User', but in DemocracyOS the same concept is known as a ['Citizen'](https://github.com/DemocracyOS/app/blob/development/lib/models/citizen.js). Further, these models have similar properties but are specified with slightly different terminology. In DemocracyOS a 'Citizen' includes the following properties:

``` Citizen:
	firstName:
	lastName:
	username:
	avatar:
	createdAt:
	updatedAt:
	profilePictureUrl:
	disabledAt:
	... 
```

While in Loomio a 'User' includes the following:

``` User:
	name:
	username:
	avatar_initials:
	avatar_kind:
	avatar_url:
	profile_url:
	... 
```

Both of these include the term 'username' which is undoubtedly the same concept, but other properties use different terms e.g. ```profile_url``` (Loomio) is the same concept as ```profilePictureUrl``` (DemocracyOS) while the 'name' concept is specified as ```name``` in Loomio but split across ```firstName``` and ```lastName``` in DemocracyOS. Humans can recognise ```profile_url``` and ```profilePictureUrl``` as the same concept but machines are not so smart! Further cmplications can arise when from different specifications. Dates and time data might have the same property name ```createdAt``` but use different date formats. 

If these apps followed a Piecemeal Integration strategy and wished to allow users to share their personal data accross apps, i.e. an 'Import your account details from Loomio/DemocracyOS' the developers must write a specific 'translation layer' for transforming one of these models into another *in addition* to any 'Import account' feature. 

###Central Hub

The 'Central Hub' strategy puts the onus of connecting and writing translation layers on the other apps that want to connect with you! Clearly, this is only viable if your app is a market leader in its domain and controls access to desirable data. Twitter, Facebook and other well-known apps have succesfully used this strategy to create a surrounding 'ecosystem' of apps. This strategy is quite simple:

	1. Become a market leader.
	2. Maintain a useful, easy to use API.

Well, perhaps #1 is not so simple. 

A surrounding ecosystem of apps has certain advantages for market leaders.  Every app that connects and relies on your API has the incentive for your app to maintain its positon. These other apps will perhaps also do their own marketing and promote your app by proxy.



###Integration as a Service

###Open Vocab


## Linked Data and Open Vocab Implemention Strategies


##User

##Group

##Membership

##Motion 

##Vote