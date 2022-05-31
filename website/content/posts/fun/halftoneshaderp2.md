+++
title = "Half Tone Shader - Part 2"
path = "posts/fun/halftoneshaderp2"
date = 2022-05-30
template = "page.html"
+++

# Setting up an environment for WGSL

Since we are pretty darn cool and we want to learn something new and fancy, we are obviously going to write our shader in [WGSL](https://gpuweb.github.io/gpuweb/wgsl/) as spoilered in the last part. But beeing cool comes at a price. We can't just use some easy tool like the [ShaderToy](https://www.shadertoy.com/) to develop the shader since it doesn't support WGSL (or at least I'm not aware of it). This means, we need some other home cooked environment in which we can try out our shaders. Unfortunately, this is not done in a minute either.

Since we are lazy, we try to take some shortcuts at least. Instead of setting up a whole [WGPU](https://sotrh.github.io/learn-wgpu/) project, we are just going to use a game engine called [Bevy](https://bevyengine.org/), which abstracts at least some of the boilerplate away. Of course this has some tradeoffs in terms of low level control, but we just want to have a sandbox where we can test some shader programs. In our case the "sandbox" will be a boring plane in 2D space. This is going to be our empty wall, on which we will draw beautiful pieces of art. But I digress ... Let's finally start

First of all, we are going to create an empty project

```zsh
cargo new wgsl-tutorial
```

After that we will include some recent version of bevy in the `Cargo.toml` file. Attention! Deviate from the versions used in this tutorial at your own risk! 

```toml
# ...
# blah
# ...

[dependencies]
bevy = 0.7
```

After that ... it's coding time 😎. Open your `main.rs` and plug in that sweet and juicy bevy boilerplate code:

```rust
use std::time::Duration;

use bevy::prelude::*;
use bevy::window::PresentMode;
use bevy::winit::{UpdateMode, WinitSettings};

const RESOLUTION: f32 = 16.0 / 9.0;
const HEIGHT: f32 = 900.0;

fn main() {
    App::new()
        .insert_resource(WindowDescriptor {
            title: "BEVY SHADER TEST".to_string(),
            width: HEIGHT * RESOLUTION,
            height: HEIGHT,
            present_mode: PresentMode::Mailbox,
            ..default()
        })
        .insert_resource(WinitSettings {
            focused_mode: UpdateMode::ReactiveLowPower {
                max_wait: Duration::from_millis(50),
            },
            unfocused_mode: UpdateMode::ReactiveLowPower {
                max_wait: Duration::from_millis(50),
            },
            ..default()
        })
        .add_plugins(DefaultPlugins)
        .add_startup_system_to_stage(StartupStage::PreStartup, setup)
        .run();
}

fn setup(mut commands: Commands) {
    commands.spawn_bundle(OrthographicCameraBundle::new_2d());
}
```

This will basically just set up an empty window with a grey background like this one over here.

![Screenshot Boilerplate](../../../halftoneshader/boilerplate.png)

There are also some nice extras for you in there 🍬🍬🍬

We used a pretty new feature of bevy, the Power Saving Modes.

```rust
        // ...
        .insert_resource(WinitSettings {
            focused_mode: UpdateMode::ReactiveLowPower {
                max_wait: Duration::from_millis(50),
            },
            unfocused_mode: UpdateMode::ReactiveLowPower {
                max_wait: Duration::from_millis(50),
            },
            ..default()
        })
        // ...
```

Basically the screen is rendered less frequently with these settings so that your computer won't go boom 🔥 when there is nothing changing on the screen but you still want occasional screen updates. You don't need to use the Power Saving Modes, but it's a neet feature I just wanted to highlight here. After all that has been done, we can start integrating some shader code.

The following code was heavily inspired by some well structured videos on youtube from my guy [Logic Projects](https://www.youtube.com/channel/UC7v3YEDa603x_84PgCPytzA). In particulat I'm talking about these two videos of his:

- [Basic Shader Setup in Bevy](https://www.youtube.com/watch?v=EKS0SSq8UPQ)
- [Bind Groups Explained](https://www.youtube.com/watch?v=_hX37bsdYao)

This is going to be a bit lengthy, but when we made it through, we don't need to touch the code that follows ever again and we can stay in shader-land as long as we want.
