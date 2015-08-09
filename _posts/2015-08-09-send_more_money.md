---
layout: post
title: Higher Order Functions in Lua: Send More Money
---

# Higher Order Functions in Lua: Send More Money

There's a classic problem for children first learning algebra and deductive reasoning skills known as SEND+MORE=MONEY (which wikipedia calls a cryptarithm and was published in 1924 by Strand Magazine). Each letter is a stand in for a numeric digit and the goal is to find the value for each letter such that it makes a valid equation. The process of deducing it (spoilers) is to realise that M is clearly a 1 (two 4 digit numbers can only make a 5 digit number starting with 1), S can't be a 0 for the same reason, but then is clearly 9, since 9+1 is the only way here to get to 10, making O be 0, and so on. For fun, go deduce the rest on the problem on your own.

But we're programmers. We've reduced the search space down from 100,000,000 just by knowing M=1 and the computer can do the rest of the heavy lifting for us, and we're cool with it being brute-force at this point. Today, however, we want to learn what a Higher Order Function is.

## Functional Programming Isn't This

Here's what _some people_ would tell you is the difference between imperative and functional programming:

    -- Imperative
    X = 1
    function add(Y)
      Z = X + Y
    end
    add(2)
    print(Z)

    -- Functional
    function add(X, Y)
      return X + Y
    end
    Z = add(1, 2)
    print(Z)

This is wrong and misleading, and possibly a tongue-in-cheek joke that went over my head. Since jokes require context in order to be funny, anyone who says this without doing it in context is condemning future programmers to not understanding why functional programming is desirable. Yes, this demonstrates functions that have no side-effects, but it doesn't really teach anything.

## A Real Imperative Solution

As much as it pains me to print it, this is the imperative solution:

    function crappy_solve()
      local m = 1
      for o = 0, 9 do
        for n = 0, 9 do
          for e = 0, 9 do
            for y = 0, 9 do
              for s = 0, 9 do
                for d = 0, 9 do
                  for r = 0, 9 do
                    local send=s*1000+e*100+n*10+d*1;
                    local more=m*1000+o*100+r*10+e*1;
                    local money=m*10000+o*1000+n*100+e*10+y*1; 
                    if (send+more==money) and
                      (s~=e) and (s~=n) and (s~=d) and (s~=m) and
                      (s~=o) and (s~=r) and (s~=y) and (e~=n) and
                      (e~=d) and (e~=m) and (e~=o) and (e~=r) and
                      (e~=y) and (n~=d) and (n~=m) and (n~=o) and
                      (n~=r) and (n~=y) and (d~=m) and (d~=o) and
                      (d~=r) and (d~=y) and (m~=o) and (m~=r) and
                      (m~=y) and (o~=r) and (o~=y) and (r~=y) then
                      print(send, more, money)
                    end
                  end
                end
              end
            end
          end
        end
      end
    end

It's sufficient. If you did this at a job interview and at least got that M=1 before launching into the loop, the interviewer would probably accept that you understood the problem and not make you write the whole thing. The only thing that might need explaining is the big if statement, which is just a convoluted way of making sure the letters are different. Bonus points for moving the ifs up into their outer loops to cut down how much electricity the search burns. That's really where the mess of code will come in, for instance:

      for o = 0, 9 do
        if m ~= o then
          for n = 0, 9 do
            if m ~= n and n ~= o then

## Higher Order What Now?

If you wanted to double all of the values in a table, here's a way to do it (imperatively):

    function double(t)
      for i = 1, #t do
        t[i] = t[i] * 2
      end
    end
    Z = {1, 2, 3}
    double(Z)

Or, the "No Side Effects" or "Pure Function" way to do it:

    function doubled(t)
      local result = {}
      for i = 1, #t do
        result[i] = t[i] * 2
      end
      return result
    end
    Y = {1, 2, 3}
    Z = doubled(Y)

Another way, is with a Higher Order Function. Lua has a thing going for it call "1st Class Functions", which is a way to say that they're values also and can be passed around and created the way numbers and strings and tables can. A higher order function is one that takes a function and returns a new function representing a change it will make. This one isn't obvious because you'll have some functions there that doesn't seem related to the task of doubling an array. Lets look at them first.

