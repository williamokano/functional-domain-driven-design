# functional-domain-driven-design
A pragmatic and balanced approach to combine DDD, FP, hexagonal architecture, microservices all together with Kotlin.

## Introduction TL;DR

As a developer, I've been working almost all my career in JS and java, imperative OOP and layered architectures,
through different companies and variety of domains.

Some years ago a decided to open my mind, I learned functional programming with Scala, at least I tried, my head almost exploded.
I also tried different programming languages, such as ruby, elixir, typescript or even ocaml, till finally in I fell in love
with kotlin .

One day I realised that I didn't know what the S of Solid really meant, so it led me to understand dependency-inversion and
the architectures based on it, I fell in love again, this time with hexagonal architecture.

Parallel to all of that, I learned that software engineers should not work alone and isolated, we need to work together,
 so, welcome agile and [XP](https://en.wikipedia.org/wiki/Extreme_programming).

What was next? divide and conquer! microservice architectures, time to create small, autonomous and decoupled services, well I
thought I was. Actually, I was going more to the path of creating distributed monoliths.

Still, something was missing, that's when I was introduced to domain-driven design. Wow, how it was possible to work all
those years without paying attention to the most important thing? **The business**.

Let's put everything together:

<p align="center">
  <img width="55%" src="doc/img/cloud.png">
</p>

Wow, a lot of fuzzy words, right?

Now, I still feel that I know nothing, I had to review concepts time to time, but in this project I will try apply DDD,
FP, hexagonal, microservices and kotlin in a real complex scenario in order to see how powerful they are.


## The problem

Software is meant to solve problems, therefore, let's imagine something to solve then ...

**Note**: This section and the following one are about DDD, how decisions are affecting top-down, we will break down the
problem till get to implementations details, so feel free to jump directly to the [implementation section](#the-implementation) if you are not
interested, or you already master all the DDD concepts.

### The domain - Give me the loan!

In DDD terminology, the domain is the group of business problems we are trying to solve usually associated with one activity,
or more simple, what do we want to solve? In our case our imaginary activity is an online company that gives **Fast Personal Loans**
called **Give me the loan!**.

The idea is pretty simple, you download the mobile app, create an account, take one **photo** of your **ID** and some of your
last **payslips** and request for a personal loan with a **very low interests!**.

<p align="center">
  <img width="70%" src="doc/img/give-me-the-loan.png">
</p>


### Discovering the domain

We, as developers, are eager to code, but in order to do it efficiently, let's understand what we have to do first.
In DDD world, this part is the strategic part, a crucial aspect of DDD, discover the domain, break it down in
sub-problems, loose-coupled parts that we can tackle autonomously, **the sub-domains**.

There are some techniques to do so, but one of the most effective and quickest way to do it is a **big-picture [event-storming](https://www.eventstorming.com/)**,
a workshop-based method where we only need a big space, a wide wall, a lot of sticky notes and the right people.

<p align="center">
  <img width="70%" src="doc/img/event-storming.png">
</p>

I am not an expert of running this kind of workshop, and the goal of this post is not to explain it; but in a nutshell,
an [event-storming](https://en.wikipedia.org/wiki/Event_storming) is a collaborative and visual tool that can be used for
several purposes, one of them is to discover and decompose a domain into subdomains through domain events, which will
discovered and clustered by development teams and domain experts together (If you are already interested [here](https://github.com/ddd-crew/eventstorming-glossary-cheat-sheet) some tips.)

Coming back to our cool problem, let's suppose we have run this session with all the stakeholders and here the subdomains
as a result:

<p align="center">
  <img width="70%" src="doc/img/sub-domains.png">
</p>

As you can see we also identified the different [types](https://thedomaindrivendesign.io/domains-and-subdomains/) of subdomains,
the **core** ones, the parts of the domain which have the greatest potential for business impact, supporting subdomains,
without them our Domain cannot be successful, and generic subdomains, the ones that could be even outsourced.

During these sessions we also spotted that a Loan have different meanings depending on the subdomain, we are discovering
the **ubiquitous language**, another DDD concept.

## The solution

### Bounded context

We have our company divided in different subdomains, we already know which ones are important, but what about teams, services,
repositories, in summary, **what about boundaries?**

Here it comes, one of the most important concepts of DDD, yes, this word so complicated to explain, the Bounded Context,
I really like to make an analogy with FP here, BCs are the monads of DDD, they are super important but everyone struggles
 to explain them.

But, what is exactly a bounded context?

> A bounded context is a delimited context that define explicit boundaries in terms of organization, concepts and vocabulary,
application, teams, code or even data, most of the times within a subdomain.

Still broad and fuzzy?

Let's simplify it, a BC is just the other side of the problem, **the solution, how you solve your domain problem**.

Instead of try to elaborate a simple definition, we can define a set of rules to understand it better:
- A BC often maps one-to-one with a subdomain, but they cannot.
- A BC is owned by one, and only one team, but one team can own several BCs.
- A BC usually maps one-to-one with a service, but it can be split in several ones if the team decides to.
- A BC owns a set of concepts and vocabulary, shared by team members, domain experts and source code.
- A BC should be as autonomous as possible, enabling teams to deliver faster and independently of each other.

<p align="center">
  <img width="35%" src="doc/img/BC.png">
</p>

#### Bounded contexts and microservices

The original promise of microservices is to allow your teams to release frequently and independently of each other ... sounds familiar?
Yeah, bounded context and microservices are trying to achieve the same at different levels, that's why they are a perfect match!

<p align="center">
  <img width="40%" src="doc/img/MS-love-BC.png">
</p>

### Loan Evaluation Context

We are lucky, we have assigned to the team in charge of Loan Evaluation, one of the core bounded contexts, but, what we know
about it?

Luckily, we have tools in place, you remember the event-storming? the previous one was the big picture version, but there is
another one, a **Software Design Event-Storming**, a more granular version where we will discover the moving parts of a our software
implementation, again, developers and business experts together.

[diagram: TODO]

This exercise will give us an idea about the business workflows in terms of:

- Aggregates: In our case, only one, `Loan Evaluation`, it will be reflected in the code.
- Domain Events: **Things that happened in the domain that are relevant for the business, it does not mean that they will
be events in our implementation, they could be events or just states in our model**. Some of them will be published
to the outside world, the integration ones, committed events that occurred within our bounded context which may be interesting
to other domains, applications or third party services. We'll see later how they will be implemented.
- More shared model, more ubiquitous language, commands and events give us an idea of which methods, functions or domain components.
we will have in our code.
- Dependencies: TODO
- Policies: a.k.a. business rules
- UI: Not applies here.


In summary, an idea of how our solution will look like.

### Business Workflows as pipelines

We have been tacking about DDD concepts enough, we have our domain divided, we have aggregates, domain events, we know
how our solution would look like but now is time to introduce some functional concepts at business level.

What is a function?

<p align="center">
  <img width="35%" src="doc/img/fn.png">
</p>

We can see it black box with an input and output.

If we chain them, we obtain a [pipeline](https://martinfowler.com/articles/collection-pipeline/), a really common pattern in functional programming.

<p align="center">
  <img width="70%" src="doc/img/pipeline.png">
</p>

Cool, and what if we try to apply the same concepts, thinking in data flow transformations at business level?

<p align="center">
  <img width="80%" src="doc/img/workflow.png">
</p>

Our pipeline is now a workflow, we just change the name ;)

After the event storming and talking with domain experts we also know which dependencies we are going to have,
let's add them to our workflow.

<p align="center">
  <img width="80%" src="doc/img/workflow-with-dependencies.png">
</p>

Now one step back, put the workflow in the bounded context:

<p align="center">
  <img width="80%" src="doc/img/whole-workflow.png">
</p>

Looking at the workflow as a pipeline, we are able break down the work in a small steps that we have to follow to fulfill
our goal, evaluate a loan.

### Communication with other bounded contexts

// diagram with messages http an so on

## The implementation

### Applying hexagonal architecture

To create a micro-service we need an architectural pattern to organise the code and its different concerns, in our case
we are going to apply hexagonal architecture (a.k.a. ports & adapters), since it is domain-centric approach, it is the perfect
chassis for our DDD project.

<p align="center">
  <img width="80%" src="doc/img/ddd-loves-hexa.png">
</p>

Explain hexagonal architecture is not the goal of this project, ([here](https://github.com/albertllousas/implementing-hexagonal-architecture) a depth explanation), but in a nutshell
hexagonal architecture is just a way to apply dependency-inversion (S of SOLID). You could think about hexagonal as [Encapsulation](https://en.wikipedia.org/wiki/Encapsulation_(computer_programming)) applied at business level.

<p align="center">
  <img width="80%" src="doc/img/hexa-encapsulation.png">
</p>

From the external world perspective, we are going to expose certain functionalities through the ports, the only way to
access to the inner hexagon, our business.

Hexagonal together with DDD looks like this:

<p align="center">
  <img width="80%" src="doc/img/hexa-ddd.png">
</p>


### Anemic domain model anti-pattern in FP

### Declarative and type-driven workflows

*Types* : think in types declarative type driven, what I want to do? Construsct the pipeline as you want talking business language, someone will implement it

Pipeline == workflow== usecase == aplication services in ddd==  [diagram]

Just write the pipeline with types, with tdd comes natural

declare what we want to do at business level (application service)

declarative programming

type what you want to do, type the pipelime

diagram and code

add types, we don't care about who is going to impl

### Code that talks the business language

### Error handling: Monads come to the party

*Monads* are a functional pattern, I am not going even try to explain, but they are useful for several purposes, one of them error handling
Familiar with Railway programming?
We have pipes we chain functions, let add errors to the equation

### DDD building blocks

### Wiring up everything

#### External dependencies

// diagram with inmemory and so on

## Run the project

### Running the tests
















