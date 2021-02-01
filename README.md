Release Rails - coding challenge
=========

This challenge will test your ability to consume and api and produce an api per the following instructions.  There is no UI component. The challenge consists of three parts:

* Consume the API, setup consuming the api every 5 seconds, and store the info in the database.
* Create Endpoints to create accounts, users, ability to assign users to accounts, and ability to assign "nodes" to users to monitor.
* Create Endpoint to query for "nodes" by user, and with params for thresholds, types of nodes, and includes pagination.

When you have finished, we will join you on a call to walk through your design and test it.  At that point we will do a `rake db:reset db:migrate` and restart the background job that gathers data on the 'nodes'.  We will then walk through creating users, accounts, assigning nodes to users and run the api queries below.

## Consume API and store the info in the database

First, you will want to use your favorite HTTP library or gem to consome the following api.

URL: [https://api.releaseapp.io/interview_api/nodes](https://api.releaseapp.io/interview_api/nodes)

HEADERS: `'X-Auth-Token: int_api_yourtoken' -H  'X-Email: youremail@example.com'`

**CURL EXAMPLE**

`curl -H 'X-Auth-Token: int_api_yourtoken' -H  'X-Email: youremail@example.com' https://api.releaseapp.io/interview_api/nodes`

Once you have your code consuming this API, you will want to set it up to hit the endpoint every **5 seconds** and store the data into the database. This process should run in the background however you see fit.  The data is a list of 20 'nodes' and attributes for each node.  You will want to structure a node centric object model, where each consecutive call to the api keeps a history of the attributes as they change.  Later on we will be querying against that history.

## Create Users, Accounts, assign Roles and create associations between Users, Accounts and Nodes

* Create API endpoint to create **Users** with at least the attributes : `name, email`
* Create API endpoint to create **Accounts** with at least the attributes : `name`
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

For the final part of the challenge we want to create an endpoint to query nodes based on the following paramaters.  

_**Important : Only users with the role `admin` may view nodes of type `large`!**_ 

### Request Params

**Required Params:**

* `:user` - return all the nodes for the particular user, regardless of account.  Users have access to nodes through multiple accounts.

**Optional Params:**

* `:status` - is an enum with three values `green`, `yellow`, `red`
  * `green`  < 5% `errored_pods` && < 5% `restarted_pods`
  * `yellow`  >= 5% && < 10% `errored_pods` || >= 5% && < 10% `restarted_pods`
  * `red`  >= 10% `errored_pods` || >= 10% `restarted_pods`
* `:type` - is an enum with three values `small`, `medium`, `large`.  These match the data from the api.
* `:page` - pagination page
* `:per` - number of nodes per page
 
### Response
TODO

## Conclustion
Once you are done make a pull request against the original repository.  We will setup a time to walk through the challenge assignment with you.  We will want to make calls against your nodes query api, examples of calls we will want to see:
* GET `/users/:user_id/your_endpoint` ## user_id Chrissy's id returns all the nodes and statuses as she's an admin
* GET `/users/:user_id/your_endpoint?page=1&per=5` ## returns first 5 nodes and statuses for the particular user, regardless of account
* GET `/users/:user_id/your_endpoint` ## user_id Jack's id does not return any `:large` nodes

We will also want to query with the `:status` param.  As an example:
* GET `/users/:user_id/your_endpoint?status=yellow` ## user_id Chrissy's id returns all nodes with statuses satisfying the yellow threshold above 
* GET `/users/:user_id/your_endpoint?status=yellow` ## user_id Jack's id returns all nodes with statuses satisfying the yellow threshold above, except large nodes 
