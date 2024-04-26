# Core RPCs Through the Command Line

Blockchains produce huge amounts of information related to things like transaction volume and asset ownership, much of which is useful to dApps in the rest of the ecosystem. Accessing and putting that data to work, however, can require a huge amount of technical know-how.&#x20;

## What are Remote Procedure Calls?

Remote Procedure Calls (RPCs) are one way of solving this problem. RPCs are software that facilitate communication between a blockchain network and users, companies, or applications. Rather than needing to run a full custom node or set up a complex program to pull data, the relevant information can be gathered by simply hitting the endpoint provided by the RPC.&#x20;

If it helps, you can think of an RPC as being essentially the opposite of an oracle. The [purpose of an oracle](https://cointelegraph.com/learn/what-is-a-blockchain-oracle-and-how-does-it-work) is to take _external_ data (such as the results of an election or a weather forecast) and move it _into_ a blockchain, while the purpose of an RPC is to take _internal_ data (such as information related to transactions and blocks) and move it _out of_ a blockchain.&#x20;

## Which RPC Providers can I Use?

The Core Chain supports [five RPC providers](https://chainlist.org/chain/1116) -- the native Core RPC, and RPCs provided through Ankr, InfStones, IceCreamSwap, and 1RPC. Here are the relevant URLs, to make for easy copying:

* https://rpc.coredao.org&#x20;
* https://core.public.infstones.com&#x20;
* https://rpc.ankr.com/core&#x20;
* https://1rpc.io/core&#x20;
* https://rpc-core.icecreamswap.com

Below, you'll find a discussion of how to utilize these RPCs with the command line interface (CLI). The next page covers how to [do this with Postman](https://docs.coredao.org/developer/interacting-with-core/core-rpcs-through-postman).

### Working with RPCs Through the Command Line

We should start by first understanding what CURL is and [how it works](https://developer.ibm.com/articles/what-is-curl-command/).

"CURL" can also be written "cURL", and it's short for "client URL". In essence, it's a CLI tool that offers developers a way of fetching data directly from a remote server.&#x20;

The simplest CURL command consists of fetching a webpage's HTML code by passing in a URL, and you can try it now by running this in your CLI of choice:

`curl https://coredao.org/`

As is often the case there are thousands of little subtleties involved when using CURL for interacting with RPC nodes, but this basic idea never changes.

Here's a more advanced command, which we can use to get information related to a specific transaction on the Core Chain by passing in the transactions hash and a few other parameters. Don't be intimidated by the added complexity, we're going to walk you through what all the relevant parts mean.

```
curl -H "Content-Type: application/json" -X POST 
     --data '{"jsonrpc":"2.0","method":"eth_getTransactionByHash",
            "params":["0xc9c4a5d14857ace0db197c7393806868824763377f802645aacf6f38d9c309b7"],
            "id":1}'
     --url 'https://rpc.ankr.com/core'
```

(NOTE! The code above won't run correctly in terminal because it's been broken up to make it more readable. To copy and paste directly into the CLI, use this instead:

```
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionByHash","params":["0xc9c4a5d14857ace0db197c7393806868824763377f802645aacf6f38d9c309b7"],"id":1}' --url 'https://rpc.ankr.com/core' 
```

)

So what's going on here? CURL is a method for sending API requests, which contain an endpoint (i.e. the URL from which the information is being retrieved) and an HTTP method. In our case, the URL comes at the end after the `--url` option; you can swap in any of the valid RPC URLs found [here](https://chainlist.org/chain/1116), just don't forget to wrap the URL in single quotes ('').

<figure><img src="../.gitbook/assets/Screenshot 2023-12-20 at 12.26.37 PM (1).png" alt=""><figcaption><p>Any of the URLs in the red box will work.</p></figcaption></figure>

The HTTP method comes after the `- X`, and in this example we're issuing a `POST` request: `-X POST`. The `-H "Content-Type: application/json"` part which comes immediately before is known as a _header_, and it allows us to specify metadata about the request. In our example we're specifying what type of content we want to see, but the command will run fine without it. We've included mostly because we don't want you to be confused if you see these kinds of headers in other examples, and because there are situations in which the command will throw an error if a header isn't included.&#x20;

That covers the header (`-H`) and the HTTP method (`-X`), and the next big topic is the data we're feeding to the CURL command via the `--data` option. The two most important components to understand are the `"method"` and the `"params"` .

There are a huge number of [RPC commands](https://ethereum.org/en/developers/docs/apis/json-rpc/) you can use for grabbing blockchain data, and they tend to have a fairly intuitive naming scheme. The `"eth_getTransactionByHash"` method let's you get a transaction by its hash, for example, while the `"eth_getBalance"` method will tell you the balance of a given address.&#x20;

How does the method know which transaction hash to look at or which address's balance to check? You tell it with the `"params"` field. In our example above we asked for information pertaining to a specific transaction by passing in the transactions hash:

```
"params":["0xc9c4a5d14857ace0db197c7393806868824763377f802645aacf6f38d9c309b7"]
```

Pay attention to the fact that the transaction hash is in double quotes  ("") and that the whole parameters field is wrapped in a list (\[]). Omitting either will leave you with errors.&#x20;

What parameters are required for a given method will depend on the method in question, but you can find that information in [Ethereum's JSON-RPC documentation](https://ethereum.org/en/developers/docs/apis/json-rpc/).&#x20;

Finally, we'll briefly touch on the `"jsonrpc"` field and the `"id"` field.  The first is just the specific version of JSON-RPC you're using. As with most software projects there are different types of JSON-RPC, and you have to specify which type you're using in your CURL command (we're using `"2.0"` throughout).

The `"id"` field allows you to match a given request with a given response, i.e. you'd assign the first request a "1", the second request a "2", etc. This isn't really an issue for us because we're only sending the one request, but if you were constructing a more complicated analytical pipeline you'd want to have a way of knowing that response "x" goes with request "x", and that's what the `"id"` field offers you.&#x20;

Now, for the sake of completeness let's walk through everything one more time, in the order in which it's included in the command:

* `curl`: since we're using CURL, we have to prefix it with the name of the command.
* `-H`: this is a header containing information about the request's content etc.
* `-X`: this is where we specify what kind of HTTP command we're using, which in our case is `GET`.
* `--data`: this is where we pass the method, its parameters, the id, etc. to the request.&#x20;
* `--url`: this is the URL we want the request to be pointed at.

And, in case you're curious, here's what the results returned by our CURL command look like:

```
{"jsonrpc":"2.0",
"id":1,
"result":
    {"blockHash":"0xb1cbf38843207e6133b69b53559c22c61463208cc2a822a92ba18e30da3054ba",
     "blockNumber":"0x972743",
     "from":"0x7ef3a94ad1c443481fb3d86829355ca90477f8b5",
     "gas":"0x7a120",
     "gasPrice":"0x0",
     "hash":"0xc9c4a5d14857ace0db197c7393806868824763377f802645aacf6f38d9c309b7",
     "input":"0xf340fa010000000000000000000000007ef3a94ad1c443481fb3d86829355ca90477f8b5",
     "nonce":"0x74622",
     "to":"0x0000000000000000000000000000000000001000",
     "transactionIndex":"0x4",
     "value":"0x2d01fcfa9da000",
     "type":"0x0",
     "v":"0x8dc",
     "r":"0x15c80da30b54e61f383f38f2033f71ef4201a39ff4e799dadf13937dde88b1a0",
     "s":"0xd63214598ae5cd8da665517e60d8471f1b4c591711247d0f94958ec0add4ba9"
     }
   }%
```

We're not going to go through what all these fields because it's not our focus, but if you don't get an output that looks a lot like this, there's an error somewhere.&#x20;

#### Incorporating CURL into a Python Workflow

As much fun as it would be to spend all day in Terminal, chances are you'll want to move into an IDE to build anything more complicated.&#x20;

In Python, this whole command can be duplicated using the `requests` library. We'll have to import `requests`, then create `header` and `data` dictionaries, then pass those into `requests.post`. Here's what that looks like:

````python
```python
import requests

headers = {
    'Content-Type': 'application/x-www-form-urlencoded',
}

data = '{"jsonrpc":"2.0","method":"eth_getTransactionByHash","params":["0xc9c4a5d14857ace0db197c7393806868824763377f802645aacf6f38d9c309b7"],"id":1}'

response = requests.post('https://rpc.ankr.com/core', headers=headers, data=data)
print(response.content)
```
````

If you wanted to use one of the other RPC URLs, of course, you'd swap it in for `'https://rpc.ankr.com/core'`.

