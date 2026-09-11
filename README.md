# ActivityPub Remixing and Aggregation Task Force

This is the Remixing and Aggregation Task force of the Social Web Incubator Community Group. It was proposed and approved at the [June 2025 meeting of the SocialCG](https://hedgedoc.socialweb.coop/M9qI1gDdRyCVicaqK0rOng).

## Leads

- Bob Wyman
- Evan Prodromou

## Participate

Questions, suggestions, or ideas should be submitted as [GitHub issues](https://github.com/swicg/activitypub-remix/issues).

Action items for this Task Force will be tracked on the [Project Dashboard](https://github.com/orgs/swicg/projects/2).

The Task Force [meets](https://www.w3.org/events/meetings/2b3b9228-5f3d-43ca-9d68-845b82957f25/20260612T170000/) monthly on the second Friday.

## Table of Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Introduction

[ActivityPub](https://activitypub.rocks/) defines an [outbox](https://w3c.github.io/activitypub/#outbox) property of an [actor](https://w3c.github.io/activitypub/#actors) that contains all the [activities](https://w3c.github.io/activitystreams/#activities) performed by the actor.

This is only one way to organize activities, though. Collections could include  activities by multiple actors, or activities that act on a particular object, or activities organized by hashtag or search terms.

**Aggregation** is the process of bringing together activities performed by multiple actors -- maybe all actors; **remixing** is the process of filtering and/or combining activities based on different criteria than the actor.

## User-Facing Problem

People want to aggregate and filter collections of activities by criteria.

### User stories

These are user stories this task force considers in scope. User stories are tracked in the issue tracker with the [user story](https://github.com/swicg/activitypub-remix/issues?q=state%3Aopen%20label%3A%22user%20story%22) label.

- [Followable lists of actors](https://github.com/swicg/activitypub-remix/issues/1): "As an ActivityPub user, I would like to follow a list of actors, so that I don't have to find and follow them all separately."
- [Follow a hashtag](https://github.com/swicg/activitypub-remix/issues/2): "As an ActivityPub user, I want to follow a hashtag, so if anyone posts using that hashtag, I can see it."
- [Follow a search term](https://github.com/swicg/activitypub-remix/issues/3) "As an ActivityPub user, I want to follow a search term, so that if anyone mentions that search term, I will know about it."
- [Union of feeds](https://github.com/swicg/activitypub-remix/issues/4) "As an ActivityPub users, I want to combine the activities from two or more feeds, so I can get everything I'm interested in in one feed."
- [Intersection of feeds](https://github.com/swicg/activitypub-remix/issues/5): "As an ActivityPub users, I want to follow the intersection of multiple feeds, because I only am interested in the activities in the one feed if they also meet the criteria of the second feed."
- [View policy on feeds](https://github.com/swicg/activitypub-remix/issues/6):
"As an ActivityPub user following a feed, I want to know what the criteria are that make up that feed, so that I can replicate it or inspect it."
- [Create a feed](https://github.com/swicg/activitypub-remix/issues/7): "As an ActivityPub user, I want to create a feed of things interesting to me and share it with others, so that people know that I am expert in this area /or/ so that people are more informed on the topic I suggest."
- [Privacy from being aggregated](https://github.com/swicg/activitypub-remix/issues/8): "As an ActivityPub user, I want to express my consent to aggregation, so that I am in control of when and where my content is re-used."
- [Suggesting changes to a feed policy](https://github.com/swicg/activitypub-remix/issues/9): "As an ActivityPub user, I want to suggest a change to the policy for a feed, so that the feed contents are more targeted to its expressed purpose."
- [Follow everything that doesn't meet a particular criterion](https://github.com/swicg/activitypub-remix/issues/10)
- [Update a feed](https://github.com/swicg/activitypub-remix/issues/17): "As an ActivityPub user, I want to update a feed, to change the name or description or access permissions or criteria."
- [Delete a feed](https://github.com/swicg/activitypub-remix/issues/18): "As an ActivityPub user, I want to delete a feed, because I no longer want to follow it or share it."
- [Use all actors on a server as a source](https://github.com/swicg/activitypub-remix/issues/19): "As an ActivityPub user, I want to create a feed that uses all actors on a server as a source, so I can filter activities from those actors."
- [Use actors in a collection as a source](https://github.com/swicg/activitypub-remix/issues/20): "As an ActivityPub actor, I want to define a feed with a collection of actors as the source, so I can add and remove actors from the collection without changing the feed."
- [Use all known actors as a source](https://github.com/swicg/activitypub-remix/issues/21): "As an ActivityPub actor, I want to create a feed that filters activities by all known actors, so I can get a global view of a topic or search term."

### Non-goals

TBD

## User research

None performed (yet).

## Proposed Approach

Aggregated, filtered and remixed feeds are ActivityPub actors with object type [Application](https://w3c.github.io/activitystreams/#application) . Following a feed works exactly like [following any other actor](https://www.w3.org/TR/activitypub/#follow-activity-outbox) with ActivityPub.

Feed actors have a property, `feedCriteria`, that specifies the sources of the feed and filtering criteria on those sources.

Feed actors use [Announce](https://www.w3.org/TR/activitypub/#announce-activity-inbox) activities to re-share the activities of source actors.

Users can create, update and delete feeds using [client to server interactions](https://www.w3.org/TR/activitypub/#client-to-server-interactions).

A new context document defines the terms specific to this application area. This explainer uses `https://remix.example/context` as a placeholder.

### Dependencies on non-stable features

- The ActivityPub API is not widely implemented.

### Solving "Followable lists of actors" with this approach

The `feedCriteria` property would have a way to define multiple sources for a feed.

This actor aggregates the activities of three related actors, and uses the `Announce` activity type to share their activities to its followers.

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    "https://remix.example/context"
  ],
  "id": "https://example.com/feed/threestooges",
  "type": "Application",
  "name": "The Three Stooges",
  "inbox": "https://example.com/feed/threestooges/inbox",
  "outbox": "https://example.com/feed/threestooges/outbox",
  "feedCriteria": {
    "sources": [
      "https://example.org/user/larry",
      "https://social.example/actor/moe",
      "https://example.com/user/curly"
    ]
  }
}
```

Question: Does the feed actor have to follow each of the sources?

### Solving "Follow a hashtag" with this approach

The `feedCriteria` property would have a way to define filtering for a feed, including hashtag filtering.

The following actor would include only announcement by Acme Corporation that are related to customer safety.

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    "https://purl.archive.org/miscellany",
    "https://remix.example/context"
  ],
  "id": "https://example.com/feed/acmesafety",
  "type": "Application",
  "name": "Acme Corporation #CustomerSafety",
  "inbox": "https://example.com/feed/acmesafety/inbox",
  "outbox": "https://example.com/feed/acmesafety/outbox",
  "feedCriteria": {
    "sources": "https://acme.example/organization/acme",
    "filters": {
      "type": "Hashtag",
      "name": "#CustomerSafety"
    }
  }
}
```

`Hashtag` is defined in [ActivityPub Miscellaneous Terms](https://www.w3.org/community/reports/socialcg/CG-FINAL-apmisc-20260828/).

Question: is `Hashtag` the right type to use here? Or should we be more specific, like `HashtagTerm` or `HashtagSearchTerm`?

### Solving "Follow a search term" with this approach

The `feedCriteria` property would have a way to define filtering for a feed, including search terms.

The following actor would include only activities by a group of actors that match a specific search term.

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    "https://remix.example/context"
  ],
  "id": "https://example.com/feed/fourhorsemenapocalypse",
  "type": "Application",
  "name": "Four Horseman 'apocalypse' Posts",
  "inbox": "https://example.com/feed/fourhorsemenapocalypse/inbox",
  "outbox": "https://example.com/feed/fourhorsemenapocalypse/outbox",
  "feedCriteria": {
    "sources": [
      "https://example.org/user/conquest",
      "https://social.example/actor/war",
      "https://example.com/user/famine",
      "https://other.example/user/death"
    ],
    "filters": {
      "type": "SearchTerm",
      "content": "apocalypse"
    }
  }
}
```

`SearchTerm` is a new term for this application.

Question: can the search term be a regular expression? prefix? Other search criteria?

Question: which object types match? Only content types (`Note`, `Article`, `Image`, `Video`, `Audio`)?

Question: which properties match? `name`, `summary`, `content`? Others?

Question: which activities are found for the search term? Only `Create`?

### Solving "Union of feeds" with this approach

One way to provide a union of feeds is to have multiple sources. Those source could themselves be feeds.

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    "https://remix.example/context"
  ],
  "id": "https://example.com/feed/k8sandkubernetes",
  "type": "Application",
  "name": "#k8s and #kubernetes",
  "inbox": "https://example.com/feed/k8sandkubernetes/inbox",
  "outbox": "https://example.com/feed/k8sandkubernetes/outbox",
  "feedCriteria": {
    "sources": [
      "https://tags.example/actor/k8s",
      "https://tags.example/actor/kubernetes"
    ]
  }
}
```

Another way is to have multiple filter criteria joined with a union operator, like `Or`.

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    "https://remix.example/context"
  ],
  "id": "https://example.com/feed/k8sandkubernetes",
  "type": "Application",
  "name": "#k8s and #kubernetes",
  "inbox": "https://example.com/feed/k8sandkubernetes/inbox",
  "outbox": "https://example.com/feed/k8sandkubernetes/outbox",
  "feedCriteria": {
    "sources": [
      "https://aggregator.example/feed/allactors"
    ],
    "filters": {
      "type": "Or",
      "operands": [
        {
          "type": "Hashtag",
          "name": "#k8s"
        },
        {
          "type": "Hashtag",
          "name": "#kubernetes"
        }
      ]
    }
  }
}
```

`Or` is a new term in this vocabulary.

### Solving "Intersection of feeds" with this approach

This can be provided with a filter that is an intersection operator, like `And`.

This feed provides Canadian news by sharing activities from all actors on a server that are tagged both #canada and #news.

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    "https://remix.example/context"
  ],
  "id": "https://example.com/feed/canadiannews",
  "type": "Application",
  "name": "Canadian News",
  "inbox": "https://example.com/feed/canadiannews/inbox",
  "outbox": "https://example.com/feed/canadiannews/outbox",
  "feedCriteria": {
    "sources": [
      "https://server.example/feed/public"
    ],
    "filters": {
      "type": "And",
      "operands": [
        {
          "type": "Hashtag",
          "name": "#news"
        },
        {
          "type": "Hashtag",
          "name": "#canada"
        }
      ]
    }
  }
}
```

`And` is a new term in this vocabulary.

### Solving "View policy on feeds" with this approach

The `feedCriteria` property shows the sources of the feed and its filters.

### Solving "Create a feed" with this approach

A user can post an activity with the [Create](https://w3c.github.io/activitypub/#create-activity-outbox) object type to their outbox to create a new feed. The [object](https://w3c.github.io/activitystreams/#object-property) property of the Create activity should have type `Application` and a `feedCriteria` property.

For example, with this activity, the user is creating a new feed that filters all activities on the server with hashtag #NativePlants.

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    "https://remix.example/context"
  ],
  "type": "Create",
  "object": {
    "type": "Application",
    "name": "#NativePlants Hashtag Feed",
    "feedCriteria": {
      "sources": [
        "https://server.example/feed/public"
      ],
      "filters": {
          "type": "Hashtag",
          "name": "#NativePlants"
      }
    }
  }
}
```

Question: how to limit following the created feed to actors the creator selects?

### Solving "Privacy from being aggregated" with this approach

An actor can define a `Collection` of feed actors they consent to be a source for.

For example, this actor has expressed consent to be a source for two feed actors.

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    "https://remix.example/context"
  ],
  "id": "https://example.com/user/foo",
  "type": "Person",
  "inbox": "https://example.com/collection/1",
  "outbox": "https://example.com/collection/2",
  "sourceFor": {
    "id": "https://example.com/collection/3",
    "type": "Collection",
    "items": [
      "https://feeds.example/feed/k8sexperts",
      "https://other.example/feed/nativeplantfans"
    ]
  }
}
```

Question: does a follow relationship show consent to be aggregated?

Question: does blocking a feed actor suffice for opting out of being aggregated?

Question: should `sourceFor` be a property with multiple values, or a collection?

### Solving "Suggesting changes to a feed policy" with this approach

TBD. Perhaps some kind of custom activity, like `Suggest` or `Propose`? Or maybe `Invite` or `Offer`?

Proposing changes to objects created by other users is unusual in ActivityPub.

### Solving "Follow everything that doesn't meet a particular criterion" with this approach

This can be provided with a filter that is a negation operator, like `Note`.

This feed provides Portland, Maine weather updates by excluding matches for #oregon (where there's another city named "Portland") from the union of #portland and #weather.

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    "https://remix.example/context"
  ],
  "id": "https://example.com/feed/portlandmeweather",
  "type": "Application",
  "name": "Portland Maine Weather",
  "inbox": "https://example.com/feed/portlandmeweather/inbox",
  "outbox": "https://example.com/feed/portlandmeweather/outbox",
  "feedCriteria": {
    "sources": [
      "https://server.example/feed/public"
    ],
    "filters": {
      "type": "And",
      "operands": [
        {
          "type": "Hashtag",
          "name": "#portland"
        },
        {
          "type": "Hashtag",
          "name": "#weather"
        },
        {
          "type": "Not",
          "operand": {
            "type": "Hashtag",
            "name": "#oregon"
          }
        }
      ]
    }
  }
}
```

`Not` is a new term in this vocabulary.

### Solving "Update a feed" with this approach

A user can post an activity with the [Update](https://w3c.github.io/activitypub/#update-activity-outbox) object type to their outbox to create a new feed. The [object](https://w3c.github.io/activitystreams/#object-property) property of the Update activity is the feed to be changed.

For example, with this activity, the user is changing the previously-created native plants feed to exclude content that matches the search term "poison ivy".

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    "https://remix.example/context"
  ],
  "type": "Update",
  "object": {
    "type": "Application",
    "id": "https://feeds.example/feed/nativeplants",
    "feedCriteria": {
      "sources": [
        "https://server.example/feed/public"
      ],
      "filters": [
        {
          "type": "Hashtag",
          "name": "#NativePlants"
        },
        {
          "type": "Not",
          "operand": {
            "type": "SearchTerm",
            "content": "poison ivy"
          }
        }
      ]
    }
  }
}
```

### Solving "Delete a feed" with this approach

A user can post an activity with the [Delete](https://w3c.github.io/activitypub/#delete-activity-outbox) object type to their outbox to delete a feed. The [object](https://w3c.github.io/activitystreams/#object-property) property of the Delete activity is the feed to be deleted.

For example, with this activity, the user deletes the native plants feed.

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    "https://remix.example/context"
  ],
  "type": "Delete",
  "object": {
    "type": "Application",
    "id": "https://feeds.example/feed/nativeplants"
  }
}
```

### Solving "Use all actors in a collection as a source" with this approach

The `feedCriteria` `sources` property can include either actors or collections, similar to the addressing properties.

For example, this feed filters activities by all actors in an actor's `following` collection that include the hashtag #montreal.

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    "https://remix.example/context"
  ],
  "id": "https://example.com/feed/followingmontreal",
  "attributedTo": "https://example.com/user/evan",
  "type": "Application",
  "name": "#montreal content by actors that Evan follows",
  "inbox": "https://example.com/feed/followingmontreal/inbox",
  "outbox": "https://example.com/feed/followingmontreal/outbox",
  "feedCriteria": {
    "sources": {
      "type": "Collection",
      "id": "https://example.com/user/evan/following"
    },
    "filters": {
      "type": "Hashtag",
      "name": "#montreal"
    }
  }
}
```

Question: how does the feed get updates about changes to the collection?

Question: does the feed follow every actor in the collection?

Question: is it easier to have a separate actor that just aggregates all the actors explicitly?

### Solving "Use all actors on a server as a source" with this approach

TBD. Maybe this is a subset of the "Use all actors in a collection as a source" user story; there may be some collection that contains all the actors on a server. That might be a big collection, though!

Another option might be to have a special feed with all actors on the collection explicitly in the "sources" property.

### Solving "Use all known actors as a source" with this approach

TBD. Maybe this is a subset of the "Use all actors in a collection as a source" user story, with "as:Public" as the collection?

## Alternatives considered

[More TBD]

### Non-actor feeds

Define a way to follow a feed object type that is not an ActivityPub actor.

#### Pros

- ...

#### Cons

- Actors are the things that you can follow on with ActivityPub

#### Reason for rejection

Uniform following structure

## Accessibility Considerations

TBD

## Internationalization Considerations

TBD

## Privacy Considerations

TBD

## Security Considerations

TBD

## Stakeholder Feedback / Opposition

None yet; please add an issue!

## References & acknowledgements

These are some technologies that have inspired this specification effort:

- channel.org
- Surf Social
- Mastodon Lists
- Twitter/X Lists
