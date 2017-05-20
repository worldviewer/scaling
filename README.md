# The Scaling Problem

The following advice on scaling comes from a brilliant Airbnb engineer (and former world-class poker player), Haseeb Qureshi.  A lot of this knowledge is born of hard knocks experiences which you do not necessarily want to personally experience.  And although some people might call these "problems you want to have", IMO they can be fairly interpreted as a lesson to keep an eye out for something better.

People will of course differ on the best way to solve this complex problem, and CEO's and CTO's would be wise to hear out all opinions.  The important part is that anybody who is trying to create a technology startup should understand at a high level the reasoning at any particular moment that justifies their _why_ and _how_ for addressing the scaling problem.  It is arguably one of the most fundamental issues which any startup that does not have access to incredible funding will face -- and adding to the danger is the fact that **the early decisions about what tech stack to go with will commit most startups to this sequence of events.**

Part of the intention of this document is to wonder aloud if this is all really necessary.  Is this really the best we can do?

## First, Some Basic Concepts and History of Web Architecture

### The Monolithic MVC Architecture

- **M** = Model
- **V** = View
- **C** = Controller

A _model_ is a specification for retrieving data from a source like a database.  A _view_ is a webpage -- sometimes called a _template_ -- which combines static and dynamic content.  _Static_ content is of course the content which never changes from one request to another, whereas _dynamic_ content is the reason we need a model; it's the stuff on a page that changes from one user or product to the next.

A _controller_ is the logic which handles interactions with the client (the user's computer), and which acts as the glue that runs the whole show.  In the traditional MVC architecture (as you'd see with something like PHP), this controller generally exists on a server -- the "website".  It transforms incoming requests for webpages into outgoing pages by determining what data is needed, fetching that data through the model layer, and then rendering the view embedded with that data back to the client.

Of course, data also flows the other way -- like when a user creates a password, when they submit a form with data, or when they upload a file.  In all of those cases, the model layer is used to write data _into_ the database.

A diagram is helpful here:

<p align="center">
    <img src="https://github.com/worldviewer/scaling/blob/master/img/mvc.jpg" />
</p>

The key word here is _monolithic_ because the user's computer -- the "client" -- is not really doing a whole lot, and most of the work is happening on servers which are to some extent dedicated to your site.

It's important to emphasize that these servers require _devops_ technicians (devops = "Development and Operations") who have a deep understanding of how to fix them when they break.  A devops engineer is not necessarily a developer; those developers who posess the requisite devops knowledge to untangle a devops mess -- of course, under pressure of the site being down -- are truthfully a rare breed, and as I will argue, increasingly unnecessary.

To illustrate in a really vague manner the type of situation we have with "systems" and "code" specialists, here is another diagram:

<p align="center">
    <img src="https://github.com/worldviewer/scaling/blob/master/img/fullstack-venn-diagram.jpg" />
</p>

### The Evolution of "Cloud" Deployments

#### Infrastructure as a Service (IaaS) aka Virtual Machines

> "IaaS - Infrastructure as a Service is one of the big three models of cloud computing and operates by providing virtualized hardware as a resource. This means that there is no single server or other piece of hardware that does the required work. Requests are filled using parts of actual hardware across a number of networks." (_AWS Lambda_ by Darryl Barton)

- Best example: Amazon Web Services (AWS)
- Can be a lot of work to setup, maintain and debug due to high complexity
- Was created before the introduction of the auto-scaling concept:

> "Amazon Web Services launched the Amazon Elastic Compute Cloud (EC2) service in August 2006, that allowed developers to programmatically create and terminate instances (machines). At the time of initial launch, AWS did not offer autoscaling, but the ability to programmatically create and terminate instances gave developers the flexibility to write their own code for autoscaling." (wikipedia)

#### Platform as a Service (PaaS)

> "PaaS - Platform as a Service provides hardware and software to users so that they don't have to maintain the cumbersome hardware infrastructure themselves in order to develop new applications." (_AWS Lambda_ by Darryl Barton)

- Best example: Heroku
- Easier to setup and use, manages most of the hard stuff for you
- Like IaaS, didn't start out with auto-scaling, but is evolving into it
- Pre-packaged configurations, reducing complexity
- Can be more expensive than IaaS

#### Serverless, aka Function as a Service (FaaS)

> Serverless - "a cloud computing code execution model in which the cloud provider fully manages starting and stopping of a function's container platform as a service (PaaS) as necessary to serve requests, and requests are billed by an abstract measure of the resources required to satisfy the request, rather than per virtual machine, per hour.

