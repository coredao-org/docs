# Common Error Messages

An API call that encounters an error will return 0 as its `status` code, and display the cause of the error in the `result` field. Here's an example:

```json
{
   "status":"0",
   "message":"NOTOK",
   "result":"Max rate limit reached"
}
```

***

## Max rate limit

> "Max rate limit reached"

This error occurs when you exceed the rate limit assigned to your specific API key. To resolve this, adhere to your available plan by waiting for the required amount of time before each request. If you are using a script or application, apply throttling to limit the frequency of calls.

Here's a handy reference for various API tiers and their rate limits 🚧.

| API Tier |                Rate Limit               |
| :------: | :-------------------------------------: |
|   Free   | 5 calls/second , up to 10,000 calls/day |

***

## Missing or invalid action

> "Error! Missing or invalid action name"

This error occurs when you do not specify a module or action name, or specify an invalid module and action name. To resolve this issue, double check your API query to use a valid module and action name. If you require some help getting started, try copying the sample queries provided in the documentation and pasting them into your browser.

***

## API specific errors

> "Error! Block number already pass" "Error! Invalid address format" "Contract source code not verified"

These error messages returned are specific to certain APIs and related parameters. To resolve this issue, kindly refer to the specific API's documentation, and check for the correct format or values to be specified as parameters.
