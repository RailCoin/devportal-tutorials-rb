# How to Get Post Details

Explaining the most commonly used fields of the requested content.

### Intro

The purpose of this tutorial is fetch the contents of a specified post to get an overview of all data related to that post.

We will also explain the most commonly used fields from the response object.

### Post content

Most console applications that use the `get_content` method are probably looking for the `body` to parse.  But there are many other fields to look at.  Let's break them down by use:

#### `parent_author`

Your application can determine if the content is a root post or reply by looking at the `parent_author` field.  If it's empty, then you're working with a root post, otherwise, it's a reply.

Once you know you're dealing with a reply, other fields can be useful for additional details.  For instance, `root_author`, `root_permlink`, and `root_title` can be used to figure out what the original post details are, even if the reply is deeply nested.

#### `last_update` and `created`

If you'd like to determine if the content has been modified since it was originally posted, you can check `last_update` and `created`.  If they are the same, then there has been no modification.

#### `cashout_time`

You can use `cashout_time` to determine if the content has reached payout.  If `cashout_time` is in the future, the content has not been paid yet.  You can determine the possible future payout by inspecting `pending_payout_value`.

Even before payout, you can determine what the `max_accepted_payout` is.  Most often, this is set to one of two values by the author:

* `1000000.000 SBD` - Accepted Payout
* `0.000 SBD` - Declined Payout

In addition to `max_accepted_payout`, the author may specify how much of the author reward should be in STEEM Power or liquid rewards.  The most common settings are:

* `10000` - Maximum Liquid Reward
* `0` - STEEM Power Only

Once the payout time has arrived, it's possible to determine the split between author and curation by inspecting at `author_rewards` and `curator_payout_value`.

#### `beneficiaries`

Some content will have a `beneficiaries` array.  This is used to determine reward routes any account, up to eight.  Payouts are in STEEM Power and are expressed as a reward percentage of the author reward.

#### `active_votes`

The `active_votes` field is an array that shows all votes applied to the content, including upvotes, downvotes, and unvotes (where a vote previously cast is revoked).

#### `json_metadata`

The `json_metadata` is a string of JSON that can be parsed to determine things like `tags` and `app`.  Other data may be present, depending on the application that created the content.

### Script

To request content to work with, we're using the `get_content` method:

```ruby
api = Radiator::Api.new

api.get_content(author, permlink) do |content|
  # work with content
end
```

Now we have a `content` object to inspect.  This script reads the content and displays a summary of the fields.  For example, we can get the of a post from `content.title`.

### To Run

First, set up your workstation using the steps provided in [Getting Started](https://developers.steem.io/tutorials-ruby/getting_started).  Then you can create and execute the script (or clone from this repository):

```bash
git clone git@github.com:steemit/devportal-tutorials-rb.git
cd devportal-tutorials-rb/tutorials/05_get_post_details
bundle install
ruby get_post_details.rb https://steemit.com/steemdev/@steemitdev/announcing-the-steem-developer-portal
```

### Example Output

```
Post by steemitdev
	title: Announcing the Steem Developer Portal!
	permlink: announcing-the-steem-developer-portal
	category: steemdev
	body_length: 2342 (381 words)
	posted at: 2017-10-30T16:34:27, updated at: 2017-10-30T16:34:27, active at: 2018-04-11T10:34:00
	children: 66
	net_rshares: 0
	vote_rshares: 0
	payout:
		max_accepted_payout: 0.000 SBD
		percent_steem_dollars: 100.00 %
		payout at: 2017-11-06T16:34:27 (235.2 days ago)
		author_rewards: 0.000 SBD
		curator_payout_value: 0.000 SBD
		total_payout_value: 0.000 SBD
	promoted: 0.000 SBD
	total_vote_weight: 0
	reward_weight: 100.00 %
	net_votes: 181, upvotes: 234, downvotes: 1, unvotes: 0, total: 235, top voter: thejohalfiles
	allow_replies: true
	allow_votes: true
	allow_curation_rewards: true
	author_reputation: 14487360227924
	tags: steemdev, steem, dev
	app: steemit/0.1
```
