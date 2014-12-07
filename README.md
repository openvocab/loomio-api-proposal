
##Introduction [DRAFT]

Connecting apps to other apps 

This document discusses four different strategies for integrating apps with other apps and building app 'ecosystems':
 1. 'Point-to-point Integration',
 2. 'Central Hub',
 3. 'Intergration as a Service', and
 4. 'Open Vocab'

We argue that 'Open Vocab' Strategy promotes more democratic and scalable app 'ecosystems', compared to the other options.

Next we discuss how app developers can progresively implement the Open Vocab strategy. 

Finally, we make specific API reccomendations for app's that wish to integrate using Loomio, Cobudget and DemcracyOS as examples.  


##Strategies for App Ecosystems

###Point-to-point Integration

The main problem for any integration strategy is that developers inevitably represent the same or similar data in their apps in different ways. Developers call these representations 'models'. Where developers follow a 'Point-to-point Integration' strategy they must write specific *translation layers* to transform models in one app into models in another. 

For example, user account details are stored in a model known as a 'User' in Loomio, but in DemocracyOS the same concept is known as a ['Citizen'](https://github.com/DemocracyOS/app/blob/development/lib/models/citizen.js). Further, these models have similar properties but are specified with slightly different terminology. In DemocracyOS a 'Citizen' includes the following properties:

```
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

```
name:
username:
avatar_initials:
avatar_kind:
avatar_url:
profile_url:
... 
```

Both of these include the term 'username' which is undoubtedly the same concept, but other properties use different terms e.g. ```profile_url``` (Loomio) is the same concept as ```profilePictureUrl``` (DemocracyOS) while the 'name' concept is specified as ```name``` in Loomio but split across ```firstName``` and ```lastName``` in DemocracyOS. Humans can recognise ```profile_url``` and ```profilePictureUrl``` as the same concept but machines are not so smart! Further cmplications can arise when from different specifications. Dates and time data might have the same property name "```createdAt```"" but use different date formats. 

To illustrate, for an 'Import your account details from Loomio/DemocracyOS' the developers must write a specific 'translation layer' for transforming one of these models into their own *in addition* to any 'Import account' feature. 

###Central Hub

The 'Central Hub' strategy puts the onus of connecting and writing translation layers on the other apps that want to connect with you! Clearly, this is only viable if your app is a market leader and controls access to desirable data. Twitter, Facebook and other well-known apps have succesfully used this strategy to create a surrounding 'ecosystem' of apps. This strategy is quite simple:

	1. Become a market leader.
	2. Maintain a useful, easy to use API.

Well, perhaps #1 is not so simple. 

A surrounding ecosystem of apps has certain advantages for market leaders.  Every app that connects and relies on your API has the incentive for your app to maintain its positon. These other apps will perhaps also do their own marketing and promote your app by proxy.

###Integration as a Service

A number of ventures offer [Integration (Platform) as a Service](https://www.mulesoft.com/resources/cloudhub/integration-as-a-service). The integration platform translates data from market leaders into their own representation. Apps only need to connect once to the platform and purchase integration with the market leaders as package deals.

###Open Vocab

App developers collaborate to create a common, open vocabulary* and use this voabulary in their API's. For example, the OpenVocab team has specified the same 'User' concept decribed above as a ['Person'](https://github.com/openvocab/person/blob/master/index.js) with the following properties:
```
name:
handle:
bio:
location:
avatar:
homepage:
memberships:
groups:
```

When apps use the same vocabulary, a feature that imports data from one app can work for any ther app that also uses the same vocabulary. Further, with linked data these terms reference others in existing standard vocabularies, meaning that these features will work for data stored in apps that are not *directly* in (or even aware of) the ecosystem. In fact, if apps decide to use a linked data format they are not required to use precisely these terms, only to link to the same term at its source vocabulary.


*also known as an [ontology](http://en.wikipedia.org/wiki/Ontology_(information_science))

###Discussion



![](https://www.mulesoft.com/sites/default/files/integration-complexity_2.png)

### Linked Data and Open Vocab Implemention Strategies


##User

##Group

##Membership

##Motion 

##Vote