This function, "map", looks a lot like the doubled function before, with the odd notion of just the looping over the array as its only action.

    function map(predicate, t)
      local result = {}
      for i = 1, #t do
        result[i] = predicate(t)
      end
      return result
    end

The actual job of doubling would have to be done by the predicate, shown here:

    function double(x)
      return x * 2
    end

    Y = {1, 2, 3}
    Z = map(double, Y)

The map function here has the interesting propery that it's reusable anywhere you want to take one array and produce another array with the values changes, and it uses a 1st class function to do so. Now, here's a function where it's not clear at all what it does if you've never seen it before:

    function partial(func, x)
      return function(y)
        return func(x, y)
      end
    end

This is the first Higher Order Function. It takes a function and value as input, and returns a function as output. It also shows an interesting property, called "Closure", in that the func and x variables are enclosed in the function thats returned and can be used in a later invocation. Again, it's not clear why you'd need this. But there's an interesting nuance to what this function gives you:

    doubled = partial(map, function(x) return x * 2 end)
    Y = {1, 2, 3}
    Z = doubled(Y)

## Mom, this is what I do for a living now, please send more money!

Back to the challenge I introduced before, I'd like to create a set of nice functions that abstract away the details of looping and doing condition checks.

This foreach function looks like our previous map function. It does the same kind of work, except the term "continuation" could be thrown around with functions like these. It's a function that continues with another function.

    function foreach(from, to, constrainFn, callbackFn)
      for i = from, to do
        if constrainFn(i) then
          callbackFn(i)
        end
      end
    end


This diffc function is a Higher Order Function. And it kind of looks like the Partial function I introduced before. In this case the work done is special: it's making sure that the inputs given to it are all different and returns true if they are. A sample invocation would be this: `diffc(1)(2, 3, 4) == true` and `diffc(1, 2)(2, 3) == false`.

    function diffc(...)
      local T = {...}

      return function(x)

        local arg = { unpack(T) }
        arg[#arg+1] = x

        for i = 1, #s-1
          for j = i+1, #s do
            if s[i] == s[j] then
              return false
            end
          end
        end

        return true
      end
    end

And the test function is just to get this out of our way.

    function test(m, o, n, e, y, s, d, r)
      local send = (s*1000)+(e*100)+(n*10)+d
      local more = (m*1000)+(o*100)+(r*10)+e
      local money = (m*10000)+(o*1000)+(n*100)+(e*10)+y
      return (send + more) == money
    end

Now, lets see what a proper functional solution to our problem looks like:

    function proper_solve()
      local results = {}
      local M = 1
      foreach(0, 9, diffc(M), function(o)
        foreach(0, 9, diffc(M, o), function(n)
          foreach(0, 9, diffc(M, o, n), function(e)
            foreach(0, 9, diffc(M, o, n, e), function(y)
              foreach(0, 9, diffc(M, o, n, e, y), function(s)
                foreach(0, 9, diffc(M, o, n, e, y, s), function(d)
                  foreach(0, 9, diffc(M, o, n, e, y, s, d), function(r)
                    if test(M, o, n, e, y, s, d, r) then
                      results[#results+1] = { M=M, o=o, n=n, e=e, y=y, s=s, d=d, r=r }
                    end
                  end)
                end)
              end)
            end)
          end)
        end)
      end)
      return results
    end

Here I'm composing the function foreach with the range of numbers we want to loop over, and a function that has been returned from diffc that does the condition checks, with a continueing function that closes over the previous inputs. By this particular line, `foreach(0, 9, diffc(M, o, n, e, y, s, d), function(r)`, we have a function that closes over all of those variables and the only values for r at that point will be valid potential options that are different from the other 7 numbers. Pretty clean, and the indentation doesn't get too bad.

# What?

Alright, the answer is this: 9567 + 1085 = 10652

However, we've shown a different way of approaching a problem, which is to identify the common work that has to be done over and over, and abstract it out into a useful bit of code that we can then drop in our toolbox and make use of again in the future. Thus the point of functional programming: actual legitimate reusability.

