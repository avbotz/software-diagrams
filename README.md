# Software Diagrams

AVBotz software diagrams, written in [D2](https://d2lang.com).

CI renders each diagram to an SVG and publishes them all at **https://avbotz.github.io/software-diagrams/**.

## Setup

Install [mise](https://mise.jdx.dev/getting-started.html), then in this directory:

```sh
mise trust && mise install
```

## Working on a diagram

```sh
mise run watch thalassic/vehicle-ros-graph.d2  # live preview in the browser
mise run fmt                                   # format every .d2 file
mise run check                                 # check formatting
mise run render                                # render every diagram to an SVG
mise run site                                  # build the website into _site
```

Commit only the `.d2`. Format files before pushing otherwise CI will fail.

## Conventions

- One directory per repository (`thalassic/`, …). A diagram is `<name>.d2`, and the website serves its render at `<directory>/<name>.svg`.
- Every diagram starts with `...@../_config` (with as many `../` as it is deep). Files whose names start with `_` are imports and are not rendered.
- Avoid duplication. Put common code into a separate file that is then imported.
- Avoid explicit colours. A render switches to its dark theme with the viewer's browser, and colours set in D2 don't switch with it.
