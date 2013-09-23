Hack.1
======

Goal of the hackathon (Hack 1, H1) is to lay the foundation of an HTTP API based extreme-startup done in Scala. The H1 is only about the HTTP APIs and not about any (G)UI. This document lies down the general direction and the requirements.

## Glossary

H1 - This hackathon, Hack 1.

Server - The extreme-play server that runs the show. 

Player - Stands for a team or their server that participates in the 

## General

* Extreme Server
The server will be written in Play 2.2. 

* Serialization/deserialization format
Json. Play json or Pickling might be used.  

* Storage/Persistence 
This layer should be abstracted. First implementation should only depend on in memory collections like Maps, Lists etc.



## Requirements

The requirements are divided into bite sized pieces and have priorities
 - 0 must have
 - 1 should have
 - 2 nice to have

Each requirement starts with such a priority index. Requirements also leak some of the HTTP details like methods, statuses etc. Prio 2 reqs may not be very detailed and if you are planning to do one that isn't then please start a discussion.


### Testing 

0 - Visibility of a player to the server, before or after registration, can be tested by invoking GET http://{host}:{port}/test?url=http://{another-host}:{another-port}/{path/to/the/resource}. The server should respond within a configurable time limit (default 2 seconds) the results of its attempt to connect and read the resource provided as the query parameter. 

status: 200 OK
response content type : plain/text
http entity/body: Tested: http://{another-host}:{another-port}/{path/to/the/resource} connected


2 - Metrics for time to connect and latency. 



### Registration

0 - As a player I should be able to POST the team's name and the URL (as used in the test above) of my server to register as a player. The server should respond with 400 if I post in the wrong format or, drop name or the URL or both. 409 is used to indicate that either the name or the URL is already taken. A successful request should elicit 201 and a location header with the URL in the format http://{host}:{port}/player/{uuid} where uuid is how a player is identified.

1 - For successful requests the server should also generate two uuid keys. The keys are named player-auth-key, server-id-key. Both keys should be unique. The first key is used by the player to authenticate itself and the second is used by the server to identify itself. These keys should be kept secure. 


### Playing 

### History 

### Leader-board


