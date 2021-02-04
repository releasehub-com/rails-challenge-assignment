Release Rails - coding challenge
=========

This challenge will test your ability to consume an API we've set up for you and then produce an API that will meet certain criteria we expect.  There is no UI component. The challenge consists of three tasks:

1) Consume an API from Release, setup calling the api every 5 seconds, and store the info in the database.
2) Create Endpoints to create accounts, users, ability to assign users to accounts, and ability to assign "nodes" to users to monitor.
3) Create an Endpoint to query for "nodes" by user, and with params for thresholds, types of nodes, and includes pagination.

We encourage you to write tests you feel necessary for models and requests.

## Task 1
### Consume API and store the info in the database

First, you will want to use your favorite HTTP library or gem to consome the following api.

URL: [https://api.releaseapp.io/interview_api/nodes](https://api.releaseapp.io/interview_api/nodes)

HEADERS: `'X-Auth-Token: int_api_yourtoken' -H  'X-Email: youremail@example.com'`

**CURL EXAMPLE**

`curl -H 'X-Auth-Token: int_api_yourtoken' -H  'X-Email: youremail@example.com' https://api.releaseapp.io/interview_api/nodes`

** RESPONSE BODY EXAMPLE**
```ruby
[{"id"=>"node_0",
  "name"=>"Mr Gravity",
  "total_pods"=>103,
  "errored_pods"=>3,
  "healthy_pods"=>97,
  "restarting_pods"=>3,
  "type"=>"large"},
  ...
]
```

Once you have your code consuming this API, you will want to set it up to hit the endpoint every **5 seconds** and store the data into the database. This process should run in the background however you see fit. The data is a list of 20 'nodes' and attributes for each node.  You will want to structure a node centric object model, where each consecutive call to the api keeps a history of the attributes as they change.  

Your object needs to have a `current_status` method that is the worst status that node has reached.  For example, if a node reached a yellow status (defined below), but later (further ahead in time) in its history, it is `green`, that nodes current status would still be `yellow`.

Statuses:
  * `green`  < 5% `errored_pods` && < 5% `restarted_pods`
  * `yellow`  >= 5% && < 10% `errored_pods` || >= 5% && < 10% `restarting_pods`
  * `red`  >= 10% `errored_pods` || >= 10% `restarting_pods`

## Task 2
### Create Users, Accounts, assign Roles and create associations between Users, Accounts and Nodes
Create API endpoints to do the following tasks

* Create **Users** 
* Create **Accounts** 
* Associate **Users** with **Accounts**
* Assign **Roles** by name to users.  The role names will be : `admin, developer`
* Assign a **Node** to an **Account**

_**All Users, Accounts, Roles and the assigning of Nodes to Accounts should be done by hitting your API endpoints**_
Do not use `db/seeds.rb` or a similar method to set up this data. We want to see all the data inserted through API calls. We suggest creating a reproducible way to hit your endpoints, such as Rake tasks or scripts in `bin/`.

#### Create 3 users:

```
[
  {name: 'Chrissy Snow', email: 'csnow@example.com'},
  {name: 'Jack Tripper', email: 'jtripper@example.com'},
  {name: 'Janet Wood', email: 'jwood@example.com'}
]
```

#### Create 2 accounts:

```
[
  {name: 'Acme Developers'},
  {name: 'Acme Devops}
]
```

#### User and Account Roles Setup

* Assign **Chrissy Snow** to both accounts
* Assign **Jack Tripper** to **Acme Devops**
* Assign **Janet Wood** to **Acme Developers**
* Assign the role of `admin` to **Chrissy**, and `developer` to **Janet** and **Jack**

#### Assign Nodes to Accounts

* Assign all nodes to the **Acme Devops** account
* Assign only the `small` nodes to the **Acme Developers** Account

## Task 3:
### Create Node Monitoring Endpoints

### Nodes Endpoint Url Format
`GET /users/:user_id/your_endpoint`

For the final part of the challenge we want to create an endpoint to query nodes based on the following parameters.  

_**Important : Only users with the role `admin` may view nodes of type `large`!**_ 

### Request Params
**Optional Params:**

* `:status` - is an enum with three values `green`, `yellow`, `red`
* `:type` - is an enum with three values `small`, `medium`, `large`.  These match the data from the api.
* `:page` - pagination page
* `:per` - number of nodes per page
 
### Example Response
REQUEST : `GET /users/:chrissys_user_id/your_endpoint?page=1&per=3&status=green&size=small`

RESPONSE
```ruby
[
  {
    "node_id"=>"node_5",
    "name"=>"Klaw",
    "account"=>"Acme Devops",
    "current_status"=>"green",
    "statuses"=> [
      {
        "status"=>"green",
        "size"=>"small",
        "total_pods"=>101.0,
        "percentage_errored_100"=>2.9702970297029703,
        "percentage_restarting_100"=>0.0,
        "created_at"=>"2021-02-03T04:05:42.176Z"
      },
      {
        "status"=>"green",
        "size"=>"small",
        "total_pods"=>112.0,
        "percentage_errored_100"=>0.8928571428571428,
        "percentage_restarting_100"=>0.8928571428571428,
        "created_at"=>"2021-02-03T04:05:42.911Z"
      },
      {
        "status"=>"green",
        "size"=>"small",
        "total_pods"=>113.0,
        "percentage_errored_100"=>2.6548672566371683,
        "percentage_restarting_100"=>2.6548672566371683,
        "created_at"=>"2021-02-03T04:05:43.543Z"},
      {
        "status"=>"green",
        "size"=>"small",
        "total_pods"=>102.0,
        "percentage_errored_100"=>0.9803921568627451,
        "percentage_restarting_100"=>0.0,
        "created_at"=>"2021-02-03T04:05:44.120Z"
      }
    ]
  },
  ...
]
```
### Api Functionality
* All calls to your endpoint will be scoped through a users like `GET /users/:user_id/your_endpoint`
* Always return nodes from all accounts for the user
* Only users with role `admin` can view `large` nodes
* A query for nodes with a `yellow` status, should bring back all nodes with `current_status` of `yellow` and for each node all of its statuses.  Same for any other value for `current_status`
* The params `:type` and `:status` can be combined and will be implemented as an **AND**. 
* Pagination should respect all other params. 

### Examples
* GET `/users/:user_id/your_endpoint` ## user_id Chrissy's id returns all the nodes and statuses as she's an admin
* GET `/users/:user_id/your_endpoint?page=1&per=5` ## returns first 5 nodes and statuses for the particular user, regardless of account
* GET `/users/:user_id/your_endpoint` ## user_id Jack's id does not return any `:large` nodes

We will also want to query with the `:status` param.  As an example:
* GET `/users/:user_id/your_endpoint?status=yellow` ## user_id Chrissy's id returns all nodes with statuses satisfying the yellow threshold above 
* GET `/users/:user_id/your_endpoint?status=yellow` ## user_id Jack's id returns all nodes with statuses satisfying the yellow threshold above, except large nodes 

## Conclusion
Once you are done make a pull request against the original repository.  We will schedule a time to walk through your design and test it.  At that point we will:
* walk through your implemenation and tests
* run `rake db:reset db:migrate`
* start the background job that gathers data on the nodes  
* run your rake task/s to load up all the data and associations needed to query your nodes endpoint
* run queries against the endpoint

