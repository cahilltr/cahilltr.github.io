---
layout: post
title: "Dad Math  - My Daily Coffee"
date: 2019-07-03 09:35:00 -0500
tags:
- Math
- savings
- cost
- Dads
- coffee
- thermostat
---

# What is "Dad Math"?
Simply put, "Dad Math" is figuring out the cost of things that would normally be inconsequential and putting actual values to keeping the thermostat at a certain temperature.  These are based off of things that a Dad might say to their family.  Other topics I plan to cover include: "Heating and cooling the house", "How much it costs to 'Heat the outside'", "How much beer to keep in the fridge", "Why you need to take shorter showers",  and "How much it costs when you don't turn off the lights".

To start this series off, I'm going to start with determining the actual cost of my daily coffee.

# Daily Coffee
At the beginning of February this year, I got a [Broast Coffee Chicken Cup](http://www.broasttn.com/).

<p style="text-align:center"><img width="800" height="800" src="/images/posts/2019-07-03/Broast_Cup.jpg" /></p>

While the cup cost $200, it allows me to get a free drip coffee or cold brew or cover up to $3 of a specialty drink.  This lead me to an obvious question: at what point do I break even?  Below, I'll cover some of the increasing in-depth math's to find the break even point.

## Coffee drank to cover cost of cup of only drip Coffee
Cost of Drip coffee or a cold brew is `$3.0`.

So, the number of cups to break even is `$200/$3.0 = 66.67 cups or 67 cups`.

Assuming 1 cup per day, it takes 67 days to break even.  I bought the cup in February.  Excluding weekends, that means the break even date is 5/7.

If I include the weekends (Saturday only, they're not open Sunday), the break even point is 4/20.
Realistically, I only go on weekends ~50% of the time.  There are 12 weeks from 2/1 to 4/20, so subtracting 6 days (excluding Sunday and only half of the weekends) would make the break even point 4/27.

This means the rest of the year, I'm in the black (just as I like my coffee).  Or am I?  I have a feeling that there's some extra costs I'm not including.

## Coffee drank to cover cost of cup with some specialty drinks
While I normally get drip coffee or cold brew, I occasionally will get one of their weekly specialty drinks, like Broast's "Via la Mocha", a habeñero mocha.

<iframe src="https://www.facebook.com/plugins/post.php?href=https%3A%2F%2Fwww.facebook.com%2Fbroasttn%2Fposts%2F1961423990830848%3A0&width=500" width="500" height="287" style="border:none;overflow:hidden" scrolling="no" frameborder="0" allowTransparency="true" allow="encrypted-media"></iframe>

When I find a specialty drink I like, I'll tend to get it for multiple days.

For the purposes of this blog, I'll say I get a specialty drink once a week, to even out the weeks where I get a specialty drink everyday and weeks where I only get drip coffee.

The specialty drink costs $1.50 and I'll normally tip $2.00, so the total cost of the drink is now $3.50.

Now we're not actually saving $3 a cup.  Rather we are saving: `$3 - ($3.5/5 days) = $2.3 per day`.

It's just simple division after that to determine how many cups we'll need to break even, `200/2.3 = 86.9 cups or 87 cups`.

Excluding weekends, this means the break even date is now 6/4.
Now, if I  include 50% of weekends, we'll add 10 days (it's 19 weeks from 2/1 to 6/12). Which makes the break even date 5/30.

Or does it?  Now, since we add in getting 6 cups of coffee every other week, rather than the original 5, we'll need to get fancy.  Since the cost/savings per day fluctuates every other week, we'll need to account for that using a piecewise function.  On 5-day weeks, our savings per day is `$3 - ($3.5/5) = $2.3`, while on our 6-day weeks, our savings per day is `$3 - ($3.5/6) = $2.42`.

The resulting piecewise function, in terms of the amount saved per day, is the following:
```text
f(x) =
  2.42, if (x % 11) = 0
  2.42, if (x % 11) > 5
  2.3, if (x % 11) <= 5
```

To find the number of cups to break even, we'll brute force the calculation.  We need to brute force this because I'm unaware of how to solve for `x` when using a piecewise function and it's super easy to write some quick code to do the heavy lifting.  Basically, instead of dividing like we did above, we'll simply add the saved amount value of the piecewise function, until the accumulated savings is greater than or equal to the $200.

```scala
def findValue(x: Int): Double = {
  if ((x % 11) == 0) {
    2.42
  } else if ((x % 11) > 5) {
    2.42
  } else {
    2.3
  }
}

var acc: Double = 0
var cups: Int = 1
while (acc < 200.00) {
  acc = acc + findValue(cups)
  cups = cups + 1
}
println(cups)
```

The above function prints out 86, meaning 86 cups to break even.  Therefore our break even date with 50% of weekends included is 6/5.  It doesn't seem like the piecewise function did much.

### Coffee drank to cover cost of cup and driving every day
We cannot forget that I have to drive to the coffee shop, they don't deliver directly to me (yet).  So we need to include the price of gas.  Gas in my area has been around $2.45 a gallon, so we'll use the number to make things simple.

I'm a 6 mile round trip to the coffee shop and my truck gets about 19 mpg on the trip.

```text
6 miles / 19 mpg = .315789 gallon's used every day.

.315789 gallons * $2.45 = $0.77
```
So a trip to Broast costs 77 cents.

We can calculate a cost per day as the following: `(3.5/5) + .77 = 1.47 cost per day`

We can the calculate the actual saved per day: `$3 - $1.47 = $1.53 saved per day` after removing daily cost.  
Now, it'll take `$200/$1.53 = 130.72 cups or 131 cups` to break even.  Which, from a weekday only point of view, makes the break even date, 8/3.

Now we need to calculate the 50% of weekends I go and get coffee.

There's 27 weeks from 2/1 to 8/3, so we'll use 14 weekend trips. Since the cost/savings per day fluctuates every other week, we'll need to account for that using a piecewise function.

We need to get the cost per day, when I get coffee 6 days a week.  This really just means dividing $3.5 by 6 rather than 5: `(3.5/6) + .77 = 1.35`.

The resulting piecewise function, in terms of the amount saved per day, is the following:
```text
f(x) =
  1.65, if (x % 11) = 0
  1.65, if (x % 11) > 5
  1.53, if (x % 11) <= 5
```

Again, we'll brute force the caculation, using similar code (only the value differ, factoring in driving cost).

```scala
def findValue(x: Int): Double = {
  if ((x % 11) == 0) {
    1.65
  } else if ((x % 11) > 5) {
    1.65
  } else {
    1.53
  }
}

var acc: Double = 0
var cups: Int = 1
while (acc < 200.00) {
  acc = acc + findValue(i)
  cups = cups + 1
}
println(cups)
```

This results in 123 cups, which makes the break even date 7/25.

The piecewise function helps quite a bit more here, as the added cost of driving drags the calculation out.

To help visualize the piecewise function, I used [desmos.com, a web-based graphing tool](https://www.desmos.com/calculator/woigw9qykh), which was invaluable.

## Summary
These numbers hold true for me.  I also have to go out of my way to get the cup of coffee, which is well worth the time and effort as it gets me out of the house.  The cost of driving would be less if Broast were along the way to work, as I'd factor in a smaller amount (or nothing) depending on how far out of the way I'd have to go to get my coffee.

To concisely summarize article, the true "break even date" is 7/25 or 123 cups of coffee.  Which gives me 136 days (including all weekends), to take advantage of "free" coffee.

If you're ever in the area, I'd strongly suggest stopping by Broast for a cup of coffee.

If you find an error, let me know so I can go and fix it.
