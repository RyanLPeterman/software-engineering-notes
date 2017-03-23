# Software Engineering
> "Software engineering is about managing risk"

## Main Topics
* Revision Control
* Code Reviews
* Coding Style
* Refactoring
* Testable Code
* Unit Testing
* API Design
* Design Reviews
* Integration Testing
* Build Automation

## Revision Control
All valuable source code resides in source control

* Pros:
    * Reproducible state
    * Backs up work
    * Documents progress
    * Facilitates postmortems
    * Make collaboration possible
    * Legal pedigree (leave an audit trail, establish ownership)
    * Reduces risk
    * More scalable for people to contribute to
* Cons:
    * Adds complexity

Most projects are organized with a central "master" repository. Even distributed
source control tools such as Git use a "master" repo in most teams. The benefit of
using git is that the central repo isn't privileged, which makes it much
easier to fork/branch and share between developers.

#### Git Source Control
Git thinks of repo not as a set of files each with version history, but
a single version repository.

* Makes atomic commits natural across multiple files
* Repo exists as a set of states that are linked to each other

## Testing
If you don't want code to break, write tests for it.

Characteristics of a good test:
* Automated
* Repeatable - deterministic
* Meaningful - tests something important
* Reliable - pass/fail means something
* Easy to run
* Fast
* Checked into version control

Types of testing:
* Unit tests - tests that run on individual components of code
* Integration tests - tests that run on system components integrated together
* Regression tests - tests that run on old code to check if it still performs correctly after
it was changed or interfaced with other software
* Load tests - tests that see how a system handles load
* Fuzz tests - testing a system with random inputs

#### Unit tests
* Tests units of code in isolation
* Written in the same language as the code they test

Benefits:
* Serve as documentation and demonstrate API
* Allows developers to make changes confidently
* Faster in the long run

What to test:
* Boundary conditions
* Preconditions and postconditions
* Be defensive/check for things that can't happen
* Error handling

Hard to test:
* Singletons and global variables
* Long, complex methods
* Objects with state
* Tight coupling
* Bad abstractions

#### Dependency Injection
A technique whereby one object supplies the dependencies of another object. For example,
if some code we wish to test is dependent on some class. We can "mock" or create a fake
object and define its behavior just for the sake of the test so that the test no longer
depends on the other class and instead uses the mock object.


#### Integration tests
* Tests a whole binary or system
* Often written in a scripting language like Bash or Python

## Code Reviews
Another tool to reduce risk. Drastically, lowers errors when another person reviews code.
Also ensures that someone else values and understands the code.

## Build Systems
Desirable qualities:
* Easy to use - One simple step/command
* Repeatable
* Automated
* Checked into version control
* Correct - faithful representation of current source state
* Hermetic - different users, different environments with same results
* Flexible - change options, languages, build rules
* Fast - use build clusters, cache intermediates, and parallelize
* Manages dependencies
* Scalable


