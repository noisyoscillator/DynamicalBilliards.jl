All plotting functionality of `DynamicalBilliards` lies within a few well-defined functions that use the `PyPlot` package to plot aspects of the system.

All plotting functions are brought into scope when `using PyPlot`. The functions are:
```@docs
plot_obstacle
plot_particle
plot_cyclotron
plot_billiard
animate_evolution
```

## Examples

### Plotting Obstacles with keywords
```@example obstacles
using DynamicalBilliards, PyPlot

bd = billiard_sinai()

figure()
plot_obstacle(bd[2]);
plot_obstacle(bd[4], color = "blue", linestyle = "dotted", lw = 5.0);
plot_obstacle(bd[1], facecolor = "yellow", edgecolor = "black");
savefig("rand_obstacles.svg"); nothing # hide
```
![](rand_obstacles.svg)

### Plotting a Billiard

```@example 8
using DynamicalBilliards
bd = billiard_iris()
```

If you want to quickly plot the entire billiard with default parameters, simply use the function `plot_billiard(bd)`:

```@example 8
using PyPlot
plot_billiard(bd)
savefig("billiard_example.svg"); nothing # hide
```
![](billiard_example.svg)

[`plot_billiard`](@ref) also sets up the axis to have equal aspect ratio and sets up the axis limits to be just large enough to contain the entire billiard.



### Plotting particles

Following the above example, we create and plot a particle using the function `plot_particle`:
```@example 8
p = randominside(bd)
plot_particle(p)
# Plot one more particle with purple color,
# pentagon shape and bigger size (default is s=30):
p2 = randominside(bd)
plot_particle(p2; color=(0.5, 0, 0.8), marker="p", s=60.0)
savefig("particles_example.svg"); nothing # hide
```
![](particles_example.svg)

(notice that the particle position and direction are random)

## Color conventions
The default plotting settings have been chosen for maximum clarity and consistency. The color conventions followed are:
* Particles are black.
* Reflecting obstacles (e.g. `Disk`, `FiniteWall` etc.) are green.
* Randomly reflecting obstacles (e.g. `RandomDisk` or `RandomWall`) are purple.
* Ray-splitting obstacles are red with dashed linestyle.
* Periodicity enforcing obstacles are yellow with dotted linestyle (if and when plotted).
* Doors (`InfiniteWall` with `isdoor=true`) are plotted with alternating black and cyan dashed lines.

Particle orbits use matplotlib's color cycle (first one is blue).

## Animating the motion of a particle

The function [`animate_evolution`](@ref) is provided to animate the evolution of a particle from collision to collision.
Let's animate a particle inside a simple pentagon with magnetic field:

```julia
bd = billiard_polygon(5, 1)
a = Disk([0.0,0.0], 0.4)
bd = Billiard(bd.obstacles..., a)
p = randominside(bd, 1.0)

animate_evolution(p, bd, 50; savename = "penta")
```

<video width="100%" height="auto" controls>
<source src="https://github.com/JuliaDynamics/Tutorials-and-Resources/blob/master/billiard_animations/penta.mp4?raw=true" type="video/mp4">
</video>

## Periodic Billiards
In order to plot periodic billiards, you have need to call a different method of
[`plot_billiard`](@ref), since now you
also have to specify the limits of plotting. The
methods provided are:
```julia
plot_billiard(bd, xmin, ymin, xmax, ymax)
plot_billiard(bd, xt::Vector{T}, yt::Vector{T})
```
The last one conveniently plots the combo of particle-trajectory and
periodic-billiard taking care of all the details internally. Give the keyword
`plot_orbit = false` if you do not want to plot the orbit defined by `(xt, yt)`.

For example, the following code
```@example 8
using DynamicalBilliards, PyPlot
r = 0.25
bd = billiard_rectangle(2, 1; setting = "periodic")
d = RandomDisk([0.5, 0.5], r)
d2 = Disk([1.5, 0.5], r/2)
bd = Billiard(bd.obstacles..., d, d2)
p = randominside(bd)
xt, yt, vxt, vyt, t = construct(evolve!(p, bd, 20)...)
plot_billiard(bd, xt, yt)
plot_particle(p)
savefig("rectperiodic.svg"); nothing # hide
```
![](rectperiodic.svg)

And, you can also periodically plot billiards with hexagonal periodicity. Only give
the keyword argument `hexagonal = true` to [`plot_billiard`](@ref). As an example:

```@example 8
bd = billiard_hexagonal_sinai(0.3, 1.50; setting = "periodic")
d = RandomDisk([0.62, 0], 0.2)
d2 = Antidot([0.62/2, 0.5], 0.25)
bd = Billiard(bd..., d, d2)

p = randominside(bd, 0.2)
xt, yt = construct(evolve(p, bd, 10)...)

xmin = minimum(xt); xmax = maximum(xt)
ymin = minimum(yt); ymax = maximum(yt)

plot_billiard(bd, xt, yt; hexagonal = true)
savefig("hexperiodic.svg"); nothing # hide
```
![](hexperiodic.svg)


## Boundary Map plots
```@docs
plot_boundarymap
```
Examples are shown in the [phase spaces](basic/phasespaces.md) section.
