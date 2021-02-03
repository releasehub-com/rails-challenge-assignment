Release Rails - coding challenge
=========

This challenge will test your ability to consume and api and produce an api per the following instructions.  There is no UI component. The challenge consists of three parts:

* Consume the API, setup consuming the api every 5 seconds, and store the info in the database.
* Create Endpoints to create accounts, users, ability to assign users to accounts, and ability to assign "nodes" to users to monitor.
* Create Rake tasks to load the accounts, users and assign roles and nodes.
* Create Endpoint to query for "nodes" by user, and with params for thresholds, types of nodes, and includes pagination.
* Write tests you feel necessary for modesl and requests.

## Consume API and store the info in the database

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
 {"id"=>"node_2",
  "name"=>"Captain Toxin the Hunter",
  "total_pods"=>111,
  "errored_pods"=>0,
  "healthy_pods"=>109,
  "restarting_pods"=>2,
  "type"=>"large"},
 {"id"=>"node_3",
  "name"=>"The Tiger Shark",
  "total_pods"=>118,
  "errored_pods"=>4,
  "healthy_pods"=>113,
  "restarting_pods"=>1,
  "type"=>"medium"},
 {"id"=>"node_5",
  "name"=>"Klaw",
  "total_pods"=>119,
  "errored_pods"=>0,
  "healthy_pods"=>117,
  "restarting_pods"=>2,
  "type"=>"small"},
 {"id"=>"node_6",
  "name"=>"Beetle",
  "total_pods"=>111,
  "errored_pods"=>3,
  "healthy_pods"=>108,
  "restarting_pods"=>0,
  "type"=>"small"},
 {"id"=>"node_7",
  "name"=>"Dark Mephisto",
  "total_pods"=>109,
  "errored_pods"=>1,
  "healthy_pods"=>105,
  "restarting_pods"=>3,
  "type"=>"large"},
 {"id"=>"node_8",
  "name"=>"Maya Herrera Girl",
  "total_pods"=>108,
  "errored_pods"=>3,
  "healthy_pods"=>102,
  "restarting_pods"=>3,
  "type"=>"small"},
 {"id"=>"node_9",
  "name"=>"Supah Deathlok Brain",
  "total_pods"=>111,
  "errored_pods"=>2,
  "healthy_pods"=>106,
  "restarting_pods"=>3,
  "type"=>"small"},
 {"id"=>"node_10",
  "name"=>"Green Carnage Machine",
  "total_pods"=>118,
  "errored_pods"=>0,
  "healthy_pods"=>117,
  "restarting_pods"=>1,
  "type"=>"small"},
 {"id"=>"node_12",
  "name"=>"Doctor Walrus",
  "total_pods"=>103,
  "errored_pods"=>2,
  "healthy_pods"=>101,
  "restarting_pods"=>0,
  "type"=>"small"},
 {"id"=>"node_13",
  "name"=>"Supah Aqua",
  "total_pods"=>110,
  "errored_pods"=>0,
  "healthy_pods"=>109,
  "restarting_pods"=>1,
  "type"=>"small"},
 {"id"=>"node_15",
  "name"=>"Riddler Ivy",
  "total_pods"=>100,
  "errored_pods"=>2,
  "healthy_pods"=>98,
  "restarting_pods"=>0,
  "type"=>"large"},
 {"id"=>"node_17",
  "name"=>"Ultra Stardust",
  "total_pods"=>108,
  "errored_pods"=>0,
  "healthy_pods"=>108,
  "restarting_pods"=>0,
  "type"=>"small"},
 {"id"=>"node_18",
  "name"=>"Captain Blade",
  "total_pods"=>111,
  "errored_pods"=>1,
  "healthy_pods"=>107,
  "restarting_pods"=>3,
  "type"=>"small"},
 {"id"=>"node_19",
  "name"=>"Supah Cat IX",
  "total_pods"=>104,
  "errored_pods"=>3,
  "healthy_pods"=>99,
  "restarting_pods"=>2,
  "type"=>"small"},
 {"id"=>"node_11",
  "name"=>"Green A-Bomb",
  "total_pods"=>117,
  "errored_pods"=>7,
  "healthy_pods"=>105,
  "restarting_pods"=>5,
  "type"=>"large"},
 {"id"=>"node_16",
  "name"=>"Wiz Kid",
  "total_pods"=>118,
  "errored_pods"=>8,
  "healthy_pods"=>102,
  "restarting_pods"=>8,
  "type"=>"medium"},
 {"id"=>"node_4",
  "name"=>"Magnificent Etrigan",
  "total_pods"=>113,
  "errored_pods"=>8,
  "healthy_pods"=>100,
  "restarting_pods"=>5,
  "type"=>"small"},
 {"id"=>"node_1",
  "name"=>"Warbird",
  "total_pods"=>102,
  "errored_pods"=>13,
  "healthy_pods"=>71,
  "restarting_pods"=>18,
  "type"=>"medium"},
 {"id"=>"node_14",
  "name"=>"Venom",
  "total_pods"=>109,
  "errored_pods"=>14,
  "healthy_pods"=>81,
  "restarting_pods"=>14,
  "type"=>"small"}]
  ```

Once you have your code consuming this API, you will want to set it up to hit the endpoint every **5 seconds** and store the data into the database. This process should run in the background however you see fit.  The data is a list of 20 'nodes' and attributes for each node.  You will want to structure a node centric object model, where each consecutive call to the api keeps a history of the attributes as they change.  

Each node will have a `current_status` that is the worst status that node has reached.  For example, if a node reached a yellow status (defined below), then when you call the api a subsequent time it is `green`, that nodes current status would still be `yellow`.

Later on we will be querying against that history.

## Create Users, Accounts, assign Roles and create associations between Users, Accounts and Nodes

* Create API endpoint to create **Users** 
* Create API endpoint to create **Accounts** 
* Create API endpoing to associate **Users** with **Accounts**
* Create API endpoint to assign **Roles** by name to users for a particular account.  The role names will be : `admin, developer`
* Create API endpoint to assign a **Node** to an **Account**

### Create Rake Task/s To do the following

_**All Users, Accounts, Roles and the assigning of Nodes to Accounts should be done by hitting your API endpoints, not directly into the DB using ActiveRecord.**_

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

## Create Node Monitoring Endpoints

### Nodes Endpoint Url Format
`GET /users/:user_id/your_endpoint`

For the final part of the challenge we want to create an endpoint to query nodes based on the following paramaters.  

_**Important : Only users with the role `admin` may view nodes of type `large`!**_ 

### Request Params
**Optional Params:**

* `:status` - is an enum with three values `green`, `yellow`, `red`
  * `green`  < 5% `errored_pods` && < 5% `restarted_pods`
  * `yellow`  >= 5% && < 10% `errored_pods` || >= 5% && < 10% `restarted_pods`
  * `red`  >= 10% `errored_pods` || >= 10% `restarted_pods`
* `:type` - is an enum with three values `small`, `medium`, `large`.  These match the data from the api.
* `:page` - pagination page
* `:per` - number of nodes per page
 
### Example Response
REQUEST : `GET /users/:user_id/your_endpoint?page=1&per=3&status=green&size=small`

RESPONSE
```ruby
[{"node_id"=>"node_5",
  "name"=>"Klaw",
  "current_status"=>"green",
  "statuses"=>
   [{"status"=>"green",
     "size"=>"small",
     "total_pods"=>101.0,
     "percentage_errored_100"=>2.9702970297029703,
     "percentage_restarting_100"=>0.0,
     "created_at"=>"2021-02-03T04:05:42.176Z"},
    {"status"=>"green",
     "size"=>"small",
     "total_pods"=>112.0,
     "percentage_errored_100"=>0.8928571428571428,
     "percentage_restarting_100"=>0.8928571428571428,
     "created_at"=>"2021-02-03T04:05:42.911Z"},
    {"status"=>"green",
     "size"=>"small",
     "total_pods"=>113.0,
     "percentage_errored_100"=>2.6548672566371683,
     "percentage_restarting_100"=>2.6548672566371683,
     "created_at"=>"2021-02-03T04:05:43.543Z"},
    {"status"=>"green",
     "size"=>"small",
     "total_pods"=>102.0,
     "percentage_errored_100"=>0.9803921568627451,
     "percentage_restarting_100"=>0.0,
     "created_at"=>"2021-02-03T04:05:44.120Z"}]},
 {"node_id"=>"node_6",
  "name"=>"Beetle",
  "current_status"=>"green",
  "statuses"=>
   [{"status"=>"green",
     "size"=>"small",
     "total_pods"=>101.0,
     "percentage_errored_100"=>0.0,
     "percentage_restarting_100"=>0.9900990099009901,
     "created_at"=>"2021-02-03T04:05:42.192Z"},
    {"status"=>"green",
     "size"=>"small",
     "total_pods"=>110.0,
     "percentage_errored_100"=>2.727272727272727,
     "percentage_restarting_100"=>1.8181818181818181,
     "created_at"=>"2021-02-03T04:05:42.914Z"},
    {"status"=>"green",
     "size"=>"small",
     "total_pods"=>100.0,
     "percentage_errored_100"=>0.0,
     "percentage_restarting_100"=>1.0,
     "created_at"=>"2021-02-03T04:05:43.547Z"},
    {"status"=>"green",
     "size"=>"small",
     "total_pods"=>113.0,
     "percentage_errored_100"=>2.6548672566371683,
     "percentage_restarting_100"=>1.7699115044247788,
     "created_at"=>"2021-02-03T04:05:44.123Z"}]},
 {"node_id"=>"node_8",
  "name"=>"Maya Herrera Girl",
  "current_status"=>"green",
  "statuses"=>
   [{"status"=>"green",
     "size"=>"small",
     "total_pods"=>119.0,
     "percentage_errored_100"=>0.8403361344537815,
     "percentage_restarting_100"=>1.680672268907563,
     "created_at"=>"2021-02-03T04:05:42.212Z"},
    {"status"=>"green",
     "size"=>"small",
     "total_pods"=>100.0,
     "percentage_errored_100"=>1.0,
     "percentage_restarting_100"=>0.0,
     "created_at"=>"2021-02-03T04:05:42.921Z"},
    {"status"=>"green",
     "size"=>"small",
     "total_pods"=>103.0,
     "percentage_errored_100"=>2.912621359223301,
     "percentage_restarting_100"=>1.9417475728155338,
     "created_at"=>"2021-02-03T04:05:43.554Z"},
    {"status"=>"green",
     "size"=>"small",
     "total_pods"=>120.0,
     "percentage_errored_100"=>3.3333333333333335,
     "percentage_restarting_100"=>0.0,
     "created_at"=>"2021-02-03T04:05:44.130Z"}]}]
```
### Api Functionality
* All calls to your endpoint will be scoped through a users like `GET /users/:user_id/your_endpoint`
* You should always return nodes from all accounts for the user
* Only users with role `admin` can view `large` nodes
* A query for nodes with a `yellow` status, should bring back all nodes with `current_status` of `yellow` and for each node all of this statuses.  Same for any other value for `current_status`
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

