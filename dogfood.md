# MemeoList dog food app
Previously while working on the mobile team, Summers (GitHub user secondsun) wrote a simple application "memeolist" (a portmanteau of memo-list and meme).  This exercise let him explore how the RHMAP 3.x product worked if he were building an app that was very similar to other apps he had seen. He then compiled his experiences into a post mortem, fixed a few annoying bugs, and gained a deeper understanding of how RHMAP worked.

Finally he shared a running instance with the mobile-team so that we could get a "stress test" of the application.  Overall this was a fun and successful project for us and we should replicate that exercise with Aerogear so we can test our assumptions and stretch our knowledge.

A video demonstration for the project is here : https://drive.google.com/file/d/0B64fta5NYF0TUmNFZy0zQ3JTRjQ/view
The source code for the original versions are here : https://github.com/secondsun/memeolist-android and https://github.com/secondsun/art-henry-cloud-app/

## Project Description

Memeolist is a social media application for the AeroGear community where they can share funny images (memes) with each other.  The application will consist of AeroGear services : UPS, KeyCloak, and Metrics; a GraphQL API layer fronted by a custom connector, and client applications using out Xamarin, iOS, Android, and JavaScript SDKs.

## Core Features / User Stories
This section of the proposal describes the actions that a user can perform in the application and provides some basic constraints.

### A User Logs in
Users should be able to log in with a social login that is provided by the underlying operating system. For example, On Android these accounts will be exposed through the account picker. On platforms where this isn't provided, we should fall back to an OAuth flow using the system browser.

### Users can manage a self profile
Users should be able to upload and edit information about themselves in the application.  This will include email, a display name, a profile picture, and a short biography.  As much of this information should be auto filled from the social login when a user first logs in.

### User should be able to see recent memes
Upon logging in, users should be able to see all of the memes which have been recently posted by other users.  They should be able to view older memes by scrolling through the feed in a list style view.  A meme view will show the meme as an image, the display name of the author of the meme, a summry view which displays the number of comments, the number of likes, the date a meme was posted, and provide an option to "like" a meme or "unlike" a previously liked meme.

### User should be able to see comments on a specific meme
A meme allows users to comment on it.  Comments are displayed on a timeline from oldest to newest.  With the exception of the comment's author, comments are read only.

### Users should be able to comment on a specific meme.
A user should be able to comment on a meme.  Users are able to edit and delete their own comments.

### Users should be able to upload memes
A user should be able to create a meme.  Memes may be created from either a template provided in the application, uploaded from a user's local files, or uploaded from a picture that is taken from the application.  Users are also able to delete memes they have uploaded.  Once a meme is generated, it is not able to be edited.

A meme consists of a picture, a comment displayed aligned to the top of the picture, and a comment displayed aligned with the bottom of the picture.  

### Users can delete their activity
A user should have the ability to delete all of their comments and meme history from the app.  When a user deletes a meme it will also delete all comments associated with that meme

### Where possible offline usage should be supported

Users should be able to create memes, view cached memes, author comments, and like posts while offline.  Once the device returns online the qued activity should be submitted to the server even if the app is backgrounded.

## Clients

The memeolist dogfood exercise will support iOS and Android native as well as cross platform development with our Cordova and Xamarin SDKs.  Additionally, the use of GraphQL should let us generate some of the client code which means we won't be starting from scratch for each platform.

### Android

David Martin has done some work with graphQL and Android related to the current research on a new sync service.  The Apollo project has good tooling for supporting GraphQL APIs on Android, and the AeroGear Android SDK supports all of the features we are planning on targetting.  The existing memeolist code from the FeedHenry demo can also serve as a starting point for development and testing, though it will need to undergo a lot of revisions.

### Xamarin SDK

The Xamarin SDK is lacking support for UPS which will have to be implemented for both iOS and Android.  Additionally we lack experience with Xamarin and GraphQL, but a Google search shows that there are a few projects which can be researched for support.  This application will also offer people interested in upskilling on Xamarin a chance to work on a project from greenfield.

