+++
date = "2015-04-03T15:16:27+11:00"
title = "API Versioning"

+++

After Sol is widely used by internal and external parties, an API versioning strategy may be implemented to avoid breaking client applications. This wiki page collects opinions about it and different strategies of API versioning. There is no universally accepted method, nor does any standard specify one.

# Version-less API

In an ideal world, after we put out our API to the public, we want backward compatibility on every change we make to the API. Many opinions (including Mike Amundsen's) state that: the best API is a version-less API. Don't do it unless you absolutely MUST.

## Why not use versioning for your Web API?

- Versioning is an anti-pattern on the web. The web does not work on version. Here's a quick test: What version of browser are you using right now? People don't care about the version. They just see new features. Another test: What version of HTML is the page you just loaded? We don't know, and often we don't care. Even the question "What version of Web are you using now?" doesn't make sense. On the web, not everyone cares what version they are on. The Web is not the same as the development space. Therefore, don't leak your implementation details to the outside world because nobody cares.
- Versioning is not a feature, but a tool to deal with changes over time. Nobody picks an API because it has more versions.

## Versioning is a tool to deal with changes

When things don't change, we don't need versioning. Sounds sweet. But when things change, who wants to know? Those people who are affected by the change. They are the build team, test team, product team, integration team, clients, etc. But different groups have different needs to know about the details level of the change. [Semantic Versioning](http://semver.org/) was introduced by Tom Preston-Werner to specify which team will be interested in which level of the change.

M.m.R.B stands for Major, minor, Revision, Build (very similar to semantic versioning). The build team wants to know about any changes to the build number. The test team wants to know what has been added or fixed when a new revision is rolled out. The product team is interested in the minor version; the integration team the major version.

M.m.R (semantic versioning) means:

- a revision is a bug fix that doesn't affect existing functionality or break anything
- a minor version is a new feature that doesn't adversely affect existing feature or break anything
- a major version is something that breaks some other functionalities in the system

Semantic versioning makes sense for internal code management because we have lot of bits and pieces put together. Nevertheless, at the Web API level, the level of details of semantic versioning is not helpful and freaks people out. It is an overkill. People only care if it breaks. No breaks, no worries.

## How can we change without breaking?

- Never take stuff away. Don't get rid of an endpoint, a call, a returned value. They have to stay there.
- Never change the meaning of existing stuff. Create something new instead, and let people know about it.
- Make all new stuff optional. If I add a new API endpoint, it should not be required to run.

All these notions is called extending the interface. We can't destroy, but we build on top of it. We always like to have a clean system. We want to treat everything as a greenfield project. We want to destroy all the evidence of things in the past. But we can't do that with the API we have already exposed to the public.

So sometimes we have to break things. How? Create a new version. Versioning is for breaking. And this is the last resort. All the existing clients stay on the old timeline. The new ones can work on the new timeline.

# API versioning methods

In most cases, it is recommended that you only use a major version number. A scheme like semantic versioning would be overkill for an API. Additions and backward-compatible changes should not increase the version number.

## Option 1: Change hostname

Example:

| | |
| ------------- | ------------- |
| Old | https://api.lonelyplanet.com/pois/5bf85d01-420f-465e-a4c6-bc591ddeaa42    |
| New | https://api.v2.lonelyplanet.com/pois/5bf85d01-420f-465e-a4c6-bc591ddeaa42 |

## Option 2: Add version to the URI path

Example:

| | |
| ------------- | ------------- |
| Old | https://api.lonelyplanet.com/pois/5bf85d01-420f-465e-a4c6-bc591ddeaa42    |
| New | https://api.lonelyplanet.com/v2/pois/5bf85d01-420f-465e-a4c6-bc591ddeaa42 |

This is by far the most common method. It is fairly straightforward from an initial implementation standpoint. This method usually goes along with deprecating a version when there are too many existing versions to avoid maintenance. Imagine if you had to fix a bug for all versions of the API, and you're already on version ten!

This is the method Mike Amundsen recommends. Simply place the major version number in the URI. Make versions easy to identify, hard to make mistake (ex: new authentication keys). Putting it in the URI is the most usable and the hardest to ignore.

However, there is another opinion on this. Embedding of API version into the URI would disrupt the concept of Hypermedia (stated in Roy T. Fieldings PhD dissertation) by having a resource address/URI that would change over time. How about not change the URI, but add new URI for new version and keep old version as is?

## Option 3: Add version to the query parameters

Example:

| | |
| ------------- | ------------- |
| Old | https://api.lonelyplanet.com/pois/5bf85d01-420f-465e-a4c6-bc591ddeaa42           |
| New | https://api.lonelyplanet.com/pois/5bf85d01-420f-465e-a4c6-bc591ddeaa42?version=2 |

## Option 4: Passing a custom header

Example:

`curl -H "Accepts-version: 2.0" https://api.lonelyplanet.com`

## Option 5: Specify a parameter in the HTTP accept header

Example:

`Accept: application/vnd.siren+json: version=2`

## Option 6: Add version to media type

Example:

| | |
| ------------- | ------------- |
| Old | application/vnd.siren+json        |
| New | application/vnd.sol.v2.siren+json |

# References

Mike Amundsen - Designing APIs for the Web
http://urthen.github.io/2013/05/09/ways-to-version-your-api/
http://urthen.github.io/2013/05/16/ways-to-version-your-api-part-2/
http://alphahydrae.com/2013/06/rest-and-hypermedia-apis/#api-versioning
http://stackoverflow.com/questions/389169/best-practices-for-api-versioning
http://www.lexicalscope.com/blog/2012/03/12/how-are-rest-apis-versioned/
