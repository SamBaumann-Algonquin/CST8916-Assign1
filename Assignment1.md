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

```
mutation ($username: String!) {
  insert_user_one(object: { username: $username }) {
    id
    username
  }
}
```

GraphQL allows us to forgo the over or underfetching issues RESTful APIs struggle with by using queries, mutations and subscriptions to only access what
they need from a backend server. In the above example, creating a new user is as simple as making a function call on the spot to input an ID and username
into a database (again, actual variable names and values may vary). If we want to be reguarly informed of changes on the backend (such as receiving a new
message from a user), we can utilize GraphQL's Subscription feature to be constantly updated whenever something changes:

subscription {
  message(order_by: { id: desc }, limit: 1) {
    id
    username
    text
    timestamp
  }
}

Here, we subscribe to new message events, and call our message query with a last known ID and timestamp to make sure our user has as much information as
possible as often as possible.

## WebSockets

WebSockets provide a full-duplex communication channel over a single, long-lived connection, allowing real-time data exchange between a client and a server. This makes it ideal for interactive and high-performance web applications.

**Format**: Binary  
**Transport protocol**: TCP  


### Key Concepts and Characteristics

- **Persistent Connection**: Unlike the traditional request-response model, WebSockets provide a full-duplex communication channel that remains open, allowing for real-time data exchange.
- **Upgrade Handshake**: WebSockets start as an HTTP request, which is then upgraded to a WebSocket connection if the server supports it (via the `Upgrade` header).
- **Frames**: Once the connection is established, data is transmitted as frames. Both text and binary data can be sent through these frames.
- **Low Latency**: Direct communication between client and server reduces overhead, enabling faster data exchange compared to repeated HTTP connections.
- **Bidirectional**: Both client and server can send messages independently.
- **Less Overhead**: After the initial connection, data frames require fewer bytes, improving performance.
- **Protocols and Extensions**: Supports subprotocols and extensions for standardized or custom protocols.


### Use Cases

- **Online Gaming**: Real-time multiplayer games requiring instant action updates.
- **Collaborative Tools**: Applications like Google Docs for simultaneous editing.
- **Financial Applications**: Real-time stock price updates in trading platforms.
- **Notifications**: Social media or messaging apps delivering instant alerts.
- **Live Feeds**: News websites streaming updates to users in real-time.


### **Application to Chat Apps**  
- **Workflow**:  
  1. User A sends a message via WebSocket.  
  2. Server broadcasts it to all clients in the channel.  
  3. User B receives the message instantly.  
- **Pros**:  
  - Real-Time Updates: Messages appear without manual refreshing.  
  - Efficiency: Avoids HTTP overhead for frequent updates.  
- **Cons**:  
  - Scalability: Managing many concurrent connections requires robust infrastructure.  
  - Complexity: Requires stateful connections (unlike REST/GraphQL). 


### Example

**Request**  
```http
GET ws://site:8181
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: [hash]
```

---

## Optimization & Testing

To compare and contrast the pros and cons of each approach, we queried our good friend ChatGPT and had it generate a table for us to outline the strengths
and weaknesses of each method (naturally, we made sure to critically examine its output and make sure that it lined up with our findings in parts I and initial
of this document!)

| **Criteria**              | **REST**                                | **GraphQL**                              | **WebSockets**                    |  
|---------------------------|-----------------------------------------|------------------------------------------|-----------------------------------|  
| **Data Fetching**         | Multiple endpoints, fixed responses     | Single endpoint, flexible queries        | Persistent, bidirectional streams |  
| **Real-Time Support**     | Polling required (inefficient)          | Polling required (inefficient)           | Native real-time updates          |  
| **Over-fetching**         | Common (full resource responses)        | Avoided (client-defined queries)         | N/A (event-driven)                |  
| **Scalability**           | Easy (stateless)                        | Moderate (schema/resolver complexity)    | Challenging (stateful connections)|  
| **Use Case Fit**          | Simple CRUD operations                  | Complex, dynamic data needs              | Real-time communication           |  

As we can see above, the table outlines quite clearly that this seems to not be as straightforward as choosing just one! Real-time applications by definition benefit from
having native support, and  barriers such as over-fetching are definite downsides that REST and GraphQL stumble on. The use-case for Websockets fits perfectly with any
real-time system, especially a chatroom application, and if a company such as WhatsApp or Facebook wishes to host, create and update thousands or even millions of 
users, rooms and messages at once, a highly scalable and robust solution would outweigh any downside of additional complexity incurred by a more challenging implementation.

For these reasons, mixing and matching would be our reccomendation for a chatroom application. In the case of a live chat, RESTful APIs would be best suited for the more
static operations such as creating and mainting users within the system. GraphQL can be used for more surgical queries when searching for specific information so as to 
not waste resources by fetching superfluous data, and Websockets are ideal for the actual (and arguably most important) peer-to-peer communication of a real-time chat.

## Conclusion

In summary, some stuff

*link to video here once we publish it to YouTube

## References

Websites or articles we used go here:

Microsoft Graph REST API: https://learn.microsoft.com/en-us/graph/api/chat-post?view=graph-rest-1.0&tabs=http

Building a GraphQL App with Hasura: https://hasura.io/blog/building-a-realtime-chat-app-with-graphql-subscriptions-d68cd33e73f

Bridging the Gap: https://medium.com/@novenkottage/bridging-the-gap-rest-graphql-and-websockets-e27f2a87090a

REST, GraphQL and Websockets System Design Cheat sheet: https://hackernoon.com/the-system-design-cheat-sheet-api-styles-rest-graphql-websocket-webhook-rpcgrpc-soap

ChatGPT: Comparison analysis table