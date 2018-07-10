# Reblogging Post

To reblog ("resteem") a post, we can use a custom json operation that is handled by the follow plugin.

For this operation, we will use `custom_json` and a properly formed id and payload so that `follow_plugin` will pick up the reblog data and display the selected post in the feed of the account doing the reblog.

### Sections

1. [Making the api call](#making-the-api-call) - broadcasting the operation
    1. [Example api call](#example-api-call) - make the call in code
    1. [Example api call using script](#example-api-call-using-script) - using our tutorial script
    1. [Example Output](#example-output) - output from a successful call
1. [Custom JSON Fields](#custom-json-fields) - understanding the result
1. [To Run](#to-run) - Running the example.

### Making the api call

To broadcast the operation, we can use a `Radiator::Transaction` instance:

```ruby
tx = Radiator::Transaction.new

# .
# ... your code here
# .

tx.process(true)
```

Passing `true` to `Radiator::Transaction#process` will broadcast the operations queued in the `operations` array of the transaction.

#### Example api call

If we want to reblog, we need to form the `json` properly, for example:

```ruby
data = [
  :reblog, {
    account: reblogger,
    author: author,
    permlink: permlink
  }
]

tx.operations << {
  type: :custom_json,
  id: 'follow',
  required_auths: [],
  required_posting_auths: [reblogger],
  json: data.to_json
}
```

#### Example api call using script

And to do the same with our tutorial script:

```bash
ruby reblogging_post.rb https://steemit.com/@inertia/kinda-spooky
```

#### Example Output

From the example we get the following output from our script:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "id": "0aa41e06b2612315d32cadeb671eb1201f266dd7",
    "block_num": 24063620,
    "trx_num": 19,
    "expired": false
  },
  "id": 3
}
```

The response we get after broadcasting the transaction gives us the transaction id ([`0aa41e0...`](https://steemd.com/tx/0aa41e06b2612315d32cadeb671eb1201f266dd7)), block number ([`24063620`](https://steemd.com/b/24063620)), and the transaction number of that block (`19`).

### Custom JSON Fields

Broadcasting a `custom_json` operation will require the following fields:

* `id` - set to `follow` to indicate that this operation is handled by the `follow_plugin`
* `required_auths` - leave this as an empty JSON Array, we only need the posting authority
* `required_posting_auths` - JSON Array containing the account name with the posting authority
* `json` - the actual payload of the operation, containing a JSON Array:
  * First element - `reblog`
  * Second element - JSON Object containing:
    * `account` - account that is doing the reblog op
    * `author` - author of the post being reblogged
    * `permlink` - permlink of the post being reblogged

### To Run

First, set up your workstation using the steps provided in [Getting Started](https://developers.steem.io/tutorials-ruby/getting_started).  Then you can create and execute the script (or clone from this repository):

* `<url>`

```bash
git clone git@github.com:steemit/devportal-tutorials-rb.git
cd devportal-tutorials-rb/tutorials/14_reblogging_post
bundle install
ruby reblogging_post.rb <url>
```