> Despite the name, it does not actually involve running code without servers. The name 'serverless computing' is used because the business or person that owns the system does not have to purchase, rent or provision servers or virtual machines for the back-end code to run on." (wikipedia)

- Best example: AWS Lambda
- A very recent innovation (AWS Lambda launched in 2014), awareness still fairly low
- Especially suited to deploying limited-scope "microservices" that can rapidly "spin up" from sleep on demand to thousands of concurrent instances, solve just one small problem, then just as quickly spin back down
- The key innovations here are *infinite scalability* (baked in from the start!) and a system of *billing* which enormously benefits lean startups (if the support plan is removed from the subscription, the first million hits to your AWS Lambda endpoints are free), providing the startup with ample time to achieve profitability
- Best if used in conjunction with the Serverless framework (https://serverless.com/), which supports a simple Node-based wrapper and simplified deployment tools to accomplish a wide variety of backend tasks
- Low complexity (any Node developer should be able to figure this stuff out, even junior Node dev's)
- No "operations" experience required as with virtual machines -- just developers

There is one more acronym you really need to know ...

### The Content Deliver Network (CDN)

<p align="center">
    <img src="https://github.com/worldviewer/scaling/blob/master/img/cdn.png" />
</p>

- Serving assets from your own server (left) vs serving assets from a CDN (right)
- Good at shoveling data (static content like code and images)
- Tons of locations, globally accessible
- Faster than AWS
- Never serve assets from your own servers (this creates high _latency_, which is delay to page render)

### The Traditional Scaling Process

Now, it's one thing to say, _"Hey, look at this new shiny thing. Isn't it pretty?"_, but to truly get why it's so great, we really need historical context.  We must ask: _What is so bad about the old way of scaling?_

We must also take the time to understand: _What are the risks, or downsides, to adopting this new technology?_

This is where Haseeb's explanation of how startups have traditionally scaled can provide us with some contrast.

## If it's Slow --> What is Slow? --> Work Backwards to a Solution

### Caching

You're just starting out.  Maybe the site is at this point running on a machine you've got in your bedroom, things are catching on, and you are starting to notice that a piece of a page is very slow.  So far, you've not done anything to actually speed your site up.  What should you do?

The first thing you should always try when you sense a scaling problem is caching.  Check to see if you have a complex database query which can be cached.  The logic behind this approach is that databases are stored on hard drives, which are notoriously slow ...

<p align="center">
    <a href="http://norvig.com/21-days.html#answers">
        <img src="https://github.com/worldviewer/scaling/blob/master/img/speed-of-operations.png" />
    </a>
</p>

For the sake of understanding the table ...

Mutex:

> "In computer programming, a mutex [mutual exclusion] is a program object that allows multiple program threads to share the same resource, such as file access, but not simultaneously. When a program is started, a mutex is created with a unique name." (http://www.webopedia.com/TERM/M/mutex.html)

L1 / L2:

> "Cache memory is a special memory used by the CPU (Central Processing Unit) of a computer for the purpose of decreasing the average time required to access memory. Cache memory is a relatively smaller and also a faster memory, which stores most frequently accessed data of the main memory. When there is request for a memory read, cache memory is checked to see whether that data exists in cache memory. If that data is in the cache memory, then there is no need to access the main memory (which takes longer time to be accessed), therefore making the average memory access time smaller. Typically, there are separate caches for data and instructions. Data cache is typically set up in a hierarchy of cache levels (sometimes called multilevel caches). L1 (Level 1) and L2 (Level 2) are the top most caches in this hierarchy of caches. L1 is the closest cache to the main memory and is the cache that is checked first. L2 cache is the next in line and is the second closest to main memory. L1 and L2 vary in access speeds, location, size and cost." (http://www.differencebetween.com/difference-between-l1-and-vs-l2-cache/)

Branch misdirection:

> "Branch misprediction occurs when a central processing unit (CPU) mispredicts the next instruction to process in branch prediction, which is aimed at speeding up execution." (https://en.wikipedia.org/wiki/Branch_misprediction)

The important part of that is to notice just how much faster retrieving something from a cache can be compared to network and disk requests.

There are many ways to cache something, but two are worth mentioning here:

*Fragment Caching:* This is more than just caching the data.  It's saving a giant string with rendered HTML and CSS.

*HTTP Caching:* Every time you get a page, the HTTP headers contain a version number that informs the server of the last time you visited the site.  If the page has not since changed, the server says no, your version is updated, and you serve the page to yourself, based on what you last saw.

### Blocking

Something on your page is slow again, and it seems to happen when new data appears on the page.  This can happen if you're using something like PHP, but would be much less likely to occur on a Node backend.  It seems that nothing can happen while you wait for this new data to appear on your page.

You've got a blocking thread.

> "An example of synchronous, blocking operations is how some web servers like ones in Java or PHP handle IO or network requests. If your code reads from a file or the database, your code "blocks" everything after it from executing. In that period, your machine is holding onto memory and processing time for a thread that isn't doing anything.

> In order to cater other requests while that thread has stalled depends on your software. What most server software do is spawn more threads to cater the additional requests. This requires more memory consumed and more processing." (http://stackoverflow.com/questions/10570246/what-is-non-blocking-or-asynchronous-i-o-in-node-js)

Your backend I/O accesses need to be asynchronous, non-blocking.  What that means is that when you make a request to your (unavoidably slow) disk, your server does not sit there and wait for the response.  It instead simply registers a callback in a queue of asynchronous callbacks.

<p align="center">
        <img src="https://github.com/worldviewer/scaling/blob/master/img/non-blocking.jpg" />
</p>

This is a bit like saying, _"We're working on that, you go and do your thing, and I'll get back to you when the data is ready."_

Node.js comes with this ability baked in.  All network and disk accesses are today handled with what are called promises ...

```javascript
new Promise((resolve, reject) => {
    request(url, (err, response) => {
        if (err) {
            reject(err);
        } else {
            resolve(response);
        }
    });
});
```

### The CPU / Memory Usage is Too Damn High

Your site is slow again.  This time, you can tell that your server is pegged.  What do you do?

You're starting to enter dangerous territory at this point known as "operations" ("devops").  The risk you face is that the complexity of your system is about to drastically increase.  You should do everything you can to avoid that: Put simply, you should buy a better server.  Don't scale horizontally until you absolutely have to.

<p align="center">
        <img src="https://github.com/worldviewer/scaling/blob/master/img/horizontal-vs-vertical-scaling.png" />
</p>

Horizontal scaling is complex and expensive.  To make it work, all of your application servers must be _stateless_.  In other words, every single request is like a blank slate, because there's no real guarantee that the same machine handled the last client interaction.  All of your state is going to have to exist in either your database or your cache.

Making matters worse, you're now going to need load balancers to evenly distribute the incoming requests.  All of this added complexity creates more failure points.  Things are going to start breaking.

<p align="center">
    <img src="https://github.com/worldviewer/scaling/blob/master/img/load-balancing.png" />
</p>

> "Your centralized load balancer secretly hates you. You just haven't realized it yet ...

> Lets rip off the band aid: It might work well enough for brochureware, but for web-based applications, centralized load balancing is just a dumb idea. It started out as a hack to make a bunch of servers look like one server; a workaround for the fact that HTTP and the web wasn't designed for modern applications. It's an unnecessary middleman and a bottleneck. It forces you toward certain [structures] that are not suitable for the modern web. It doesn't like you, and it doesn't like your friends." (http://abnorman.com/rantings/2014/7/28/unbalanced)

### Globalization

Your site is slow again.  But, this time, it's a latency problem, and some geographic regions are worse than others.

The solution is of course globalization; you need horizontal scaling that is geographically targeted.

<p align="center">
    <img src="https://github.com/worldviewer/scaling/blob/master/img/algolia-global-latency.png" />
</p>

The thing to know about this is that you may be able to get away with compartmentalizing the problem.  A case-in-point is Algolia Search.  What you do is you upload the data which needs searching to their servers, and that data is then distributed to _their_ global network.  This is a great low-cost solution for search because it enables autocompletion to work regardless of where on the planet the user is searching from.

<p align="center">
    <img src="https://github.com/worldviewer/scaling/blob/master/img/autocompletion.gif" />
</p>

The point with globalization is that you should not immediately jump to this idea that you need to place your entire app on servers that are spread out over the world.  You should check if the solution can be specifically targeted to your bottleneck features.

### Full-table Database Scans

Your site is slow again, and this time it's your huge database.  Your monolithic database is buckling under the load.  Before you think about scaling it horizontally, you need to first optimize it to make sure that you are *never* doing a full-table scan of your huge database, which is a complete waste of time.

A full-table scan is when your database searches have to check every single key before they find the record you are looking for.  This is what is called an O(n) complexity -- which in algorithm-speak translates to "really sucks".

A better situation would be if your database search was able to rule out half of the records with each index it checked on its way to finding the one you want.  This is known as a sorted binary search tree.  Your search time would then reduce from O(n) to O(log n), a vast improvement when n is large.

Notice how fast it would be, for instance, to get to 67 in the binary search tree below (3 steps).  If this was a table index, it would be the very last item (10 steps).

<p align="center">
    <img src="https://github.com/worldviewer/scaling/blob/master/img/binary-search-tree.png" />
</p>

The trade-offs with this is that it takes up more space, and when you do an update, you now have to update two different things.  So, all of your writes are now slower.

### Denormalizing the Data

> "The root cause of 99% of database performance problems is poorly written SQL code. Quite often poorly designed SQL code can be the result of an inappropriate underlying table structure. The table structure may be over Normalized forcing SQL code to join many tables to find a single item of information." (http://oracledba.ezpowell.com/oracle/papers/Denormalization.htm)

> Database Normalization, or simply normalization, is the process of organizing the columns (attributes) and tables (relations) of a relational database to reduce data redundancy and improve data integrity. (wikipedia)

What they're saying is that joins are bad.  Imagine you spent all of this time finding the right key in yor database table only to be referenced to yet _another_ table, and the process repeats some number of times.  These sorts of things aren't planned; they might slip into the architecture as new features are built out.

The graphic below demonstrates the process; notice in the bottom half that data is being taken from one table and inserted into another, reducing the number of hops from two to one.

<p align="center">
    <img src="https://github.com/worldviewer/scaling/blob/master/img/denormalization.jpg" />
</p>

The downside of this process is that it is a risky transformation.  If something goes wrong in the process, the data can become inconsistent.  You better hope that the problem is apparent before new data starts going into that corrupted database, or it's game over.

### Expensive SQL Queries (aka N+1 Queries)

Your site is slow.  And it's the database again.  And in the process of talking it through, you realize that your junior developer has placed a SQL query inside of a loop.

You are sending a shit-train of queries when you could just write one.  The train of requests is overwhelming your database.

<p align="center">
    <img src="https://github.com/worldviewer/scaling/blob/master/img/n-plus-1-query-problem.jpg" />
</p>

```javascript
// bad
User.friends().forEach(friend => {
    friend.getFromDatabase();
});
```

### Horizontal Database Scaling

Recall the Algolia example.

<p align="center">
    <img src="https://github.com/worldviewer/scaling/blob/master/img/algolia-global-latency.png" />
</p>

When you change your search data, how does this fresh data get to all of those other geographical endpoints?

They let you upload your data to the nearest server, and that fresh data then propagates to all of the others.

That is called a fully-distributed database (which we'll get to in a moment).  The leader-follower replication model is sort of like that, except it centralizes writes into one machine.  Writes always go to the same leader machine, but that information then distributes to an array of machines which service reads.

<p align="center">
    <img src="https://github.com/worldviewer/scaling/blob/master/img/leader-follower.png" />
</p>

This is great because most apps have very high ratios of read-to-writes.  But, of course, the leader-follower replication model sometimes doesn't work -- like if you have a write-heavy load.

### Distributed or Sharded Database

Take the leader-follower model, and now create leaders for each geographic region.

> "A database shard is a horizontal partition of data in a database or search engine. Each individual partition is referred to as a shard or database shard. Each shard is held on a separate database server instance, to spread load.

> Some data within a database remains present in all shards,[notes 1] but some appears only in a single shard. Each shard (or server) acts as the single source for this subset of data." (wikipedia)

https://www.atlantic.net/blog/why-a-distributed-database-is-used-and-types-of-distributed-data/

> Why are distributed databases becoming so popular?

> May 25, 2014 by Adnan Raja

> Many companies have left behind centralized databases in favor of distributed databases (in which the database, as its name implies, is distributed throughout an array of servers in various locations), for a variety of reasons. Let’s look at some of the basic advantages of distributed databases, a typical scenario in which they are used, and the different formats in which data is distributed throughout the system.

> Why distributed databases are becoming increasingly popular

> Here are the basic reasons why the centralized model is being left behind by many organizations in favor of database distribution:

> Reliability – Building an infrastructure is similar to investing: diversify to reduce your chances of loss. Specifically, if a failure occurs in one area of the distribution, the entire database does not experience a setback.

> Security – You can give permissions to single sections of the overall database, for better internal and external protection.

> Cost-effective – Bandwidth prices go down because users are accessing remote data less frequently.

> Local access – Similarly to #1 above, if there is a failure in the umbrella network, you can still get access to your portion of the database.

> Growth – If you add a new location to your business, it’s simple to create an additional node within the database, making distribution highly scalable.

> Speed & resource efficiency – Most requests and other interactivity with the database are performed at a local level, also decreasing remote traffic.

> Responsibility & containment – Because any glitches or failures occur locally, the issue is contained and can potentially be handled by the IT staff designated to handle that piece of the company ...

What they're not telling you is that this introduces significant complexity into the system.  This stuff is not easy to implement, and debugging becomes very tricky.

### There are Too Many Developers Touching the Same Code

When you've got multiple developers working on the same code -- like with a monolithic codebase -- you get merge conflicts.  This can slow down development and act as a vector for error, and it's one of the reasons that companies break up their codebase into microservices.

In an ecosystem of microservices, each service has its own database and there is no single point of failure.  But, if dependencies exist between them, failures can propagate through them.

<p align="center">
    <img src="https://github.com/worldviewer/scaling/blob/master/img/microservices.png" />
</p>

You might think it makes sense to start a codebase as microservices rather than slowly evolving into the paradigm, but it's not necessarily the case that it's understood early on how such a split system should be architected.

One approach to microservices is to use it as a targeted process for addressing system bottlenecks.

### The Story of Pokemon Go

<p align="center">
    <img src="https://github.com/worldviewer/scaling/blob/master/img/pokemon-go.png" />
</p>

> "Within 15 minutes of launching in Australia and New Zealand, player traffic surged well past Niantic’s expectations. This was the first indication to Niantic’s product and engineering teams that they had something truly special on their hands ...

> Pokémon GO is a mobile application that uses many services across Google Cloud, but Cloud Datastore became a direct proxy for the game’s overall popularity given its role as the game’s primary database for capturing the Pokémon game world. The graph opening this blog post tells the story: the teams targeted 1X player traffic, with a worst-case estimate of roughly 5X this target. Pokémon GO’s popularity quickly surged player traffic to 50X the initial target, ten times the worst-case estimate. In response, Google CRE seamlessly provisioned extra capacity on behalf of Niantic to stay well ahead of their record-setting growth ..." (http://www.dataarchitect.cloud/bringing-pokemon-go-to-life-on-google-cloud/)

In other words, Pokemon Go owes much of its success to its upfront architectural decision to build a scalable serverless system.  Had they not done so, the story would have ended up very different.

## The Serverless Approach: Benefits vs Risks

<p align="center">
    <img src="https://github.com/worldviewer/scaling/blob/master/img/serverless.png" />
</p>

https://specify.io/concepts/serverless-architecture

> **"Purpose 1: Lower Operations Costs**

> The services in a microservice architecture are always running. In fact, often there is more than one instance running of each service in order to achieve height availability. In a serverless architecture nothing runs when no event occurs. The platform which hosts the functions takes care to execute them only when necessary. And regarding to the pay-as-you-go principle you don’t have to pay if nothing is executed.

> Scaling and autoscaling is no problem in a serverless architecture. When workload increases the affected functions just gets executed more often (in parallel of course).

> Elasticity configuration is also much more effective in a serverless architecture. Instead of saying:

> 'I’m willing to pay for 3 GB memory, and then stop the autoscaling'

> you now can say

> 'I’m willing to pay for 30000 events of type X and 5000 events of type Y, then stop scaling'

> It’s obvious that serverless computing is very effective regarding the resource usage when operating such a solution.

> **Purpose 2: Reduce Time to Market**

> Another motivation to build serverless architectures is a faster development and deployment cycle. This is achieved because of the following reasons:

> The serverless architecture style motivates to use existing or third party services (e.g. Auth0 for authentication) to reduce the custom code [see, 01].
Each function should cover a clear defined responsibility. This leads to very small code base for each function. Because the responsibilities are well defined, each function can be developed and deployed independently.

> Because functions are small and have a clear responsibility the likelihood for reusing existing functions increases.

> The platforms to operate serverless architectures takes care of all the operating stuff. The developers just have to upload the code and get direct feedback how the code behave in production.

> **Risk 1: Vendor Lock-In**

> At the moment, platforms to host serverless architectures are mainly provided by big players. AWS Lambda to name the top dog. To run a system on AWS lambda you must use very AWS specific services (Amazon API Gateway, DynamoDB, S3, etc.). Once you developed a complex system on top of these services you are cursed to stick with AWS, no matter how often they decide to increase their prices.

> Running your own platform to operate a serverless architecture is pointless because, in most cases, the overhead will be much higher then the savings.

> **Risk 2: Complexity and Low Cohesion**

> For decades, software engineers strove for height cohesion and less complexity. Domain driven design and microservices are a perfect match because they summarize the experience how to group related functionality.

> There is the risk that developer ignore those lessons when building serverless architectures and that they end up in a set of unmaintainable functions. In my opinion, serverless architecture tend to produce 'nanoservices' and requires more experienced engineers to get it right. See arnon.me for a defintion of nanoservices:

> 'Nanoservice is an antipattern where a service is too fine-grained. A nanoservice is a service whose overhead (communications, maintenance, and so on) outweighs its utility'"