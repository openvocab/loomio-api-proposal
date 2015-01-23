
#An Open Vocab Proposal for Loomio, Cobudget and DemocracyOS [DRAFT - WORK IN PROGRESS]

##Introduction

This article gives an overview of software integration, outlines four strategies for a particular type of integration: 'data integration', recommends one of the outlined strategies 'Open Vocab', and provides implementation details for that strategy with reference to Loomio, Cobudget and DemocracyOS.

We align the OpenApp project and our recommendations with the [IndieWeb](http://www.wired.co.uk/news/archive/2013-08/15/indie-web) and [unhosted](https://unhosted.org/) movements and the process outlined in [Rebuilding the Web We Lost](http://dashes.com/anil/2012/12/rebuilding-the-web-we-lost.html). Accordingly, we value tools and processes that promote open, democratic participation; and where users control their own data. 

###How to use this article

We've written this article with a non-technical audience in mind but still assume you're familiar with some basic technical terms of the Web. For example, you will need to understand what [API](http://en.wikipedia.org/wiki/Application_programming_interface) means. 

If you want a high-level of overview of how an important subset of software integration works and the implications of diffirent strategies read [Types of Data Integration](#types-of-integration) followed by [Data Integration Strategies](#part-i-data-integration-strategies).

If you're already confident in the Open Vocab approach and want to understand how Linked Data and JSON-LD work continue on with [Open Vocab Implementations with JSON-LD](#open-vocab-implementions-with-json-ld).

If you want to dive into the details of what the Loomio and Cobudget's APIs could look like head to [Data Entities](#part-III:-data-entities).

---
###Types of Integration

Software integration occurs at three levels:
 1. User Interface (UI),
 2. Business Logic aka 'Services', and
 3. Data

Integrating at the UI level means that familiar components of one app appear in another app. For example, the Loomio decision pie chart might appear in Cobudget in some relevant context. In practice this could mean using the [iframe](https://developer.mozilla.org/en/docs/Web/HTML/Element/iframe) html element to directly embed a component. 

Integrating at the Business Logic level means that apps rely on common 'services'. [Gravatar](https://en.gravatar.com/) is a popular service that allows app users to define their avatar image in one place and allow other apps to pull this information from it. In another example, two independent apps might depend on a third 'Group Service' app to provide a common store for group data. This might allow a user to join a group in one app and be automatically added to the same group in another app. 

Integrating at the Data level means that an app can read from (and potentially write to) different sources of data. For example, DemocracyOS and Loomio may independently publish data through their APIs about decisions or discussions happening around particular topics or in particular regions. A third party app might pull data from these apps, aggregate it, and present the user with a feed of 'discussions happening in your region', or 'curent discussions on topc X'. 

We can think of data integration as a 'substrate' layer on top of which we can build further integrated features and apps. Data integration makes it possible to recreate the same UI elements of one app within another. Similarly, data integration makes it possible to use an app as a service for another app. The reverse is not true and there are also functionality that only data integration can provide that the other kinds of integration cannot. 

For the remainder of this article we only consider Data integration. We discuss UI and Business Logic integration separately in forthcoming articles.

-----
 
## Part I: Data Integration Strategies

###The Translation Problem

Developers represent the same or similar data in their apps in different ways. Developers call these representations [models](http://en.wikipedia.org/wiki/Data_model).

For example, user account details are stored in a model known as a [User](https://github.com/loomio/loomio/blob/master/app%2Fserializers%2Fuser_serializer.rb) in Loomio, but [Citizen](https://github.com/DemocracyOS/app/blob/development/lib/models/citizen.js) in DemocracyOS. The respective developer teams have specified these models with slightly different terminology. In DemocracyOS a ```Citizen``` includes the following properties:

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

Both of these include the term ```username``` which is undoubtedly the same concept. Other properties use different terms e.g. ```profile_url``` (Loomio) is the same concept as ```profilePictureUrl``` (DemocracyOS). The 'name' concept is specified as ```name``` in Loomio but split across ```firstName``` and ```lastName``` in DemocracyOS. Humans can recognise ```profile_url``` and ```profilePictureUrl``` as the same concept but machines are not so smart! Further complications can arise when properties are formatted in different ways. Dates and time data might have the same property name: ```createdAt``` but use different date formats. 

We know of four strategies for dealing with the 'translation problem' and integrating data between apps:
 1. 'Point-to-point Integration',
 2. 'Central Hub',
 3. 'Integration as a Service', and
 4. 'Open Vocab'

Which strategies should apps adopt? None of strategies *exclude* the others in practice, but each has different implications *in emphasis*. We outline and discuss the implications of these strategies with particular referance to their motivating factors, costs, disadvantages and politics.

---
###Point-to-point Integration

A 'Point-to-point Integration' strategy uses a *translation layer* to transform models in one app into models in another. We can imagine a Loomio feature that allows users to 'Import your account details from DemocracyOS'. This requires a translation layer to translate a DemocracyOS ```Citizen``` model and the relevant properties into a Loomio ```User``` model. 

**Motivation:**
Often the simplest way to do data integration is to write a translation layer in the retrieving app and hit the serving app's API for the data.

**Costs:**
The cost of doing this varies depending on the nature of the data being pulled, the ease of the serving app's API and whether they the two data representations need to be kept in sync. If the use-case requires only a single data entity (Posts, User account etc) it might be equivalent to a medium to large feature. 

**Analog organisation:**
The Network. Providers retain control over the data. Connections between apps are ad-hoc.

**Disadvantages:**
When the number of apps to connect is small point-to-point integration is quite manageable. A network of three app's requires only six connections to be fully integrated:

<img src="http://mathinsight.org/media/image/image/network_triangle.png" width="100px">

Problems can arise if either of these apps make changes to their models, their API, or as the number of apps grows. For example a network of 7 app's depicted in the following picture has 13 connections, more than the number of apps, yet it isn't fully integrated:

<img src="http://www.nature.com/srep/2012/120608/srep00444/images/srep00444-f8.jpg" width="200px">

As the number of apps grows linearly, the number of connections grows exponentially. Assuming that all apps need integration with every other app then number of connectons will equal ```n^2 - n``` where ```n``` is the number of apps. E.g:

| # of apps | # of connections |
|-----------|------------------|
| 2         | 2                |
| 3         | 6                |
| 4         | 12               |
| 5         | 20               |
| 10        | 90               |
| 15        | 210              |

Costs increase with the number of connections. A developer will also find it difficult to understand the network as a whole. This could make reasoning about the downstream implications of a particular point-to-point integration difficult.

<img src="https://www.mulesoft.com/sites/default/files/integration-complexity_2.png">


---
###Central Hub

The Central Hub strategy puts the onus of connecting and writing translation layers on the other apps that want your data!

Clearly this is only viable if your app is a market leader and hosts desirable data. Twitter, Facebook and other well-known apps have succesfully used this strategy to create a surrounding 'spoke and hub' network of apps. 

**Motivation:**
Every app that connects and relies on the Hub's API has the incentive for the Hub to maintain a leading positon. These other apps may also do their own marketing and promote the Hub by proxy. For example, [Medium](http://medium.com/) requires users to have a Twitter (the Hub) account. Medium was founded by one of the Twitter cofounders who clearly has an incentive to promote Twitter and extend the 'Twittersphere'. 

**Costs:**
This strategy costs much less than the point-to-point strategy. The central app only needs to maintain an easy-to-use API with good documentation. Connecting apps bear integration costs.

**Analog organisation:**
Feudalism. The Hub controls most of the data in the network. Many apps rely on the central app for functionality.

**Disadvantages:**

Today, the majority of current web users experience comes via large providers like Google, Facebook, and Twitter who have pursued a central hub strategy. These providers control much of our online data. This control entails the following political consequences:

 1. **Commercial logics dominate**. Most of these providers serve advertising or onsell user data to advertisers - we lack online spaces free from commercial imperatives.

 2. **Fragile security**. Malicious or morally ambiguous third parties (hackers, the NSA etc) might gain access to all user data through a single entry point.

 3. **Fragile user rights**. The business behind the Hub app may go bust, or another business may buy it prompting a revison in the Terms of Service and degradation of user rights.

The situation is analogous to the use private offline spaces for public good purposes: when private interests control and surveil the spaces where people meet and interact then these interests will often act to curtail freedoms of speech, protest and dissent. For more information on this perspective please see [@anildash](https://github.com/anildash)'s talk [The Web We Lost](http://dashes.com/anil/2012/12/the-web-we-lost.html)

---
###Integration as a Service

A number of ventures offer [Integration as a Service](https://www.mulesoft.com/resources/cloudhub/integration-as-a-service). The integration platform translates data from market leaders into their own representation. Apps need only connect once to the platform and purchase integration with several leading apps as package deals.

**Motivation and Costs:**
If the app in question only needs integration with market leaders (i.e. the 'Hubs') then paying an integration provider to perform and maintain these may well be cost-effectve compared to the equivalent developer time required to do this internally.

**Analog organisation:**
The Market. 

**Disadvantages:**

Has the same consequences as 'Central Hub'.

If integrations form a key part of your app's value propositon then outsourcing this to a contracted service may put your team at a disadvantage later on.

---
###Open Vocab

Open Vocab is more commonly known as [Semantic Interoperability](http://en.wikipedia.org/wiki/Semantic_interoperability). Here, developers agree to use a common, open vocabulary* in their API's. 

To illustrate, the OpenVocab team has specified the same User concept decribed above as a [Person](https://github.com/openvocab/person/blob/master/index.js) with the following properties:
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

When apps use the same vocabulary a feature that imports data from one app can also work for *any* other app that also uses the same vocabulary.  

**Motivation:**
We have framed the costs and benefits of previous strategies in instrumental terms - how do these weigh up for each app and the team behind it? Broader concerns motivate an Open Vocab strategy:

 1. Provide **low-cost integration** for connecting (client) apps. Whereas an app following the Central Hub strategy offloads the integration costs to the connecting apps and leverages its central position, the Open Vocab particpant assumes that they are merely *one data provider in an open network of similar providers*. Standard vocabularies allow connecting apps to use one translation layer per model rather than the exponentially growing number of translation layers as outlined above. We believe this will dramatically lower costs (compared to point-to-point) for *the network as a whole* once it grows beyond a small number of apps.

 Low cost integration is one of the principles of [Commons Based Peer Production](http://en.wikipedia.org/wiki/Commons-based_peer_production#Principles)

 2. **Mitigate the security vulnerabilities of centrally hosted data**. Open vocab helps facilitate a Web where individuals and groups host their own data while still connecting to public data and other trusted individuals and groups in the network. This point does require more technical backing than Open Vocab (we plan to use [Secure Scuttlebut](https://github.com/ssbc)).

 3. **Shift power from large providers towards individuals and groups.** As above, users need not submit to any Terms of Service or fear the loss of their data when they host it themselves. This aligns OpenApp with the [IndieWeb](http://www.wired.co.uk/news/archive/2013-08/15/indie-web) and [unhosted](https://unhosted.org/) movements.

**Costs and Disadvantages:**
Costs come from:
 1. Agreeing to a standard vocab: composing this vocab from existing standards, or writing our own when existing ones are not appropriate. In OpenApp, the OpenVocab team facilitate these tasks.

 2. For serving apps, modifying or implementing API's that conform to the above standards. Fortunately JSON-LD makes this somewhat easier than this would otherwise be (discussed below).

 3. For client apps, implementing JSON-LD tooling and the translation layers between OpenVocab entities in question and their own models.


**Analog organisation:**
The Commons.

*also known as an [ontology](http://en.wikipedia.org/wiki/Ontology_(information_science))

####General Discussion

The four strategies each have a different pattern of costs and benefits.  Point-to-point has smaller incremental costs and benefits, but benefits degrade as the network grows. Central Hub has small costs, high flexibility, but benefits dispropotionately go to the Hub and reinforce aspects of the Web that we find concerning. Integration-as-a-service delivers a large amount of value quickly but has ongoing costs and the same problems as Central Hub. Open Vocab has a larger upfront investment cost (vocab R&D, reaching agreement) but scalable, expanding benefits across the network.

For these reasons and the values that go alongside them we reccommend the Open Vocab strategy. In Part II we look at implementing an Open Vocab Strategy with [JSON-LD](http://json-ld.org/)

---
## Part II: Implementation Details

### Open Vocab Implementions with JSON-LD

#### JSON-LD Introduction

#### The @context object


#### Standard Vocabs and Prefixes

#### Modifying the API

#### JSON-LD tooling


## Part III: Data Entities

###User

[TODO add user]

###Group

[TODO add user]

###Membership

[TODO add user]


###Motion 

[TODO discuss Popolo Mtion spec]


###Vote