### Makefiles
When creating a makefile, targets should compile to intermediates so that
we can re-use them to save CPU time (i.e. object files that didn't change).

#### Syntax:
``` make
<target>: <dependency> <dependency> ...
    # must be tabbed, not spaced
    <build operation>
    <build operation>
```
#### Variables:
To improve maintainability and reuse text, use variables. Note, there are
predefined and special variables such as $(CXX) and $(CXXFLAGS).
```make
# to set
FLAGS=-std=c++11 -Wall -Werror

# to use
$(FLAGS)
```

#### Other useful targets:

```make
test: config_parser_test
    ./config_parser_test
clean:
    rm -f *.o config_parser_main config_parser_test
# This prevents make from treating "clean" and "test" as names of a file
.PHONY: clean test
```

## Static Analysis
A process that inspects the code of your program without executing it directly

For example:
* Compiler warnings
* Type checking
* Linters

Can detect:
* Use after free (some)
* Buffer overrun (some)
* Concurrent deadlock detection (some)
* Uninitialized variables (all)
* Dead code (some)

## Runtime Analysis
Run program in an instrumented runtime (virtual machine) and inspect what happened.

This is much less performant than static analysis usually

## Code Readability
"Code should be written to minimize the time it would take for someone else to understand it."

#### Code Size
Fewer lines of code is usually better, but not always if the solution gets too complex

Short and complex:
```c++
assert((!(bucket = FindBucket(key))) || !bucket->IsOccupied());
```
Longer but simple:
```c++
bucket = FindBucket(key);

if (bucket != NULL) {
  assert(!bucket->IsOccupied());
}
```

#### Commenting
Code with comment should take less time to understand. Avoid needless comments.

Good comment:
```c++
// fast version of "hash = (65599 * hash) + c"
hash = (hash << 6) + (hash << 16) - hash + c;
```
Bad comment:
```c++
// constructor for LocationRecorder
public LocationRecorder() { ...
```
#### Function length
Ask yourself, which function length is more easy to understand to another programmer.

#### Naming
A few simple rules/conventions:
* Put units in quantity names

#### "a < b" vs "b > a"
Put the more variable value on the left, and the more constant value on the right. This
follows from English usage.

* "If you are at least 18 years old."
* "If 18 years is less than or equal to your age"

## Error Handling
Some options:
* Crashing - useful if recovery not possible, can still be high-availability through redundancy
* Returning failure response - error codes, exceptions, boolean success value
* Recovering from errors - requires careful design, and a lot of effort in most cases
* Recording information - crash dump, logging, status page

## Application Program Interface (API)
Public interface/boundary between parts of the software you are building

Good API:
* Easy to use correctly
* Hard to use incorrectly
* Intuitive
* Uniform level of abstraction
* Do one thing well
* Easy to extend
* Hidden implementation details

Bad API:
* Does too many things
* Awkward usage
* Requires careful setup by caller
* Small changes in usage result in huge differences in behavior

If designing an API, start small and simple since APIs are hard to kill and
taking away methods from an API can break existing users easily.

## Design Patterns
Generally reusable solution to commonly occurring problems in software design.

#### Observer
An object, called the subject, maintains a list of dependents, called observers,
and notifies them automatically of state changes by calling one of their methods.

Pros:
* One-to-many notifications
* When notifications should be handled by remote entity

Cons:
* Repetitive

#### Lazy Initialization
Delaying an expensive process until the first time it is needed.

Pros:
* Faster startup times
* Simpler initialization logic

Cons:
* Can lead to unexpected and/or unpredictable runtime behavior

#### Factories
Creation pattern that uses a "factory" methods that deal with creating objects
without having to specify the exact class of the object that will be created.

Pros:
* Self-documenting construction
* Named constructors
* Decoupling the construction of dependencies

Cons:
* Overused

#### Singleton
Pattern that restricts the instantiation of a class to one object.

Pros:
* Process-wide state
* System access

Cons:
* Essentially a global variable
* Harmful when testing

#### Pools/Freelists
Creation pattern that uses a set of initialized objects read to use rather than
creating and destroying on demand.

Pros:
* For managing expensive objects
* Central management of scare resource

#### Resource Acquisition is initialization (RAII)
Holding a resource is tied to object lifetime. Creation allocates the resource, while
deletion deallocates the resource.

Pros:
* Automatically manages resources

Cons:
* Verbose and error-prone in many languages

#### Decorators
Allows behavior to be added to an individual object by using a Wrapper or Decorator.

Pros:
* Separating concerns of layered implementations that share an interface
* Composes implementations in a flexible way

Cons:
* Can't always hide layering behind the same interface

#### Continuation
Programming style in which one passes in a callback function as input to a function
which will be called to "continue" to the next step of the computation.

Pros:
* Compositional lightweight interfaces
* Functional-style programming
* Better in languages with anonymous methods

Cons:
* Can turn code into spaghetti like goto

#### Strategy
Programming style in which one defines a general algorithm, such as run() or execute()
from which the language dispatches to the correct implementation of the function.

Pros:
* When multiple implementations of a particular operation
* Allows code to move into a polymorphic set of objects
Cons:
* Overuse causes unnecessary or convoluted class hierarchies
* Makes it harder to know what's going on

## Anti-patterns
Design patterns that should be avoided.

#### Stringly Typed
When all parameters are strings rather than more meaningful types. Causes problems
since parsing and translation is done at every layer.

#### Unclear Object Lifetime
Lack of RAII object makes ownership less clear. Could be partially solved if the new'd object
is a member of another object. Shared pointers are an option but hard to follow ownership.
Unique pointers are the best solution because they provide a clear understanding of object lifetime.

## Threading and Concurrency
Concurrency allows us to do work while waiting on something else. This keeps multiple CPU cores busy
and makes UIs responsive.

Strategies for concurrency:
* Isolate work - i.e. each request is independent and runs in its own thread
* Use a library - handles management of concurrent resources for us
* Use a mutex - safe, but slows down critical section of code (usually not a problem don't worry about performance)
    * ```std::lock_guard<std::mutex>``` helps us never forget to unlock
* Use a tool - i.e. -Wthread-safety in clang
* Add comments specifying thread safety

## Distributed Systems
Instead of using a giant supercomputer, have many different computers work together as one
giant processor.

#### System level design patterns:
* Master/Worker - useful when problem is data parallel
    * divides the load across several identical jobs
    * coordination is done by master
    * i.e. load balancing
* Dynamic (load-based) Sharding - divides worker's problem across several other workers
    * typically allows for easily adding more workers to increase resources
* Separate routing path from data path - clients communicate directly with backend for data
    * increases total ingress/egress bandwidth
* Admission Control - put a worker that makes sure requests can be serviced before accepting request
    * Prevents memory exhaustion on overload
    * Note: Even just glancing at a request takes some amount of processing and server can still crash
* Distributed Consensus - allows a group of distributed processes to agree on some state change
    * Helps deal with network partitions when some nodes may be unreachable
* Decoupling through Queuing - put a queue in between receiver of data and consumer of data
    * Allows systems to process work at different rates
* Memcache - a cache that each worker talks to to prevent recomputing the same results many times
* Multiversion Concurrency Control (MVCC) - For recent writes, store several versions of corresponding to different time stamps
    * requires garbage collection to clean up duplicative versions

#### System Level Antipatterns:
* Store meta data in the system itself
    * this can cause problems if the system goes out we lose access to meta data (i.e. Amazon S3)
    * requires even higher QoS tiers to make sure meta data operations succeeded
* Synchronous Global Configuration Roll-out
    * all processes consult a central config for current config values
    * if the global config is bad, can cause global simultaneous failure

#### Known Hard Problems:
* CAP Theorem
    * can only have 2 of Consistency, Availability, Partition Tolerance
* Commits Across Atomicity Domains
    * What you commit to two subsystems and one fails?
    * Make operations idempotent
* Balancing Quality of Service and Utilization
* Roll-outs and Protocol Upgrades
    * In order to do, make a sequence of backwards compatible changes
* Reprocessing
* Moving Data

#### Failure Modes:
Correctness Bugs:
* Can be detected with regression tests
* Systems are often data-parallel so test can be run with fraction of the resources

Performance Bugs:
* Hard to catch in tests
* Needs full scale and load to properly test

Problems:
* Producer-Consumer Rate Mismatch - Consuming/Adding to Queue at different rates
* Hot spots - load is concentrated on a small working set
* Amplified Rare Events - at scale rare problems can become common

#### Debugging Techniques
* Status pages
* Logging
* Local Request Tracing
* Distributed Tracing

## Performance and tuning
Some performance metrics:
* Queries per second (QPS)
* Latency
* Memory footprint
* Bandwidth
* Concurrent connections

## Postmortems
A report on a recent failure that occurred should include:

* Title
* Summary
* Impact
* Root cause
* Time line
* What went well/poorly
* Action items

Retrospective - just like a postmortem, but no outage
    * What went well, and what could go better?