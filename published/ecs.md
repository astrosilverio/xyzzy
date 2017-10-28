The big update from like months ago is that I'm working on [a package called Braga](https://github.com/astrosilverio/braga), which models "things" and their abilities and relationships between them. I will use Braga to make the `Rooms` and `Things` and `Players` in my text adventure. I'm using a pattern called ECS (Entity-Component-System) that is primarily used for graphics games, but hey, it'll do the trick.

### ECS In a Nutshell

Entities represent whole "things"--like a cat. Components are groups of attributes or methods that represent abilities or qualities of things. To give an Entity, like a cat, abilities, the cat needs to be associated with Components, specifically instances of Components. For example, `Location` could be a Component class; or `Portable`, or `Container` or `CanBeSwitchedOn` or `Flying` or something else of the sort. To give the cat the quality of being alive, give the cat an instance of the `Alive` Component class; that `Alive` instance will store whether on not the cat is alive.

The S of ECS is for System; Systems let you group together Entities for simultaneous updates, and provide a service interface for making changes to Entities with a specified Component profile.

One of the tenets of ECS is that the Entities should not store any information about their abilities or traits; they are just an identity that ability-bearing Components get tacked on to. This setup obviates the problem of complex inheritance trees--if you want two types of Entity to share a behavior, give them both an instance of the appropriate Component instead of having them inherit from each other or a parent class. It prevents classes from growing to gigantic proportions--in my original text adventure, my `Room` class was quite large, now a `Room` is just a collection of the appropriate Components, which taken individually are a manageable size. However...

#### Fake Object-Orientation

When I started playing with ECS, the first thing I noticed was that I was annoyed that attributes did not belong to Entities. While that is the design feature that makes ECS so extensible / unique / useful, it is also really irritating if every time you want to check if the cat is alive, you have to check that attribute on a _Component_ on the cat instead of the cat itself.

Besides making it annoying for me to mess around with Entities in the repl, I realized that the extra overhead required to access attributes would complicate my text adventure code. I don't want `hogwarts` to know how its `Room`s and `Thing`s and `Player`s work under the hood. So I violated the guiding principle of ECS a bit and am faking object orientation by rewriting `__getattr__` on Entities.

The familiar way of accessing attributes with dots, like `cat.components`, is just sugar for `getattr(cat, 'components')`. Normally, `__getattr__` will raise an `AttributeError` if an attribute is not found. If I wasn't faking object-orientation, then `cat.components` would not raise an `AttributeError` but `cat.alive` would, because the `cat` Entity does not have an attribute `alive`. But if you hack the Entity's `__getattr__` like so:

```
    def __getattr__(self, name):
        for component in self.components:
            try:
                attr = getattr(component, name)
            except AttributeError:
                pass
            else:
                return attr
        raise AttributeError
```

Then the Entity will search through all of its Components to see if any of them have the attribute before throwing up its hands to say "I don't have that attribute". The result? I can access attributes that are stored in the Entity's Components as if they were actually stored on the Entity itself. Fake object orientation! For example, in the case of a cat with an Alive Component attached to it...

```
> cat = Entity()
> cat.components.add(Alive())
> cat.alive
  True
> cat.die()
> cat.alive
  False
```