### Cordova SDK
GraphQL seems to have a lot of javascript support which will be useful for our Javascript/Cordova client application.  The Cordova SDK has all of the features we will use, and we have a lot of JavaScript expertise to draw on.

### iOS

The iOS SDK has all of the features we need, and iOS has an Apollo GraphQL library as well.  Like Cordova and Android there is a lot of experience we can draw on for developing the app.


## Data Model and API layer

The following is a graphql schema for the proposed data model.  The GraphQL schema also includes the public APIs that will be implemented.

### Sample Schema

```graphql
type Profile {
  id: ID! @unique
  email: String!
  display_name: String!
  biography: String!
  pictureUrl: String!
}

type MemeList {
  id: ID! @unique
  ownerId: ID!
  createdOn: String!
  updatedOn: String!
  memes: [Meme!]!
}

type Meme {
  id: ID! @unique
  ownerId: ID!
  createdOn: String!
  updatedOn: String!
  photoUrl: String!
  comments: [Comment!]!
}

type Comment {
  id: ID! @unique
  ownerId: ID!
  createdOn: String!
  updatedOn: String!
  comment: String!
}

type Query {
  profile(email: String!):Profile!
  usersMemes(email: String!): [Meme!]!
  feed(memeListId: ID!): [Meme!]!
  meme(memeId: ID!):Meme
}

type Mutation {
  createProfile(email: String!, display_name: String!, biography: String!, pictureUrl: String!):Profile!
  updateProfile(id: ID!, email: String!, display_name: String!, biography: String!, pictureUrl: String!):Profile!
  deleteProfile(id: ID!):Profile!

  postMeme(picture: [Int!]!, topComment: String!,bottomComment: String!, memeListId: ID!): Meme!
  deleteMeme(id: ID!): Meme!
  likeMeme(id: ID!): Meme!
  unlikeMeme(id: ID!): Meme!

  postComment(memeId: ID!, comment: String!): Meme!
  deleteComment(id: ID!): Meme!
}
```

## Mobile-Core features used

Memeolist will be build using technologies provided with the mobile-core service catalog.  Additional services will be created as necessary using RHOAR runtimes and exposed via a custom connector.  Finally, we will use this as an opportunity to test graphql style APIs which will serve as the basis for the future sync project.

### Custom Service Connector

The final meme images will be generated server side using a RHOAR runtime (vert.x, node.js, or Wildfly Swarm) and exposed using the custom service connector feature.  This means that our SDKs will be responsible for upload the image, the author metadata, and the message to be displayed on it and the RHOAR service will compose that data into a Meme post and save it to a persistent data store.

### Keycloak

Social integration will be provided with KeyCloak and we will use our auth SDKs.  We should also provide a templated realm and a script so that community users can plug in their developer credentials if they wish to host their own version of memeolist.

### Push

We should use UPS to notify users of activity on memes they have published or liked.

### Metrics

Our metrics SDK should be used to collect the following information 
 * Platform start-up and device information 
 * Errors and exceptions that are happen
 * Length of time the application is open 
 * Number of memes a user interact with during a session broken fown by views, comments, and likes

### Sync / GraphQL PoC

As part of dogfooding we should also try to use this as an opportunity to upskill on GraphQL and see how GraphQL technologies integrate with our current services.  This will be used to inform decisions to be made regarding how the future sync product will be developed.

Of special note is to see how well the subscription feature of GraphQL can be used to create a realtime experience, the caching features can be used to reduce network overhead, and how we can use UPS to trigger updates to the local data when the application is backgrounded.

## Outcomes

While we are developing the application we should be aware that our larger goal is to promote AeroGear and improve our project, services, and SDKs.  

### Blog Posts

Part of building the application is recording our experiences and sharing them in a way that will serve as a roadmap to users trying to solve similar problems while also highlighting our use of OpenSource technologies.

### Community backlogs

If we find bugs, gaps in our APIs, or incomplete features we should use them to fill our backlogs by filing GitHub issues and reviewing them in the community meetings when appropriate.

