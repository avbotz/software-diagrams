# software-diagrams

AVBotz software diagrams, written in [D2](https://d2lang.com) and laid out with [ELK](https://d2lang.com/tour/elk). On every push to `main`, CI renders each diagram to an SVG and publishes them all at **https://avbotz.github.io/software-diagrams/**, so docs can link to a render, such as `https://avbotz.github.io/software-diagrams/thalassic/vehicle-ros-graph.svg`.

| Diagram | What it shows |
|---|---|
| [thalassic/vehicle-ros-graph](thalassic/vehicle-ros-graph.d2) | The ROS graph on Marlin V3: the devices and their drivers, estimation, vision, control and mission, and the topics and services between them |
| [thalassic/sub-control-internals](thalassic/sub-control-internals.d2) | Inside `sub_control`: the callbacks, the parameters, and each stage of the 50 Hz loop from arming to thrust allocation |
| [thalassic/deployment-current](thalassic/deployment-current.d2) | How the stack gets onto the Jetson and runs there today: a git checkout with its pixi environment, built and started by hand |
| [thalassic/deployment-docker](thalassic/deployment-docker.d2) | A proposal: the same stack as one image built in CI, run on the Jetson with Docker Compose |
| [thalassic/sub_vision](thalassic/sub_vision.d2) | A proposal: prop poses from the OAK-D Pro, with keypoints and PnP checked and refined by underwater stereo depth, then a tracker per prop for the behavior trees |

![Marlin V3 ROS graph](https://avbotz.github.io/software-diagrams/thalassic/vehicle-ros-graph.svg)

## Setup

Install [mise](https://mise.jdx.dev/getting-started.html), then in this directory:

```sh
mise trust && mise install
```

That installs the d2 pinned in `mise.toml`, checked against the checksums in `mise.lock`.

## Working on a diagram

```sh
mise run watch thalassic/vehicle-ros-graph.d2  # live preview in the browser
mise run fmt                                   # format every .d2 file
mise run render                                # render every diagram to an SVG beside it
mise run site                                  # build the website into _site/, as CI does
mise run check                                 # what CI runs
```

Commit only the `.d2`. SVGs are git-ignored and the website is built from the diagrams on `main`. CI fails if a file is unformatted.

## Conventions

- One directory per repository (`thalassic/`, …). A diagram is `<name>.d2`, and the website serves its render at `<directory>/<name>.svg`.
- Every diagram starts with `...@../_config` (with as many `../` as it is deep). `_config.d2` sets the ELK layout engine, the light and dark themes and the padding, so the CLI, the editor extensions and the playground all lay a diagram out the same way, and it holds the `device` class that shared imports use. Files whose names start with `_` are imports, not diagrams, and are not rendered.
- Draw each thing once. When several diagrams show the same thing, it goes in an import, and each diagram spreads that import into its own container. For example, `thalassic/_devices.d2` holds the vehicle's devices, and `thalassic/_vehicle-hardware.d2` adds the network and the Jetson for the deployment diagrams.
- Details that labels repeat but no shape draws, such as a device's IP address or `/dev` node, go in `vars` in an import that each diagram spreads at its root, such as `thalassic/_device-info.d2`, and labels use them as `${imu.dev}`. It has to be the root: vars spread into a container are only visible inside it. D2 doesn't substitute vars inside `|md` blocks, so those still spell the value out.
- No explicit colours. A render switches to its dark theme with the viewer's browser, and colours set in D2 don't switch with it. Tell things apart by shape, container and `style.stroke-dash` instead, and explain them in a legend (`vars.d2-legend`).

## Updating d2

Change the version in `mise.toml`, run `mise install` (which updates `mise.lock`), then `mise run site`, and look over the renders in `_site/` before committing: layouts change between d2 releases.
