We've been talking a lot about inheritance at work recently and I thought that
I would try to solidify some of my thinking about it.

Inheritance has been talked about to death. At this point the opinions seem to range from "You should only use it when it REALLY makes sense" to "Never use this". There's good reason for this, however if you ask people to explain these reasons they will often repeat something they've heard or give you an anecdote about one time when it went really wrong.

Inheritance necessarily complects one object with another. If you inherit an object that then inherits another object you've complected the first object with everything else in the hierarchy. This means that if any of these higher level objects change your lower level objects will have to change. This inflexibility is divisive amongst programmers. Some people actually see this inflexibility as a *benefit*. Because the api is enforced you can be sure that everyone is following the rules. However, the majority of people tend to find that this inflexibility is a net negative. The necessity for change simply outweighs any perceived benefits that you would get by using inheritance.

## The alternative

Instead of reaching for inheritance most OO developers will reach for

## Why do we do this in the first place?

The "need" for inheritance is driven by the simple fact that in OO programming data and behavior are linked. If data and behavior were separated then you would have no need to create large inheritance trees.

The Gang of Four talks about favoring composition in objects. However, If we have functions and we have polymorphic data, then we don't need inheritance OR mixins.

Here's another way to structure this code:

```ruby
class Vehicle
  def self.drive(drivable, miles:)
    drivable
  end
end

def main
  car = {gas: 20, x: 0, y: 0}
  Vehicle.drive(car, miles: 10)

  assert Vehicle.
end
```

Now you may not find these methods aesthetically pleasing, however by removing the actual behavior from our objects and by simply passing around actual data we've actually made our functions even more flexible. The need for inheritance simply goes away.

## Conclusion

In my work, flexibility and the ability to change are of the utmost importance. Companies often are attempting to find the best ways to accomplish their goals. This often means needing to try certain solutions and then iterate on them. Given the choice I'm always going to choose flexibility.

Functions and polymorphic data provide that for me. I think that we should be consider the costs when choosing inheritance because, in my estimation, there's no good reason to ever choose it.
