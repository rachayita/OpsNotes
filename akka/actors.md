# Actors

- Actor programming is reactive programming. Another way to say this is that it’s event-driven programming.

- Reactive means: readily responsive to a stimulus.

- Strongly Typed Messages, Loosely Typed Endpoints(Actors).

- It’s the reception of that message that carries true meaning in Actor programming. When the Actor pulls that message out of its mailbox and begins processing it, then it has truly received that message.

- An Actor Is Behaviour.

- context exposes contextual information for the actor and the current message, such as:

    ~factory methods to create child actors (actorOf)
    ~system that the actor belongs to
    ~parent supervisor
    ~supervised children
    ~lifecycle monitoring
    ~hotswap behavior stack as described in Become/Unbecome

- Life cycle hooks:
``` scala
    //Right after starting the actor, its preStart method is invoked
    def preStart(): Unit = ()
     
    //After stopping an actor, its postStop hook is called + cleaning process
    def postStop(): Unit = ()
     
    def preRestart(reason: Throwable, message: Option[Any]): Unit = {
      context.children foreach { child ⇒
        context.unwatch(child)
        context.stop(child)
      }
      postStop()
    }
     
    def postRestart(reason: Throwable): Unit = {
      preStart()
    }
```

- It is important to note that Actors do not stop automatically when no longer referenced, every Actor that is created must also explicitly be destroyed. The only simplification is that stopping a parent Actor will also recursively stop all the child Actors that this parent has created.

-> An ActorRef always represents an incarnation (path and UID) not just a given path. Therefore if an actor is stopped and a new one with the same name is created an ActorRef of the old incarnation will not point to the new one.

- ActorSelection on the other hand points to the path (or multiple paths if wildcards are used) and is completely oblivious to which incarnation is currently occupying it. ActorSelection cannot be watched for this reason. It is possible to resolve the current incarnation's ActorRef living under the path by sending an Identify message to the ActorSelection which will be replied to with an ActorIdentity containing the correct reference. This can also be done with the resolveOne method of the ActorSelection, which returns a Future of the matching ActorRef.

- Messages can be any kind of object but have to be immutable. Recommended approach is to use Scala case classes which are immutable.

- ! means “fire-and-forget”, e.g. send a message asynchronously and return immediately. Also known as tell.

- ? sends a message asynchronously and returns a Future representing a possible reply. Also known as ask.

- There are performance implications of using ask since something needs to keep track of when it times out, there needs to be something that bridges a Promise into an ActorRef and it also needs to be reachable through remoting. 
   So always prefer tell for performance, and only ask if you must.

- When using future callbacks, such as onComplete, onSuccess, and onFailure, inside actors you need to carefully avoid closing over the containing actor’s reference, i.e. do not call methods or access mutable state on the enclosing actor from within     the  callback. This would break the actor encapsulation and may introduce synchronization bugs and race conditions because the callback will be scheduled concurrently to the enclosing actor. Unfortunately there is not yet a way to detect these illegal accesses at compile time.

- Termination of an actor proceeds in two steps: first the actor suspends its mailbox processing and sends a stop command to all its children, then it keeps processing the internal termination notifications from its children until the last one is gone, finally terminating itself (invoking postStop, dumping mailbox, publishing Terminated on the DeathWatch, telling its supervisor).

- Since stopping an actor is asynchronous, you cannot immediately reuse the name of the child you just stopped; this will result in an InvalidActorNameException. Instead, watch the terminating actor and create its replacement in response to the Terminated message which will eventually arrive.

- Send an actor the akka.actor.PoisonPill message, which will stop the actor when the message is processed.

- A FSM can be described as:
    State(S) x Event(E) -> Actions (A), State(S’)
    If we are in state S and the event E occurs, we should perform the actions A and make a transition to the state S’.

- The FSM trait inherits directly from Actor, when you extend FSM you must be aware that an actor is actually created.
 

