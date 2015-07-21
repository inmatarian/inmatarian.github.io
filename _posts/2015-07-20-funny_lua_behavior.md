---
layout: post
title: Funny Lua Behavior - debug.setmetatable and object properties
---

# Funny Lua Behavior - debug.setmetatable and object properties

Lua's type-system has more than a few types which allow you to lookup properties. Tables are the obvious, as that's exactly what they're for.

    T = { a = 1, ['b'] = 2 }
    print(T['a'], T.b) -- 1  2

Less obvious, but they makes sense, are strings.

    S = 'hi!'
    print(S.len) -- function: 0031A428
    print(S:len()) -- 3

Likewise, the userdata type, which you're making use of a lot without realizing it.

    io.stdout:write('hi!\n'); -- hi!
    print(type(io.stdout)) -- userdata
    print(io.stdout.write) -- function: 00317FE8

Strings and Userdata have this behavior because they have a metatable.

    print(getmetatable("hi")) -- table: 003194D8
    for k, v in pairs(getmetatable("hi")) do print(k, v) end -- __index table: 003193E8
    print(string) -- table: 003193E8

    print(getmetatable(io.stdout)) -- table: 00317438
    for k, v in pairs(getmetatable(io.stdout)) do print(k, v) end
    -- setvbuf function: 003180A8
    -- lines   function: 00317D08
    -- write   function: 00317FE8
    -- close   function: 00317CE8
    -- flush   function: 00317EC8
    -- __gc    function: 00317D88
    -- read    function: 00317F08
    -- seek    function: 00317DC8
    -- __index table: 00317438
    -- __tostring function: 00317F48

None of thise is funny behavior, this is all documented and normal. What's funny is how the types behave with regards to indexable properties based on wether they have a metatable set or not. For the primitive types, you can set a metatable, except only one for the type, and only via `debug.setmetatable`. So, let's use function as our whipping boy.

    F = function() end
    print(F) -- function: 004129C8
    print(F['hi'])

    stdin:1: attempt to index global 'F' (a function value)
    stack traceback:
        stdin:1: in main chunk
        [C]: ?

    debug.setmetatable(F, {})
    print(F['hi'])

    stdin:1: attempt to index global 'F' (a function value)
    stack traceback:
        stdin:1: in main chunk
        [C]: ?

Expected behavior, right? Now lets see it with a metatable.

    debug.setmetatable(F, {__index={hi='hello'}})
    print(F['hi']) -- hello

The implications are that any particular type you've been handed can have properties on it, since debug.setmetatable might have changed that. But, and this is the important part, just checking for that property could throw an error if that metatable and the __index metamethod hasn't been set.

That's funny behavior, right? 

