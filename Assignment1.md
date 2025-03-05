# CST8914 Assignment 1 - Real-Time Applications with REST, GraphQL & WebSockets
By Samuel Baumann & Mohit Singh Panwar

Scenario: Real-time chat application use case, with a focus on social platforms

This document will outline the major considerations for how chat applications have advanced with modern technologies to handle real-time data management and communication.

## REST & GraphQL

For social media chat applications, having two or more devices connected and talking to each other in real-time is a self-evident requirement - the whole point
of a live chat is that it's live! To accomplish this, developers can choose between having their endpoints communicate with each other via REST, a scaleable, reliable
and widely adopted API that fetches data in large round trips, or GraphQL, a more complex but surgical way to fetch a select slice of information from an endpoint.

Let's start by taking a look at a typical RESTful API for a chat application. In particular, we can look at how an app would GET, POST, PUT or DELETE information from a
server by following HTTP standards. As its name implies, the POST method retrieves data from a backend. It is read-only, and does not modify the data in any way. For example,
let's take a look at the official Microsoft documentation for the creation of a Microsoft Graph chat in HTTP:

```
POST https://graph.microsoft.com/v1.0/chats
Content-Type: application/json

{
  "chatType": "oneOnOne",
  "members": [
    {
      "@odata.type": "#microsoft.graph.aadUserConversationMember",
      "roles": ["owner"],
      "user@odata.bind": "https://graph.microsoft.com/v1.0/users('8b081ef6-4792-4def-b2c9-c363a1bf41d5')"
    },
    {
      "@odata.type": "#microsoft.graph.aadUserConversationMember",
      "roles": ["owner"],
      "user@odata.bind": "https://graph.microsoft.com/v1.0/users('82af01c5-f7cc-4a2e-a728-3a5df21afd9d')"
    }
  ]
}
```

In the above snippet, a user sends a POST request to a server (in this case, the URL in the first line) containing information about the users that want to initiate a
chat, along with information like their roles in the system and unique hexadecimal ID. Of course, these variables and their associated values are arbitrary, and change
from environment to environment! When the server receives this request, it then sends back a response to the user that would look something like this:

```
HTTP/1.1 201 Created
Content-Type: application/json

{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#chats/$entity",
    "id": "19:82fe7758-5bb3-4f0d-a43f-e555fd399c6f_8c0a1a67-50ce-4114-bb6c-da9c5dbcf6ca@unq.gbl.spaces",
    "topic": null,
    "createdDateTime": "2020-12-04T23:10:28.51Z",
    "lastUpdatedDateTime": "2020-12-04T23:10:28.51Z",
    "chatType": "oneOnOne"
}
```
Where a code 201 is sent back, indicating that a new chat with a unique ID has been created. In this case, we're storing its date, time and type for
later use. This response functions as a confirmation, and has no other real functionality. But what if a user wanted to GET information about a chat?
They'd send a GET request of course!

```
GET https://graph.microsoft.com/v1.0/chats/19:b8577894a63548969c5c92bb9c80c5e1@thread.v2
```

And a response of 200 indicates that everything came back OK:

```
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#chats/$entity",
    "id": "19:b8577894a63548969c5c92bb9c80c5e1@thread.v2",
    "topic": "test group 1",
    "createdDateTime": "2021-04-06T19:49:52.431Z",
    "lastUpdatedDateTime": "2021-04-06T19:54:04.306Z",
    "chatType": "group",
    "webUrl": "https://teams.microsoft.com/l/chat/19%3Ab8577894a63548969c5c92bb9c80c5e1@thread.v2/0?tenantId=b33cbe9f-8ebe-4f2a-912b-7e2a427f477f",
    "tenantId": "b33cbe9f-8ebe-4f2a-912b-7e2a427f477f",
    "onlineMeetingInfo": null,
    "viewpoint": {
        "isHidden": true,
        "lastMessageReadDateTime": "2021-05-06T23:55:07.191Z"
    },
    "isHiddenForAllMembers": false
}
```

Here, the entirety of the known information about a chat is returned, from its ID to its URL to its visibility. Again, the actualy variables and values
held within those variables vary from application to application, but the important thing to note is that every piece of information about the resource
is sent back to the front, and the user-side technology can then parse the data for useful or relevant information.

As outlined above, REST is certainly a fast, effective and reliable way to send data back and forth between endpoints. But even these quick requests start
to create a fair bit of overhead - and these are relatively small and polished examples! What happens when requests contain much, much more information?
What happens when we need immediate access to new information, over and over? What happens when only some elements of the information actually change, while
the rest remains static (and fetching the same bit of information again and again is a waste of resources!) Enter GraphQL:



## WebSockets

Here we will talk about Websockets

## Optimization & Testing

Blah blah justify yadda yadda technology

## Conclusion

In summary, some stuff

*link to video here once we publish it to YouTube

## References

Websites or articles we used go here:

Microsoft Graph REST API: https://learn.microsoft.com/en-us/graph/api/chat-post?view=graph-rest-1.0&tabs=http