# Core RPCs Through Postman

### What is Postman

[Postman](https://www.postman.com/) is a popular software offering that makes it easy to create and use APIs.&#x20;

In this document, we'll cover the very basics of hitting an RPC endpoint with Postman. Is it not intended to be (or to replace) a full, end-to-end tutorial, but rather to offer an introduction to the subject.&#x20;

### Working with RPCs Through Postman

First, you'll need to sign up with an account. Postman makes this very easy to do by manually entering your account details or with a gmail address.&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2023-12-21 at 1.13.39 PM (1).png" alt=""><figcaption></figcaption></figure>

Projects in Postman are organized into "workspaces", so you'll need to make one to test this process.

<figure><img src="../.gitbook/assets/Screenshot 2023-12-21 at 1.19.30 PM.png" alt=""><figcaption></figcaption></figure>

You can choose from a number of different workspaces. For this example, we're going to use `HTTP`.

<figure><img src="../.gitbook/assets/Screenshot 2023-12-21 at 1.20.28 PM.png" alt=""><figcaption></figcaption></figure>

This will take you to a basic workstation, where you can structure your RPC query and get a response back:

<figure><img src="../.gitbook/assets/Screenshot 2023-12-21 at 1.24.53 PM.png" alt=""><figcaption></figcaption></figure>

First, we'll change the `GET` request to a `POST` request, as that's how the query was structured in the previous section. Then, we'll add the RPC URL we want to use to get data related to a specific Core Chain transaction:

<figure><img src="../.gitbook/assets/Screenshot 2023-12-21 at 1.27.35 PM.png" alt=""><figcaption></figcaption></figure>

Remember that you can use any of the valid [Core RPC URLs](https://chainlist.org/chain/1116).

Then, we'll head over to the `Body` tab and add the data from our prior CURL request (note that we're submitting the request as `raw` `json`):

![](<../.gitbook/assets/Screenshot 2023-12-21 at 1.33.49 PM.png>)&#x20;

Here's the same data in a format that's easy to copy and paste:

````
```postman_json
{"jsonrpc":"2.0","method":"eth_getTransactionByHash", "params": ["0xc9c4a5d14857ace0db197c7393806868824763377f802645aacf6f38d9c309b7"], "id":1}
```
````

Once we hit `Send`, we'll get back a bunch of beautiful JSON with all the information we wanted about this transaction:

<figure><img src="../.gitbook/assets/Screenshot 2023-12-21 at 1.35.09 PM.png" alt=""><figcaption></figcaption></figure>
