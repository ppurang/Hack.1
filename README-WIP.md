Hack.1
======

Goal of the hackathon (Hack 1, H1) [http://www.meetup.com/Scala-Berlin-Brandenburg/events/139827592/](http://www.meetup.com/Scala-Berlin-Brandenburg/events/139827592/) is to lay the foundation of an HTTP API based extreme-startup done in Scala. The H1 is only about the HTTP APIs and not about any (G)UI. This document lies down the general direction and the requirements.

## Preparation

On that day make sure that you have a github account (https://github.com/signup/free) and heroku account (https://id.heroku.com/signup).

You should prepare the following on your laptops to make sure that you don't loose anytime on that day

1. Install git-core. For example on ubuntu ``sudo apt-get install git-core``

2. Install heroku toolbelt: https://toolbelt.heroku.com/

3. Install play 2.2 : http://downloads.typesafe.com/play/2.2.0/play-2.2.0.zip

4. Install sbt 13: http://www.scala-sbt.org/0.13.0/docs/Getting-Started/Setup.html

5. Clone: https://github.com/ppurang/extreme-play and execute ``play run`` and access localhost:9000  in your browser or use curl. you should see: ``And we have lift off!``

6. Clone: https://github.com/ppurang/extreme-play-test

7. Prepare your IDE. For example for me it is as easy as executing ``play idea`` for https://github.com/ppurang/extreme-play and then I can open the project in Intellij.

Please keep checking this space for more updates. 

## Glossary

H1 – This hackathon, Hack 1.

Server – The extreme-play server that runs the show. 

Player – Stands for a team or their server that participates in the game.

HTTP related – ``http://tools.ietf.org/id/draft-ietf-httpbis`` (payload etc, ``http://tools.ietf.org/id/draft-ietf-httpbis-p2-semantics-23.html``). 

Feature Flag – A configurable property that enables or disables a feature.

## General

* Extreme Server – The server will be written in ``Play 2.2``
* Serialization/deserialization format – ``Json``. Play json or Pickling might be used.  
* Storage/Persistence – This layer should be abstracted. First implementation should only depend on in-memory collections like Maps, Lists etc.
* Collaboration will center around ``https://github.com/ppurang/extreme-play``. Collaborators/Teams should fork the repo and issue pull requests. 
* The server is deployed to heroku at ``http://extreme-play.herokuapp.com/`` 
* Acceptance tests can be found at ``https://github.com/ppurang/extreme-play-test``

## Requirements

The requirements are divided into bite sized pieces and have priorities
 - ``0`` must have
 - ``1`` should have
 - ``2`` nice to have

If two features have the same priority then the order of the feature decides the relative priority.

Each requirement starts with such a priority index. Requirements also leak some of the HTTP details like methods, statuses etc. Prio ``2`` reqs may not be very detailed and if you are planning to do one that isn't then please start a discussion.


### Visibility Testing 

``0`` – <del>Visibility of a player to the server, before or after registration, can be tested by invoking ``GET http://{host}:{port}/test?url=http://{another-host}:{another-port}/{path/to/the/resource}``. The server should respond within a configurable time limit (default ``2 seconds``) the results of its attempt to connect and read the resource provided as the query parameter.</del>

	status: 200 OK
	response content type : plain/text
	http payload: Tested: http://{another-host}:{another-port}/{path/to/the/resource} connected~~


``2`` – <del>Metrics for time to connect and latency.</del> 


### Registration

``0`` – <del>As a player I should be able to POST the team's name and the URL (as used in the test above) of my server to register as a player. The server should respond with ``400`` if I post in the wrong format or, drop name or the URL or both. ``409`` is used to indicate that either the name or the URL is already taken. A successful request should elicit ``201`` and a ``location header`` with the URL in the format ``http://{host}:{port}/player/{uuid}`` where ``uuid`` is how a player is identified. </del>
```json
{
  "name": "SCULK",
  "url" : "http://mystic-wind.heroku.com/play"
}
```

``1`` – <del> For successful requests the server should also generate two ``uuid`` keys. The keys are named ``player-auth-key`` and ``server-id-key``. Both keys should be unique. The first key is used by the player to authenticate itself and the second is used by the server to identify itself. These keys should be kept a secret.</del> 

``2`` – <del>As a player I should be able to ``DELETE`` my account by providing the team's ``player-auth-key`` (as payload) to the ``http://{host}:{port}/player/{uuid}``. This feature should be controlled by a configurable property, ``feature.player.unregister-able: true``, that enables or disables it. </del>

``2`` – <del>As a player I should be able to ``pause`` or ``play`` my account by POSTing the team's ``player-auth-key`` (as payload) to the ``http://{host}:{port}/player/{uuid}``. This feature should be controlled by a configurable property, ``feature.player.pause-able: true``, that enables or disables it.</del>


### Task Repository

``0`` – <del>Model tasks such that pluggable tasks can be registered for use. Something like below:</del> 

```scala
// an atomic task to test a player
trait Task {
	def query : String
	def verify : String => String 
} 

// a generator for a type of Tasks
trait Tasks[A <: Task] {
	def next : A 
}

// repo of such task generators
// Note:  this can be thought of as a random number generator and we could make it referentially transparent 
// and same with the Tasks above
object TasksRepo  {
	def register : Tasks => Unit = ???

	def select : Tasks = ??? 
}	
```

``1`` – <del> create the following tasks type</del> 
  
* ~~Addition tasks (add two numbers, add a list of numbers)~~
* ~~Multiplication tasks~~
* ~~Max in a list of random numbers of random length~~ 
* ~~First x numbers in the Fibonacci series~~



``2`` - ~~Can we generalize mathematical tasks?~~

``2`` - ~~Can we generalize list tasks?~~

``2`` – ~~Adjective tasks like - I have a red apple. What color is the apple?~~

### Playing a Player

``0`` – For each player registered create an actor hierarchy dedicated at playing just that one player.

``0`` – Every ``x`` ms (configurable) select a ``Tasks`` from the ``TaskRepo`` to generate a ``Task`` to invoke on the player. Generate a long ``token`` to capture the sequential generation of (Use a timestamp based ordered key).   

``0`` – Whenever a call to a player 
* fails to connect emit an event ``PlayerDown``
* timesout emit an event ``PlayerTimedOut``
* returns an answer emit an event ``PlayerResponded``


### Scoring 

``0`` – It should be possible to play without points. This should be configurable. 

``0`` – For each task set a max timeout till the player can respond for questions that go unanswered or time out deduct 200 pts. 

``0`` – For each task answered correctly award 100 pts and for every wrong answer deduct 100 pts.

``1`` – Extend tasks to provide possible points to win and possible points to loose. 

``2`` – For every paused minute deduct 100 pts. 

### History 

``0`` – ``GET http://{host}:{port}/player/{uuid}`` should return the history of a player. Example: 

```json
[
 {
  token: "12322e232323",
  "task" : "what is 50 x 1000",
  "response": {
   "status": 200,
   "payload": "5000"
  },
  "score": "-100"
 },
 {
  token: "12322e2324444",
  "task" : "what is 10 x 1000?",
  "response": {
   "status": 200,
   "payload": "10000"
  },
  "score": "100"
 },
 ...
]
```

``1`` – Add response times in milliseconds from the player to the history.

``2`` – ``GET http://{host}:{port}/player/{uuid}/events`` should push history of a players interactions as HTML5 server side events from the server.



### Leader-board

``0`` – ``GET http://{host}:{port}/leader`` should return the latest status of the leader-board as JSON. Example: 

```json
{
  "SCULK" : 10000,
  "NOGO" : -1000,
  "Hello World": 0 
}
```

``2`` – ``GET http://{host}:{port}/leader/events`` should return an endpoint that pushes leader-board changes as HTML5 server side events from the server. 




