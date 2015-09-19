Hyperflow - goal oriented hypermedia workflow

A system for which enables hypermedia clients to express a goal which the server directs them to achieve. This differs from traditional clients in which the server(s) generally do not have the context of what the client is trying to do.

Sample use cases

* Attending an event
* Purchasing a house

## Team
* [Anya Stettler] (https://github.com/anyarms)
* [Daniel Yokomizo] (https://github.com/dyokomizo)
* [Glenn Block] (https://github.com/glennblock)
* [James Snell] (https://github.com/jasnell)
* [Thomas Hovel] (https://github.com/thovell)
* Misha put your info here :-)

## Why Care? Why do we need this? What is different?

* Not top-down workflow
* Clients focus on the goal rather than the rel
* Additional workflows can be discovered as part of an existing workflow
* Allows work to be highly distributed across many systems
* Can model complex business processes across multiple vendors
* Does not require third party systems to be coordinated
* Can build on existing standards
* Client is in control

## How does it work? 

### Concepts
* `Context` - A JSON object which contains the goal that the client is trying to achieve, and data related to the achievement of that goal. It is a JSON-LD document.
* `Activity` - A JSON object which contains one or more `Steps` which may be executed in parallel.
* `Step` - A JSON object which contains two links:
  * A link to a resource which can be a task or another workflow. 
  * A link to a completion callback for the `Step`.
* `Worklow` - A JSON object which contains one or more Activities which are necessary to complete a goal.
### Actors
* `Client` - The client who is trying to achieve a goal
* `Context Controller` - Manages persistence of the `Context`
* `Workflow Controller` - Manages the overall workflow to help the client achieve it's goal. Ensures the `Context` is always valid. Creates `Activities` for the client to navigate the workflow.


### Part I - Stating the initial goal
![](https://raw.githubusercontent.com/glennblock/2015-Greenville/master/images/Goal%20driven%20hypermedia%204.png)

1. Client sends a `Context` to a `Context Controller` to express the goal it wants to perform. This `Goal Context` contains the goal description and related data.
2. `Context Controller` persists the `Context resource`. (add link here)
3. `Context` is sent to the `Workflow Controller`.
4. `Workflow Controller` validates that the `Context` contains the pre-requisite information.
5. `Workflow Controller` returns an activity to the client
  * If the `Context` is valid, the controller returns an `Activity` containing a list of possible steps to the client, some of which are required and all off which may be performed in parallel. 
  * If the `Context` is not valid, the controller returns an `Activity` which contains `Steps` for making the `Context` valid. 
  * Each `Step` within the `Activity` has a link to a `Task` or A `Workflow`. The `Step` also contains a callback link to the `Context Controller` to say that the `Step` has completed.
  * There is a also an `Activity` completed callback link which points to the `Workflow Controller`.

### Part II - Completing the activity and moving on
![](https://raw.githubusercontent.com/glennblock/2015-Greenville/master/images/Goal%20driven%20hypermedia%205.png)

6. The client follows a `Step` within the `Activity`.
7. The client follows the `Step` completion callback passing in a new `Step Context` which contains the data for the `Step`.
8. The `Context Controller` persists the `Step Context`. It then makes a copy of the `Goal Context` which contains a link to the `Step Context`. 
9. If there are more steps to be performed, goto step `6`.
10. The client invokes the `Activity` completed callback.
11. The `Workflow Controller` validates that all required `Activity Steps` were completed.
12.
  * If the `Activity` has not been completed, it will return a new `Activity` with the remaining `Steps` that need to be completed. 
  * If the `Activity` has been completed, then it will return a new `Activity` containing the next of set of `Steps` in the `Workflow`.
13 - Goto step `6`

## Whiteboard

![](https://raw.githubusercontent.com/restfest/2015-Greenville/master/images/Goal%20driven%20hypermedia%201.jpg)
![](https://raw.githubusercontent.com/restfest/2015-Greenville/master/images/Goal%20driven%20hypermedia%202.jpg)
![](https://raw.githubusercontent.com/restfest/2015-Greenville/master/images/Goal%20driven%20hypermedia%203.jpg)

## Candidate domain model

planning -> bookings
reconsiliation -> transitions

-Goal/State
  - need
    - preference
      - policy
    - requirement

planning:
  Advice
    potential-transition(state1, state2)
      option1
      option2
      option3

  rank(risk(needs), fulfill(needs)) = match(option1, needs)
  rank(risk(needs), fulfill(needs)) = match(option2, needs)
  rank(risk(needs), fulfill(needs)) = match(option1, needs)
  rank(risk(needs), fulfill(needs)) = match(option2, needs)

  plan
    booking1(option1)
    booking2(option2)

reconsiliation
  executed-transition(booking)


advice provider
ranker
planner
reconsiler
