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
- Recent innovation (AWS Lambda launched in 2014), awareness still fairly low
- Especially suited to deploying limited-scope "microservices" that can rapidly "spin up" from sleep, solve just one small problem, then just as quickly spin back down
- The key innovations here are infinite scalability and a system of billing which enormously benefits lean startups due to super-low startup costs (without any real support from Amazon, the first million hits to your AWS Lambda endpoints are free)
- Best if used in conjunction with the Serverless framework, which supports a simple Node-based wrapper and simplified deployment tools

### The Content Deliver Network (CDN)

The Most Important Thing to Know:
If it's Slow --> What is Slow? --> Work Backwards to a Solution

