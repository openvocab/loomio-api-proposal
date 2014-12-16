
#A Data Integration Proposal for Loomio [DRAFT - WORK IN PROGRESS]

##Introduction

###How to use this document

This document assumes you're familiar with some basic technical terms of the Web but is written with non-technical audience in mind. For example, you will need to understand what [API](http://en.wikipedia.org/wiki/Application_programming_interface) means. 

If you want a high-level of overview of software integration read the rest of the Introduction and Stratgies for Data Integration.

If you want to understand how Linked Data and JSON-LD work continue on with Open Vocab Implementation with JSON-LD.

If you want to dive into the details of what the Loomio API could look like head to Data Entities.

###Main points

 1. People mean different things when speak of 'integrating apps'. 
 2. Data integration is more powerful and flexible than other types of integration (but the others are cool too).
 3. We advocate the 'Open Vocab' approach to data integration.
 5. [JSON-LD](http://json-ld.org/) (a data format) makes impementing an Open Vocab approach easy.
 5. We provide details on how to implement an Open Vocab strategy using Loomio, Cobudget, and DemocracyOS API's as examples.

###What do you mean by 'Data Integration'?

Software integration can occur at three different levels:
 1. User Interface (UI),
 2. Business Logic or 'Services', and
 3. Data

Integrating at the UI level means that familiar elements of one app appear in another app. For example, the Loomio decision pie chart might appear in Cobudget in some relevant context.

Integrating at the Business Logic level means that apps rely on common 'services'. For example, two different apps might rely on a third 'Group' service which provides a common definition of groups for client apps. This might allow a user to join a group in one app and be automatically added to the same group in another app.

Integrating at the Data level means that an app can read from (and even write to) different sources of data. For example, DemocracyOS and Loomio may independently publish data through their API about decisions or discussions happening around particular topics or in particular regions. A third party app might pull data from these apps, aggregate it, and present the user with a feed of 'discussions happening in your region', or 'curent discussions on topc X'. 

We can think of data integration as a 'substrate' layer. when we integrate app's data its possible to recreate the same UI elements of one app within another . Its also possible to use a data source to provide similar functionality as a service. However, the reverse is not true. There are functions that only data integration can provide that the other kinds of integration cannot.

For the remainder of this document we only considers Data integration. We plan to discuss UI and Business Logic integration separately.

###Data Integration Strategies

We know of four strategies for integrating data from apps with other apps:
 1. 'Point-to-point Integration',
 2. 'Central Hub',
 3. 'Integration as a Service', and
 4. 'Open Vocab'

##Why Integrate Data?

Apps host data. The hosted Loomio sevice Loomio hosts  developers may find further uses for the data generated in other apps. When apps permit users to share data other apps can repurpose the data in different ways. Developers can focus on a particular domain where their app excels and allow other apps to deal with the related but separate problems rather than trying to develop a toolbox that does everything. 

The 'blank slate' describes a common problem where a new app could be well designed and potentially useful but lacks content. The app's value may rely on having a critical mass of existing data with which the user can interact. App promoters are often caught in a catch-22 where they need users to sign up to begin adding content, but find it difficult to convince potential users to do so when this initial content does not exist. By connecting and importing data from other apps the app could 'seed' enough content and demonstrate its value to users.

Users may have political or privacy concerns and wish to host their own a data themselves or with a group they trust. In the Web's current state multiple private providers host and control user data and experience. These third-party buinesss typically provide  a 'free' service to users but sell advertising, or onsell user data to sustain the business. A discussion of the private control of data vs the movement to shift control back to users themselves is beyond the scope of this document. Suffice to say that many people are not satisfied with the Web in its current state (including us!).

Lastly, with a conected ecosystem of apps users could avoid some of the more mundane tasks of the Web such as re-entring their details in various forms. 

-----
 
##Strategies for Data Integration

###Translating Models

The main problem for any integration strategy is that developers inevitably represent the same or similar data in their apps in different ways. Developers call these representations 'models'.

For example, user account details are stored in a model known as a [User](https://github.com/loomio/loomio/blob/master/app%2Fserializers%2Fuser_serializer.rb) in Loomio, but in DemocracyOS the same concept is known as a [Citizen](https://github.com/DemocracyOS/app/blob/development/lib/models/citizen.js). These models have similar properties but the cevelopers have specified with slightly different terminology. In DemocracyOS a ```Citizen``` includes the following properties:

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

While in Loomio a ```User``` includes the following:

```
name:
username:
avatar_initials:
avatar_kind:
avatar_url:
profile_url:
... 
```

Both of these include the term ```username``` which is undoubtedly the same concept. Other properties use different terms e.g. ```profile_url``` (Loomio) is the same concept as ```profilePictureUrl``` (DemocracyOS). The 'name' concept is specified as ```name``` in Loomio but split across ```firstName``` and ```lastName``` in DemocracyOS. Humans can recognise ```profile_url``` and ```profilePictureUrl``` as the same concept but machines are not so smart! Further complications can arise when from different formats. Dates and time data might have the same property name ```createdAt``` but use different date formats. 

###Point-to-point Integration

Where developers follow a 'Point-to-point Integration' strategy they write specific *translation layers* for transforming models in one app into models in another. A Loomio feature that allows users to 'Import your account details from DemocracyOS' would require a 'translation layer' for transforming a Citizen model into a Loomio User model. This may not be so difficult in itself, but problems can arise if either of these apps make changes to their models, their API, or the number of connections grow: 

![](https://www.mulesoft.com/sites/default/files/integration-complexity_2.png)

###Central Hub

The 'Central Hub' strategy puts the onus of connecting and writing translation layers on the other apps that want to connect with you! 

Clearly, this is only viable if your app is a market leader and controls access to desirable data. Twitter, Facebook and other well-known apps have succesfully used this strategy to create a surrounding 'ecosystem' of apps. The bones of this strategy is quite simple:

	1. Become a market leader.
	2. Maintain a useful, easy to use API.

Well, perhaps #1 is not so simple. 

Market leaders might use the 'Central Hub' startegy to consolidate their position. Every app that connects and relies on the Hub's API has the incentive for the Hub app to maintain its leading positon. These other apps will perhaps also do their own marketing and promote the hub app by proxy.

###Integration as a Service

A number of ventures offer [Integration (Platform) as a Service](https://www.mulesoft.com/resources/cloudhub/integration-as-a-service). The integration platform translates data from market leaders into their own representation. Apps need only connect once to the platform and purchase integration with several leading apps as package deals.

###Open Vocab

App developers collaborate to create a common, open vocabulary* and use this voabulary in their API's. For example, the OpenVocab team has specified the same User concept decribed above as a ['Person'](https://github.com/openvocab/person/blob/master/index.js) with the following properties:
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

When apps use the same vocabulary, a feature that imports data from one app can also work for any ther app that also uses the same vocabulary. Further, with linked data (discussed below) these terms reference others in existing standard vocabularies, meaning that these features will work for data stored in apps that are not *directly* in (or even aware of) the ecosystem. In fact, if apps decide to use a linked data format they are not required to use precisely these terms, only to link to the same term at its source vocabulary.


*also known as an [ontology](http://en.wikipedia.org/wiki/Ontology_(information_science))

###Discussion

### Open Vocab Implemention with JSON-LD


##Data Entities

###User

###Group

###Membership

###Motion 

###Vote