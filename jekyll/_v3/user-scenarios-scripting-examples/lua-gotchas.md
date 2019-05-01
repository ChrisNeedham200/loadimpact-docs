---
layout: classic-docs
title: Lua Gotchas
description: Lua is a simple and lightweight programming language, which makes it perfect for load testing. However, it has some unique attributes that you should be aware of
categories: [user-scenario-scripting-examples]
order: 9
redirect_from: /knowledgebase/articles/835737-lua-gotchas
---

***

The programming language Lua is widely accepted as simple, elegant and easy to learn. It is also light-weight and very suitable for resource-intensive applications such as load testing. But, as with any other language, it has its own pitfalls. Here is a short list of gotchas that are most often encountered by people coming to Lua from other languages. (Note that in several code examples we use the function log.log() to print out text. This is the API function provided by the LoadImpact API to log text messages during execution of a load script)

- TOC
{:toc}

#### Conversion to boolean
Any value except `nil` and `false` is considered to be `true` in Lua. Note that this means that `0` is `true` as well:
{% highlight lua linenos %}
 if 0 then
   log.info("zero is true")
 else
   log.info("zero is false")
 end
 --> Prints "zero is true"
 {% endhighlight %}

#### Variables are global by default
You have to explicitly declare local variables using keyword `local`. This means that a typo in variable name may potentially bring you trouble:
{% highlight lua linenos %}
 local has_color = true
 if has_colour then -- Note typo
   log.info("in color")
 else
   log.info("in monochrome")
 end
 --> Unexpectedly prints "in monochrome" since has_colour is nil
 {% endhighlight %}
It is considered a good practice to declare all variables as local and to limit their scope as much as possible.

#### Arrays / tables

Lua has single all-powerful data structuring type, called “table”. All common data stuructures may be represented as a Lua table. Linear arrays in Lua are also represented as tables.

#### Arrays indices start at one, not zero
Keep in mind that array indices in Lua start at one (1):
{% highlight lua linenos %}
 local t = { "one", "two", "three" }
 log.info(tostring(t[0])) --> nil
 log.info(tostring(t[1])) --> one
 {% endhighlight %}

Key `0` does not belong to the array part of the table:
{% highlight lua linenos %}
 t = { }
 t[0] = "zero"
 log.info(tostring(#t)) --> 0
 t[1] = "one"
 log.info(tostring(#t)) --> 1
 {% endhighlight %}
#### Holes in arrays
The table length (i.e. array size) definition in Lua is a bit unusual and worth reading carefully:

 > The length of a table `t` is defined to be any integer index `n` such that `t[n]` is not `nil`
 > and `t[n+1]` is `nil`; moreover, if `t[1]` is `nil`, `n` can be zero. For a regular array, with
 > non-nil values from `1` to a given `n`, its length is exactly that `n`, the index of its last
 > value. If the array has "holes" (that is, `nil` values between other non-nil values), then
 > `#t` can be any of the indices that directly precedes a `nil` value (that is, it may
 > consider any such `nil` value as the end of the array).

Source: http://www.lua.org/manual/5.1/manual.html#2.5.5

This affects not only length operator `#`, but any code that uses it directly or indirectly, like functions `unpack()` and `table.concat()`.
{% highlight lua linenos %}
 local t = { "one", "two", "three", "four" }
 log.info(tostring(#t)) --> 4
 t[3] = nil -- Make a hole
 log.info(tostring(#t)) -- May print either 2 or 4
 {% endhighlight %}
In general, avoid making “holes” in tables that you use as a linear arrays‚ unless you know what you’re doing.

## Where to find more information

**Information available at loadimpact.com**
[The LoadImpact load script API](https://loadimpact.com/load-script-api)

**Official Lua documentation**
[Lua Reference Manual](http://www.lua.org/manual/5.1/)
[Programming in Lua 2nd ed.](http://www.lua.org/docs.html#pil)

**Lua community**
[Lua Unofficial Frequently Asked Questions (FAQ)](http://batbytes.com/luafaq/)
[Lua users wiki](http://lua-users.org/wiki/)
