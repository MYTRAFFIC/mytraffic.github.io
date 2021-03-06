---
title: "Why did we reorganize our tech & product team in 2021?"
author: sdiemer
categories: [ organization, tech, product ]
layout: post
image: assets/images/t&p-org.jpg
description: "How our tech and product team works to build the best SaaS platform for our customers."
---

At the beginning of 2021 we completely rethought our Tech and Product organization.
This article describes our motivations and the reasoning behind our current
organization.


## A long time ago… 2.5 years back in the past

The development of [mytraffic's SaaS platform](https://app.mytraffic.io) was
originally started in may 2019 and we released it in production in september
2019 after **4 months** of intense work. We began signing new contracts and
customers started using our SaaS to gain insights on pedestrian flows around or
inside their physical places to take better business decisions. This new
platform really helped mytraffic's growth :rocket:, turning what was a service
company into a SaaS company. It also enabled us to reach our **1st million
annual recurring revenue** (ARR) by the end of 2019 :money_mouth_face:.  Yet,
after this successful initial launch, even if we still managed to ship a lot of
new features in 2020, we also suffered a couple of failures as months went by:
- we released some features that did not have the impact we expected
- the usage on the platform did not meet our objectives
- we had important delays on new insightful data we wanted to ship to customers
  (e.g. european pedestrian flows)

This was time for us to question the way we were building the platform and to
take actions to deeply improve our understanding of our customers.  With the
help of [Pierre Fournier](https://www.linkedin.com/in/fournierpierre/), who
performed an audit of our product processes, we identified the following issues
in our organization:
- **cruel lack of user discovery**:
    - the tech & product team only met our customers in rare occasions
    - we tried to find **solutions** instead of focusing first on discovering
      customer **needs**
- **lack of strategy and focus**: we tried to ship too much, too fast given the
  available team and the succession of features did not result from a clear
  company strategy
- **output driven instead of outcome driven**: we were happy celebrating the
  release of a new dashboard or a new pricing system (outputs) instead of
  considering the real outcome on the key company metrics and our customers
- **lack of ownership**: our tech team was working in silos of distinct
  competencies and the work on a new feature flowed from one team to the other
  in a beautiful waterfall process without a clear owner. There were also many
  features in parallel in each team. In the end, this resulted in a lot of back
  and forth internally around data quality. We had to rework algorithms several
  times before going to production and we were late in the delivery compared to
  what was first announced to business teams and customers.

![waterfall](/assets/images/waterfall.jpg)

At that time, our technical team was organized around component teams:
- one team of **data scientists** exploring the data and designing algorithms,
- one team of **data software engineers** automating our daily pipelines and
  scaling the data science algorithms in production,
- one team of **fullstack software engineers** using the data produced by the
  data pipeline to create data vizualization for our customers.

These engineers were working in collaboration with one and a half (!!) product
manager essentially focused on specs and the delivery process, and one
part-time designer providing the web app mockups. We were lacking time and
people to properly dig into the product discovery and user research activities.

This organization worked well back in the time when we were 5 or 6 in the team,
but it began to show its limits when we grew to 15. We hence decided to change
it to create more focus, to be closer to our customers and regain ownership.

## mytraffic tech & product organization, 2021 edition

*Inspired* by Marty Cagan, we decided to try shifting from a *components teams*
organization to a *[product teams](https://svpg.com/product-vs-feature-teams/)*
organization. In Cagan's terminology, product teams are teams composed of
different disciplines *empowered to solve problems in ways customers love, yet
work for business*.

### Building a true product centric organization

To do so we first needed to drastically strengthen :muscle: our product
competencies by recruiting external talents. Laura joined mytraffic as Chief
Product Officer, Pauline and Olivier as product managers, and Brice decided to
focus on a platform product manager role after two years of prolific
contributions as a lead software engineer. This scaled the number of product
managers from one and a half to five in a few months!! :100: This gave us more
opportunities than ever to listen to our customers and to perform product
discovery.


### Defining our Jobs To Be Done

Then we needed to define the number of product teams we needed and what would
be the prioritary scopes that we wanted them to embrace. **We began changing
the way we think** about our SaaS platform and our customers. What we initially
built was a very generic way to present our data, and every customer would see
it on the platform in exactly the same way. This made sense when the team was
small, in order to scale in parallel on different business verticals without
having to focus on only one or two. But in 2021, we understood that if we
wanted to **increase usage** on our platform we had to offer some customization
and to show the same data differently depending on the customer.  We revisited
the concept of [Job To Be
Done](https://hbr.org/2016/09/know-your-customers-jobs-to-be-done) (JTBD) and
decided to rethink our SaaS platform around it. The reasoning is in the end
quite simple: each and every mytraffic's customer comes to our platform because
she needs actionable data that will help her perform her day to day job.
:arrow_right: We should then design our product so that it enables doing this
job as easily as possible.

To make things more tangible, here are two Jobs To Be Done that we are
currently (end of 2021) working on, and their value proposition:
- **monitoring assets performance** (*perform*)  
  Build meaningful analytics to follow your assets performance over time,
  compare assets in your network and with your competitors, make data-driven
  decisions. Report positive and negative performance and dig into data.
- **developing a network of assets** (*develop*)  
  Find the best location for your next store.  Leverage your deep understanding
  of the market with our powerful engine to build your expansion strategy.
  Easily create your business plan to convince your stakeholders.


### The final org

We then decided to split our teams into two groups:

- **user teams** that are each focused on one JTBD module on our web platform.
  These teams build the best user experience possible for our customers coming
  to [mytraffic.io](https://app.mytraffic.io), providing them with the data and
  insights they need to complete important tasks in their day to day job. We
  currently have three of them:
    - one for *perform*
    - one for *develop*,
    - and we plan to create more as we start working on more new modules!
- **platform teams** that are focused on providing data, algorithms and R&D
  supporting one or more JTBD. We currently have three of them:
    - one for our *pedestrian flow* algorithm which aims at computing the
      number of pedestrians passing by a given address on a given week
    - one for our *vehicle flow* algorithm which aims at computing the number
      of… you guessed it… vehicles! passing by a given address on a given week
    - one team focused on data quality & monitoring

{% include question.html content="Why make this distinction between user and
platform teams you may ask? Aren't you back to the initial situation with
component teams, waterfall, and so on?" %}

Very good question! In fact we realized that it did not make sense to
try to smash together our data generation and data presentation activities in
fully cross-functional (from R&D data scientists to web designers) teams.

Designing and improving algorithms takes time… While you can have a lot done
while working on a web interface for one or two weeks, you probably need one or
two months to get anything meaningful while working on an algoritm to infer
real pedestrian flows from sparse GPS data. We think it makes sense to have
separate kind of teams with different velocities (R&D vs traditional software
development).

However, data software engineers may work on a user team to transform (e.g.
aggregate) already available data and make it useable in our product with good
performance. Moreover, data software engineers and data scientists are now
working close together to make sure that we can iterate quickly on new ideas
and reduce the time between our first data explorations and the final algorithm
in production. This new organization is thus very different from the component
teams we had when we started.

Here is a small diagram to help you visualize the way we are now organized:

![diagram of our 2021 organization](/assets/images/team_diagram.jpg)

## What's next?

Things are changing fast at mytraffic, and we never know what future will bring
us! For now we are confident that this new organization will enable us to
release functionalities that our current and future customers will love. We
will keep monitoring their usage as they are rolled out in production and
adjust as needed. In the meantime we will try to scale the team from 15 in 2021
to 25 in 2022 and create new user teams and platform teams to tackle more
topics in parallel and support our growth :rocket:. If you want to be part of
it don't hesitate to visit our [open
positions](https://www.welcometothejungle.com/fr/companies/mytraffic/jobs).
Stay tuned to see how it turns out!
