OK, maybe it's time for databases. Why? I need my state to be in memory. Having state in redis seems like the right idea.

Also, writing init states to json is a little weird, why not use mysql?

Ohohoh also I had some ideas re:testing environments. I should autogenerate a map of your game, and autogenerate tests based on your logic. god knows how. Also make a vm where the game state is updated around you.

Howevvvveeeeer it might be time to do a little comicbot refactor, mostly so that I can heroku-ify it with Rachel in a few weeks. I'd way rather just have her teach me heroku than learn it entirely by myself, and if comicbot doesn't work right than we'll spend all our pairing time on a refactor which is not really helpful. So I'm going to continue dumping hogwarts thoughts in a log while switching gears slightly.

The architecture I want should be fairly simple :goes off and draws:

Unity gives you two fns per script, one on instantiation and one every frame. Also onClick, onAwake. Similarly in hogwarts, NPCs will need to get instantiated and then updated every turn.
