Coffee Machine
===

This is a mad scientist's coffee machine... but that's just a stylistic choice, not much of a useful description.  In terms of utility, think of it like a coffee machine like you might get in a gas station.  The idea is not to make a fantastic cup of coffee for a coffee aficionado.  The idea is to make coffee as automatically as possible.

We do this by taking an entire cheap coffee machine and putting it on what is effectively a weigh scale.  Then we dump various sorts of granules and powders into the filter, dump water into the water tub, and turn it on.  Hey, presto, coffee.  Throw in some cold water after, and you get coffee that is actually drinkable in a very short amount of time, and with extremely low effort.  Well, low effort at the point of use, anyway.  You do have to build the thing first...

Now, I said "some" a lot above, but using this method we can measure the ingredients extremely accurately and repeatably, which means you should be able to make the very best coffee it is possible to make using this basic technique and the ingredients you put in, every time.  That's starting to sound pretty good, right?  Add in large supplies of ingredients, and it becomes largely one-button to perfection.  The sort of thing even a massively lazy fuck-up like me might use!

So, what's our minimum viable product?  Well, I'm very much not a coffee snob.  In this case, that sort of works against me.  Some of my friends would be happy with just coffee grounds and hot water.  I want coffee grounds, sugar/sweetener, hot water, cold water, and milk.  For the simplicity of not having to deal with refrigeration, I'm willing to compromise on powdered creamer instead of milk, which seems like a pain to deal with.  This takes me down to two sorts of ingredient: granular and easy fluid.

Electronically controlled dispensing of water is very much a solved problem.  There are two major methods, the first of which is a pump, and the second is the one we are likely to use: a solenoid valve.

Powders is ... less simple.  The method we are attempting to implement now is to have a container -- a soda bottle, in particular -- coupled to a plastic tube, which is then squished by an oval-shaped piece of 3d-printed plastic.  That bit of plastic is attached to a stepper motor.  On the other side of the oval is a button, arranged such that when the tube is fully pinched by the tube, the button will also be pressed.

Hopefully, this arrangement will mean that to start the contraption off right, we can check if the button is pressed.  If it's not, we simply keep stepping the stepper until it is pressed.  While that may dump some of the contents from the container, it won't if the dispenser was already closed, which should be the normal case.  It will reliably get us into a known position, which is the point.

From there, when it's time to make a coffee, the steps go something like this...

1. Wait for the user to press the go button.

1. Wait for the weight to stabilize.

1. If the weight isn't higher than the known weight was before the button was pressed, then the user hasn't actually put in a mug.  Beep or something, and go back to 1.

1. For each ingreedient, 