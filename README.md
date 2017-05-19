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



### Infrastructure as a Service



### The Platform as a Service

### The Content Deliver Network (CDN)

The Most Important Thing to Know:
If it's Slow --> What is Slow? --> Work Backwards to a Solution

