# The Scaling Problem

The following advice on scaling comes from a brilliant Airbnb engineer (and former world-class poker player), Haseeb Qureshi.  A lot of this knowledge is born of hard knocks experiences which you do not necessarily want to personally experience.  And although some people might call these "problems you want to have", IMO they can be fairly interpreted as a lesson to keep an eye out for something better.

People will of course differ on the best way to solve this complex problem, and CEO's and CTO's would be wise to hear out all opinions.  The important part is that anybody who is trying to create a technology startup should understand at a high level the reasoning at any particular moment that justifies their _why_ and _how_ for addressing the scaling problem.  It is arguably one of the most fundamental issues which any startup that does not have access to incredible funding will face -- and adding to the danger is the fact that _it is also one of the first decisions which must be made._

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

### CPU / Memory Usage Really High

Your site is slow again.  This time, you can tell that your server is pegged.  What do you do?

You're starting to enter dangerous territory at this point known as "operations" ("devops").  The risk you face is that the complexity of your system is about to drastically increase.  You should do everything you can to avoid that: Put simply, you should buy a better server.  Don't scale horizontally until you absolutely have to.

<p align="center">
        <img src="https://github.com/worldviewer/scaling/blob/master/img/horizontal-vs-vertical-scaling.jpg" />
</p>

Horizontal scaling is complex and expensive.  To make it work, all of your application servers must be _stateless_.  In other words, every single request is like a blank slate, because there's no real guarantee that the same machine handled the last client interaction.  All of your state is going to have to exist in either your database or your cache.

Making matters worse, you're now going to need load balancers to evenly distribute the incoming requests.  All of this added complexity creates more failure points.  Things are going to start breaking.

<p align="center">
        <img src="https://github.com/worldviewer/scaling/blob/master/img/load-balancing.jpg" />
</p>

> "Your centralized load balancer secretly hates you. You just haven't realized it yet ...

> Lets rip off the band aid: It might work well enough for brochureware, but for web-based applications, centralized load balancing is just a dumb idea. It started out as a hack to make a bunch of servers look like one server; a workaround for the fact that HTTP and the web wasn't designed for modern applications. It's an unnecessary middleman and a bottleneck. It forces you toward certain [structures] that are not suitable for the modern web. It doesn't like you, and it doesn't like your friends." (http://abnorman.com/rantings/2014/7/28/unbalanced)

