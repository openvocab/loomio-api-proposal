
#An Open Vocab Proposal for Loomio, Cobudget and DemocracyOS [DRAFT - WORK IN PROGRESS]

##Introduction

###How to use this article

This article assumes you're familiar with some basic technical terms of the Web but is written with non-technical audience in mind. You will need to understand what [API](http://en.wikipedia.org/wiki/Application_programming_interface) means. 

If you want a high-level of overview of how a subset of software integration works read the rest of the Introduction and Stratgies for Data Integration.

If you want to understand how Linked Data and JSON-LD work continue on with 'Open Vocab Implementations with JSON-LD'.

If you want to dive into the details of what the Loomio API could look like head to 'Data Entities'.

###Main points

 1. 'Integrating apps' can mean different things.
 2. Data integration is a powerful and flexible approach to integration.
 3. We advocate the 'Open Vocab' approach to data integration.
 5. [JSON-LD](http://json-ld.org/) (a data format) makes impementing the Open Vocab approach easy.
 5. We provide details on an Open Vocab implementation using Loomio, Cobudget, and DemocracyOS API's as examples.

###What do you mean by 'Data Integration'?

Software integration can occur at three different levels:
 1. User Interface (UI),
 2. Business Logic aka 'Services', and
 3. Data

Integrating at the UI level means that familiar components of one app appear in another app. For example, the Loomio decision pie chart might appear in Cobudget in some relevant context. In practice this could mean using the [<iframe>](https://developer.mozilla.org/en/docs/Web/HTML/Element/iframe) html element to directly embed a component. 

Integrating at the Business Logic level means that apps rely on common 'services'. [Gravatar](https://en.gravatar.com/) is a popular service that allows app users to define their avatar image in one place and allow other apps to pull this information from it. In another example, two independent apps might depend on a third 'Group Service' app to provide a common store for group data. This might allow a user to join a group in one app and be automatically added to the same group in another app. 

Integrating at the Data level means that an app can read from (and potentially write to) different sources of data. For example, DemocracyOS and Loomio may independently publish data through their APIs about decisions or discussions happening around particular topics or in particular regions. A third party app might pull data from these apps, aggregate it, and present the user with a feed of 'discussions happening in your region', or 'curent discussions on topc X'. 

We can think of data integration as a 'substrate' layer on top of which we can build further integrated features and apps. Data integration makes it possible to recreate the same UI elements of one app within another. Similarly, data integration makes it possible to use an app as a service for another app. The reverse is not true and there are also functions that only data integration can provide that the other kinds of integration cannot. 

For the remainder of this article we only considers Data integration. We discuss UI and Business Logic integration separately in forthcoming articles.

-----
 
##Strategies for Data Integration

###The Translation Problem

An inevitable, and perhaps main, problem for integration is that developers represent the same or similar data in their apps in different ways. Developers call these representations [models](http://en.wikipedia.org/wiki/Data_model).

For example, user account details are stored in a model known as a [User](https://github.com/loomio/loomio/blob/master/app%2Fserializers%2Fuser_serializer.rb) in Loomio, but in DemocracyOS the same concept is known as a [Citizen](https://github.com/DemocracyOS/app/blob/development/lib/models/citizen.js). These models have similar properties that the developers have specified with slightly different terminology. In DemocracyOS a ```Citizen``` includes the following properties:

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

Both of these include the term ```username``` which is undoubtedly the same concept. Other properties use different terms e.g. ```profile_url``` (Loomio) is the same concept as ```profilePictureUrl``` (DemocracyOS). The 'name' concept is specified as ```name``` in Loomio but split across ```firstName``` and ```lastName``` in DemocracyOS. Humans can recognise ```profile_url``` and ```profilePictureUrl``` as the same concept but machines are not so smart! Further complications can arise when properties are formatted in different ways. Dates and time data might have the same property name ```createdAt``` but use different date formats. 

###Data Integration Strategies

We know of four strategies for dealing with the 'translation problem' and integrating data between apps:
 1. 'Point-to-point Integration',
 2. 'Central Hub',
 3. 'Integration as a Service', and
 4. 'Open Vocab'

###Point-to-point Integration

A 'Point-to-point Integration' strategy uses a *translation layer* to transform models in one app into models in another. For example, we can imagine a Loomio feature that allows users to 'Import your account details from DemocracyOS'. This would require a translation layer to translate a DemocracyOS Citizen model into a Loomio User model. 

###Central Hub

The 'Central Hub' strategy puts the onus of connecting and writing translation layers on the other apps that want your data!

Clearly, this is only viable if your app is a market leader and controls access to desirable data. Twitter, Facebook and other well-known apps have succesfully used this strategy to create a surrounding 'spoke and hub' network of apps. The bones of this strategy is quite simple:

	1. Become a market leader.
	2. Maintain a useful, easy-to-use API.

Well, perhaps #1 is not so simple. 

###Integration as a Service

A number of ventures offer [Integration as a Service](https://www.mulesoft.com/resources/cloudhub/integration-as-a-service). The integration platform translates data from market leaders into their own representation. Apps need only connect once to the platform and purchase integration with several leading apps as package deals.

###Open Vocab

App developers use a common, open vocabulary* in their API's. 
Open Vocab is more commonly known as [Semantic Interoperability](http://en.wikipedia.org/wiki/Semantic_interoperability).

For example, the OpenVocab team has specified the same User concept decribed above as a ['Person'](https://github.com/openvocab/person/blob/master/index.js) with the following properties:
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

When apps use the same vocabulary, a feature that imports data from one app can also work for *any* other app that also uses the same vocabulary.  

*also known as an [ontology](http://en.wikipedia.org/wiki/Ontology_(information_science))

###Discussion

Which strategies should apps adopt? None of strategies exclude the others in practice necessarily, but each has different implications if an app emphasises one or neglects another. We discuss the implications of these strategies with particular referance to the motivations, costs, disadvantages and politics of each strategy.



####Point-to-point

Motivation:
Users often won't to see data that is hosted on different apps 'inside' another app and often the simplest way to about this is to write a translation layer in the retrieving app and hit the serving app's API.

Costs:
The cost of doing this varies depending on the nature of the data being pulled and the ease of the serving app's API. If the use-case requires only a single data entity (Posts, User account info etc) it might be equivalent to a medium to large feature. 

Organisational analog:
The Network. Providers retain control over the data. Connections between apps are ad-hoc.

Disadvantages:
When the number of apps to connect is small point-to-point integration is quite manageable. A network of three app's requires only six connections to be fully integrated:

![](http://mathinsight.org/media/image/image/network_triangle.png)

Problems can arise if either of these apps make changes to their models, their API, or as the number of apps. For example a network of 7 app's depicted in the following picture has 13 connections, more than the number of apps and it isn't fully integrated:

![](http://www.nature.com/srep/2012/120608/srep00444/images/srep00444-f8.jpg)

As the number of apps grows linearly, the number of connections grows exponentially. Assuming that all apps need integration with every other app then number of connectons will equal ```n^2 - n``` where ```n``` is the number of apps. E.g:

| # of apps | # of connections |
|-----------|------------------|
| 2         | 2                |
| 3         | 6                |
| 4         | 12               |
| 5         | 20               |
| 10        | 90               |
| 15        | 210              |

Costs increase with the number of connections. Further, A developer will find it difficult to understand the newtwork as a whole. This could make maintaining the network, or reasoning about the downstream implications of a particular point-to-point integration difficult.

![](https://www.mulesoft.com/sites/default/files/integration-complexity_2.png)


####Central Hub

Motivation:
Every app that connects and relies on the Hub's API has the incentive for the Hub to maintain a leading positon. These other apps also do their own marketing and promote the hub app by proxy.

For example, [Medium](http://medium.com/) users must have a Twitter account. Medium was founded by one of the Twitter cofounders who clearly has an incentive to promote Twitter to Medium users. 

Costs:
This strategy costs much less than the point-to-point strategy. The central app only needs to maintain an easy-to-use API with good documentation. Connecting apps bear integration costs.

Organisational analog:
Feudalism. The Hub controls most of the data in the network. Many apps rely on the central app for functionality and are at its mercy and cotinued .

Disadvantages:
Hugely depends on the app's success as a market leader.

The majority of current web users experience comes via large providers like Google, Facebook, Twitter etc who have pursued a central hub strategy. These providers control much of our online data. This control entails the following political consequences:

 1. Most providers serve advertising or onsell user data to advertisers - as a consequence we lack online spaces free from commercial imperatives.

 2. Malicious or morally ambiguous third parties (hackers, the NSA etc) may gain access to all user data through the single entry point of the Providers' servers. 

The situation presents an analogue to the tesion between private and public offline spaces: when private interests control and surveil these spaces people meet and interact then these interests will often act to curtail freedoms of speech, protest and dissent.  


####Integration as a service (IaaS)

Motivation and Costs:
If the app in question only needs to integrate with market leaders ('Hubs') then paying an integration provider to perform and maintain these may well be cost-effectve compared to the equivalent developer time required to do this internally.

Organisational analog:
The Market. 

Disadvantages:
Has the same consequences as 'Central Hub'
If integrations form a key part of your app's value propsiton then outsourcing this to a contracted service may put you at a disadvantage later on.


####Open Vocab

Motivation:

Costs:

Organisational Analog:
The commons.

####General Discussion

We can see that pursuing. Self interest







##Why Integrate Data?

Apps host data. The hosted Loomio sevice hosts comments, votes  developers may find further uses for the data generated in other apps. When apps permit users to share data other apps can repurpose the data in different ways. Developers can focus on a particular domain where their app excels and allow other apps to deal with the related but separate problems rather than trying to develop a toolbox that does everything. 

The 'blank slate' describes a common problem where a new app could be well designed and potentially useful but lacks content. The app's value may rely on having a critical mass of existing data with which the user can interact. App promoters are often caught in a catch-22 where they need users to sign up to begin adding content, but find it difficult to convince potential users to do so when this initial content does not exist. By connecting and importing data from other apps the app could 'seed' enough content and demonstrate its value to users.

Users may have political or privacy concerns and wish to host their own a data themselves or with a group they trust. In the Web's current state multiple private providers host and control user data and experience. These third-party buinesss typically provide  a 'free' service to users but sell advertising, or onsell user data to sustain the business. A discussion of the private control of data vs the movement to shift control back to users themselves is beyond the scope of this article. Suffice to say that many people are not satisfied with the Web in its current state (including us!).

Further, with linked data (discussed below) these terms reference others in existing standard vocabularies, meaning that these features will work for data stored in apps that are not *directly* in (or even aware of) the ecosystem. In fact, if apps decide to use a linked data format they are not required to use precisely these terms, only to link to the same term at its source vocabulary.


### Open Vocab Implementions with JSON-LD


##Data Entities

###User

###Group

###Membership

###Motion 

###Vote