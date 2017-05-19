# The Scaling Problem

The following advice on scaling comes from a brilliant Airbnb engineer (and former world-class poker player), Haseeb Qureshi.  A lot of this knowledge is born of hard knocks experiences which you do not necessarily want to personally experience.  And although some people might call these "problems you want to have", IMO they can be fairly interpreted as a lesson to keep an eye out for something better.

## First, Some Basic Concepts and History of Web Architecture

### The Monolithic MVC Architecture

M = Model
V = View
C = Controller

A model is a specification for retrieving data from a source like a database.  A view is a webpage -- sometimes called a template -- which combines static and dynamic content.  Static content is of course the content which never changes, whereas dynamic content is the reason we need a model.  It's the stuff on a page that changes from one user or product to the next.

A controller is the logic which handles interactions with the client (the user's computer), and which acts as the glue that runs the whole show.  In the traditional MVC arcitecture (as you'd see with something like PHP), this controller exists on a server -- the "website".  It transforms incoming requests for webpages into outgoing pages by determining what data is needed, fetching it through the model layer, and then rendering the view with that data back to the client.

A diagram is helpful here:



### Infrastructure as a Service



### Platform as a Service

### Content Deliver Network (CDN)

The Most Important Thing to Know:
If it's Slow --> What is Slow? --> Work Backwards to a Solution

