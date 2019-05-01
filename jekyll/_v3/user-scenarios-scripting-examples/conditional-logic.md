---
layout: classic-docs
title: Conditional Logic
description: Some test cases require behavior to be dependent on previous requests. You can build conditional logic directly into our scripts in LoadImpact
categories: [user-scenario-scripting-examples]
order: 6
redirect_from: /knowledgebase/articles/835725-conditional-logic
---

***

What if your client behaviour depends on the results of previous requests?

Let’s imagine a coin tossing game. Our client bets on head or tails. The server tosses the coin and returns the result as one of two possible text messages:

Toss result: heads!

or

Toss result: tails!

If the client wins, he should then request `won.php`, otherwise `lost.php`. This means that the client has to make a request to the server to make it perform the coin flip, then read the returned result from the server and act upon its contents, either loading “won.php” or “lost.php”.

{% highlight lua linenos %}
-- Define a list of coin sides
local coin_sides = {"heads", "tails"}
-- Pick a random coin side
local coin_side = coin_sides[math.random(#coin_sides)]
-- Tell server what we're betting on.
local response = http.request_batch({
    {"GET", "http://test.loadimpact.com/flip_coin.php?bet=" .. coin_side, response_body_bytes=1024}
})
-- Extract a coin toss result from server response
local toss_result = response[1].body:match("Toss result: (%w+)!")
-- Check that toss_result contains expected data
if not (toss_result == "heads" or toss_result == "tails") then
    -- Print error to log
    log.error(
        "server returned unexpected response: "
        .. (toss_result or response[1].body) -- toss_result would be nil if find() fails
     )
else -- Toss result is valid, go on
    -- If coin toss result matches our bet, request a victory page
    if toss_result == coin_side then
        log.info('Won!')
        http.request_batch({
            {"GET", "http://test.loadimpact.com/won.php"}
        })
    else -- Otherwise we've lost
        log.info('Lost!')
        http.request_batch({
            {"GET", "http://test.loadimpact.com/lost.php"}
        })
    end
end
{% endhighlight %}

Refer to:

- [The LoadImpact load script API](https://loadimpact.com/load-script-api)
- [Lua math.random()](http://www.lua.org/manual/5.1/manual.html#pdf-math.random)
- [Lua string.match()](http://www.lua.org/manual/5.1/manual.html#pdf-string.match)
- [Lua string patterns](http://www.lua.org/manual/5.1/manual.html#5.4.1)
