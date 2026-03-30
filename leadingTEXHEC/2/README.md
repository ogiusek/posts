# Leading TEXHEC #2
## Convinience vs Performance in Software

In the world of high performance systems, there’s a persistent myth: "**If it’s fast, it must be painful to write.**"
When building TEXHEC, I faced a choice.

Do I go full "manual mode" and hardcode every asset reference to save every CPU cycle,
or do I build a system that’s actually pleasant to use?\
I chose both.

The secret lies in building **convenient abstractions that respect the hardware**.\
Here is how we handled our Asset and **Registry** modules without killing our 6ms frame budget.

## The "Registry" - Convenience through Reflection
Manually mapping thousands of assets to IDs is a recipe for technical debt.\
To solve this, I built a Registry system.\
Instead of writing boilerplate, I define a simple Go struct:

```go
type Tiles struct {
    Grass    ecs.EntityID `path:"tiles/grass.biom" tile:""`
    Sand     ecs.EntityID `path:"tiles/sand.biom" tile:""`
    Mountain ecs.EntityID `path:"tiles/mountain.biom" tile:""`
}
```

By using **struct tags**, the engine automatically finds the files, registers them in the ECS, and injects the IDs.\
It feels like magic, but it only happens at startup.\
We pay a tiny "convenience tax" during initialization so that the rest of the game runs at full speed.

## The Asset Pipeline: Path → Dispatcher → Cache
Our asset system is built directly on top of our ECS (`ecs.World`). We don’t have a massive, bloated "Asset Manager." We have **Components**.

- **PathComponent**: Stores the string path (e.g., `"hud/btn.png"`).
- **Dispatcher**: A dedicated function for each file extension. When you ask for a `.png`, the PNG dispatcher kicks in.
- **CacheComponent**: Stores the loaded asset as an `interface`.

### Why use Interfaces for Caching?
Purists might argue that `interface{}` adds overhead.\
In reality its cost is less than negligible.\
Fighting to optimize use one pointer for something that big as assets is premature optimization.

## Custom Formats: The `.biom` Example
Sometimes, convenience means creating your own rules.\
We use a custom `.biom` format for our map tiles.

- A `.biom` asset isn't just one file, it's a directory structure (folders `1` through `6` for different tile corners/edges).
- Our dispatcher knows how to read this specific hierarchy and stitch it into a single asset in memory.

This allows the art pipeline to be simple (just drop files in folders) while the engine remains lean and fast.

## The Philosophy: Work With the Hardware
The goal isn't to avoid abstractions, it's to avoid **leaky** ones.

- **Convenience** is for the developer (eg. Registry tags).
- **Performance** is for the machine (eg. ECS).

We don’t fight the hardware, we give it a clear map of where the data is and then get out of the way.
What’s your "guilty pleasure" abstraction that you use despite the performance cost?
Let's talk in the comments.

### See also [**TEXHEC**](https://github.com/cursus-sudio/texhec)

#Golang #SoftwareArchitecture #Performance #ECS #TEXHEC
