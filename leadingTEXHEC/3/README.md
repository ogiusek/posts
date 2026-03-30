# Leading TEXHEC #3
## Why we built our own ECS
Building your own framework is often called a trap.\
Most people will tell you to just grab an existing engine and start moving.\
For me, the choice came down to a fundamental dichotomy.\
You either adapt your vision to fit someone else’s limits, or you build a tool that truly understands your problem.

To solve the scale of TEXHEC, I needed total control.\
Generic solutions are built to handle every possible game type, which means they carry weight I don't need.\
By building our own Entity Component System (ECS) in Go, we stopped fighting the tool and started owning the hardware.

## Ownership of the Problem
When you write the core logic, you understand every single memory access.
In TEXHEC, we treat the world grid as a single component rather than creating a unique entity for every singular tile.
This keeps our entity count manageable while still simulating 1,000,000 tiles in real time.

This isn’t about being a purist.
It is about having the power to change how data flows when the simulation gets heavy.

## Data Oriented Design without the Friction

We followed Data Oriented Design (DOD) to keep things fast.
The secret is in how we store data.
We use sparse structures that keep components packed together.

- **Cache Efficiency**: The CPU doesn't have to hunt for data. It finds what it needs right where it expects it.
- **Minimal Pointers**: This is how we made the Go Garbage Collector a non issue. If you don't give the GC a massive web of pointers to scan, it stays out of your way.

## The Power of Lazy Listeners
One of the best parts of a custom ECS is defining how systems talk to each other.
I implemented a logic called BeforeGet.
Instead of a system constantly pushing updates that nobody might use, we use Dirty Sets.
We mark data as changed, which is a tiny operation taking about 2ns.
The actual heavy calculation only happens the moment a system asks for that specific data.
We only work when we absolutely must.

## Simplicity as a Feature
In our engine, simplicity isn't just a goal.
It is the reason we have performance.
We don't have layers of legacy code or complex abstractions that we didn't write ourselves.
Total architectural freedom let us choose mental models that fit a massive RTS.
The journey of building this ECS taught me that "fast" code doesn't have to be "complex" code.
It just needs to be the right code for the job.
What is your experience with custom tools?
Do you prefer the safety of a big engine or the freedom of your own code?
Let’s chat in the comments.
Do you find that the lack of external constraints in your own framework actually speeds up your feature development,
or do you miss having a standard library of components to lean on?

### See also [**TEXHEC**](https://github.com/cursus-sudio/texhec)

#DataOrientedDesign #SoftwareEngineering #Golang #GameDev #TEXHEC
