---
published: true
title: "KSP2 Dev Diary: Orbit Tessellation"
layout: post
tags:
  - math
  - geometry
  - algorithm
  - rendering
  - ksp2
comments: true
note_class: alert alert-info
note_prefix: Note
note_body: >-
  I originally wrote this article as an official [Kerbal Space Program 2 dev diary](https://www.kerbalspaceprogram.com/dev-diaries) in April of 2021. It assumes that the reader has basic familiarity with [Kerbal Space Program](https://www.kerbalspaceprogram.com/games-kerbal-space-program).
  <br /><br />
  You can find the original post [on the KSP forums](https://forum.kerbalspaceprogram.com/index.php?/topic/201736-developer-insights-9-%E2%80%93-orbit-tessellation/).
---

![]({{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation_header.png)


The problems we face in game development rarely have a single “correct” answer. 

The more specialized your game is, the more specialized your problems are, and the more creative your solutions need to be. As a rocketry simulator on an interplanetary scale, Kerbal Space Program has already tackled a wealth of unique programming challenges.

Today I want to share a solution I’ve worked on for a problem that is fairly unique to KSP: _How to draw accurate orbits that look stellar regardless of where they’re viewed from?_


<!-- more -->

<br />

I’ll briefly cover a standard approach for drawing orbits, touch on some of the issues with that approach, and then look at the solution that KSP2 is using now: _screen space orbit tessellation_.

This dev diary will get a bit into the technical side of KSP2’s development, with diagrams to help illustrate some of the core concepts. We will mainly focus on this test scene of Kerbin orbiting Kerbol:

![]({{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-01_overview.png)

Quick disclaimer:

- All visuals were created specifically for this dev diary, and are in no way representative of how anything may look in the final game. It’s just programmer art.
- All code, while functional, is simplified for clarity.
- All orbits are to scale and celestial bodies are a constant screen size to make them easy to see.

<br />

## Orbital Trajectories in KSP

I hope it isn’t too _hyperbolic_ of me to say, but KSP is all about orbits. Building, flying, and crashing rockets are all core to KSP’s identity, but if I had to describe KSP with one defining characteristic, it would be that it teaches you how orbits work on an intuitive level, just by playing it.

While there are definitely exceptions (you know who you are), most players depend on the Map View to plan their journeys from launchpad A to crater B, so being able to display lots of orbits without visual artifacts is critical.

<br />

## What is an Orbit?

An orbit is the path that an object takes as it moves through space while under the gravitational influence of other objects around it. Real life orbits are chaotic and not deterministic. Without performing many small iterative calculations, it is generally not possible to say for certain where an object will be in space at an arbitrary point in time.

This is one of the reasons why KSP simplifies its orbital mechanics so that generally you’re only within the [sphere of influence](https://en.wikipedia.org/wiki/Sphere_of_influence_(astrodynamics)) of one celestial body at a time. These kinds of orbits are known as [Kepler orbits](https://en.wikipedia.org/wiki/Kepler_orbit) where position and velocity at any point on the orbit can be described using parametric equations. With a [parametric equation](https://en.wikipedia.org/wiki/Parametric_equation) we can plug in a parameter—like a [time](https://en.wikipedia.org/wiki/Epoch_(astronomy)) or an [angle](https://en.wikipedia.org/wiki/True_anomaly)—and get an accurate position or velocity.

If a curve can be described as a parametric equation it is easy for us to draw.

<br />

## Drawing Parametric Curves

In general, to draw a parametric curve we need to:

- Choose a **start** and **end** parameter, as well as how many **points** we want to generate.
- Generate the points by passing values into the parametric function that range from **start** to **end**.
- Draw a line between each consecutive pair of points.

For example, to draw a standard [sine wave](https://en.wikipedia.org/wiki/Sine_wave), we can use this parametric function:

```csharp
Vector3 GetParametricPoint(float parameter)
{
    float x = parameter;
    float y = Mathf.Sin(parameter);
    return new Vector3(x, y, 0);
}
```

With that function we can use a **start** and **end** parameter to generate our **points**:

```csharp
void GenerateParametricPoints(
    List<Vector3> points, float start, float end, int count)
{
    points.Clear();
    // Need at least two points to draw a line
    if (count < 2) return; 

    // Generate points using a parameter that
    // inclusively ranges from 'start' to 'end'
    float stepSize = (end - start) / (count - 1);
    for (int step = 0; step < count; ++step)
    {
        float parameter = start + stepSize * step;
        Vector3 point = GetParametricPoint(parameter);
        points.Add(point);
    }
}
```

And assuming that we have a function to draw a line between two points, we can finally draw the lines between the points we just generated:

```csharp
void DrawParametricCurve(List<Vector3> points)
{
    int count = points.Count - 1;
    for (int step = 0; step < count; ++step)
    {
        Vector3 from = points[step];
        Vector3 to = points[step + 1];
        Draw.Line(from, to);
    }
}
```

Here is an example of what that looks like:

<div class="video-container">
    <video class="video-content" autoplay="" controls="controls" playsinline="" preload="none" poster="{{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-02_parametric_thumb.png" onclick="if(this.readyState == 0) this.play()">
    <source src="{{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-02_parametric.mp4" type="video/mp4">
  </video>
</div>

The green line is the ideal sine curve that we want to draw, and the blue line is the result of our **GenerateParametricPoints** and **DrawParametricCurve** functions.

Changing the **start** and **end** parameters (shown here in degrees) affects the position and length of the curve and the more **points** we generate the better the lines match the ideal curve.

<br />

## Drawing an Orbit

Let’s assume that we have the following:

- A parametric equation of our orbit. The exact equation is [outside the scope of this post](https://en.wikipedia.org/wiki/Orbital_elements), but for those curious, you can see better results if it uses the eccentric anomaly as its parameter.
- A **start** and an **end** parameter that we’ll step between. A full orbit’s [parametric equation](https://en.wikipedia.org/wiki/Eccentric_anomaly) generally ranges from 0 to 2π radians, or 0 to 360 degrees.
- The number of points we want to generate. The default in KSP1 is 180 points for a full orbit.
- A graphics package to do the rendering. We need a way to draw a line between two points.

As with the sine wave, we generate points using parameters ranging from the **start** to the **end** value, and by increasing the number of points we generate, we get a smoother looking orbit:

<div class="video-container">
    <video class="video-content" autoplay="" controls="controls" playsinline="" preload="none" poster="{{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-03_adding-points_thumb.png" onclick="if(this.readyState == 0) this.play()">
    <source src="{{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-03_adding-points.mp4" type="video/mp4">
  </video>
</div>

Of course there’s a lot that I’m glossing over here, such as more efficient ways to draw lines than one at a time or other optimizations, but in a nutshell this is how orbits were drawn in KSP1.

<br />

## Adding some Perspective

Evenly distributing the points of an orbit yields excellent results when viewed from far away, specifically when the camera is not close to the plane that the orbit is on. 

However, in KSP you frequently view orbits from extremely flat angles. For example, when zoomed in close enough to see the moon of a planet, the orbit of the planet will be almost completely flat.

Let’s add the orbits of Kerbin’s moons Mun and Minmus to our example scene:

<div class="video-container">
    <video class="video-content" autoplay="" controls="controls" playsinline="" preload="none" poster="{{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-04_zoom-in-corners_thumb.png" onclick="if(this.readyState == 0) this.play()">
    <source src="{{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-04_zoom-in-corners.mp4" type="video/mp4">
  </video>
</div>

When we zoom in on Kerbin’s moons, we see sharp corners on Kerbin’s orbit. The orbit line also misses Kerbin by a significant margin. 

Even though we generated Kerbin’s orbit with 180 points, the distance between two points is still about 5 times larger than _Minmus’ entire orbit_:

<div class="video-container">
    <video class="video-content" autoplay="" controls="controls" playsinline="" preload="none" poster="{{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-05_gap-zoom_thumb.png" onclick="if(this.readyState == 0) this.play()">
    <source src="{{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-05_gap-zoom.mp4" type="video/mp4">
  </video>
</div>

If we evenly distribute the points around the orbit, then with orbits as large as those in KSP we run into scenarios where there may not be enough points from the camera’s point of view to look smooth.

We could increase the point count, but we’ll quickly run into diminishing returns. Much of the orbit already looks smooth so adding points there would be a waste, while due to the angle of the camera other parts of the orbit don’t have enough points.

… What if we could add points only where they’re needed?

<br />

## Screen Space Line Tessellation

If you have two points that were generated with a parametric equation you can generate a new point between them by averaging their parameters:

```csharp
Vector3 pointA = GetParametricPoint(parameterA);
Vector3 pointB = GetParametricPoint(parameterB);

float parameterMid = (parameterA + parameterB) * 0.5f;
Vector3 pointMid = GetParametricPoint(parameterMid);
```

We can start with a small set of points and insert new ones by averaging their parameters. The curve becomes smoother each time we do this:

![]({{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-06_sub-div-iterations.png)

In computer graphics [tessellation](https://en.wikipedia.org/wiki/Tessellation_(computer_graphics)) is the process of dividing geometry to make it smoother. 

We are effectively tessellating our parametric curve. We really only care that the curve appears smooth on the screen, so we want to evaluate the smoothness of our points in _screen space_, where the X and Y coordinates correspond to the point’s screen position:

```csharp
Vector3 screenA = camera.WorldToScreenPoint(pointA);
Vector3 screenB = camera.WorldToScreenPoint(pointB);
Vector3 screenC = camera.WorldToScreenPoint(pointC);
```

With our points in screen space, we can insert a new point between points A and B based on how smooth the curve is there. But how do we define ‘smoothness’?

<br />

## Choosing a Smoothness Heuristic

A heuristic is a rule-of-thumb; a method for solving a problem very fast while being adequately accurate. We need a heuristic to efficiently decide if our points are ‘smooth enough’. 

We’ve mentioned two heuristics so far:

- The **distance** between points. If points are visually close together then they’re less noticeable.
- The **angle** between points. The closer three points are to a straight line the less their middle corner stands out.

A convenient heuristic that incorporates both is the **triangle area**, which we can compute very quickly using the [shoelace formula](https://en.wikipedia.org/wiki/Shoelace_formula#Proof_for_a_triangle):

```csharp
float ComputeTriangleAreaXY(Vector3 a, Vector3 b, Vector3 c)
{
    return Mathf.Abs(a.x*b.y + b.x*c.y + c.x*a.y 
                   - a.y*b.x - b.y*c.x - c.y*a.x) * 0.5f;
}
```

If the triangle of **a**, **b** and **c** in screen space has an area larger than a given upper limit, then we insert a new point between a and b to smooth out the curve. 

We evaluate our heuristic on every consecutive set of three points, until all points satisfy our heuristic, or we reach another end condition like how many points we have in total.

It can help to visualize the triangles:

<div class="video-container">
    <video class="video-content" autoplay="" controls="controls" playsinline="" preload="none" poster="{{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-07_triangle-heuristic_thumb.png" onclick="if(this.readyState == 0) this.play()">
    <source src="{{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-07_triangle-heuristic.mp4" type="video/mp4">
  </video>
</div>

<br />

## Putting it all together

We can apply the triangle heuristic to our orbits, coloring each point based on the Iteration it was added in—red first, then orange, yellow, and so on:

<div class="video-container">
    <video class="video-content" autoplay="" controls="controls" playsinline="" preload="none" poster="{{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-08_subdiv-demo_thumb.png" onclick="if(this.readyState == 0) this.play()">
    <source src="{{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-08_subdiv-demo.mp4" type="video/mp4">
  </video>
</div>

The orbits are tessellated using the top-right camera and the turquoise lines show the viewing bounds of that camera. The top-left shows a close up of Kerbin and its moons. 

The closer the camera moves to the orbit, the more iterations need to be performed to satisfy our heuristic, but we also use far fewer points overall because we only generate points where needed. For example the orbits of Mun and Minmus only generate new points when they become visible. 

Here’s the original shot again with tessellation enabled and all debug drawing removed:

<div class="video-container">
    <video class="video-content" autoplay="" controls="controls" playsinline="" preload="none" poster="{{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-09_zoom-in-smooth_thumb.png" onclick="if(this.readyState == 0) this.play()">
    <source src="{{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-09_zoom-in-smooth.mp4" type="video/mp4">
  </video>
</div>

And that, in a nutshell, is how orbits are drawn in KSP2.

<br />

## Closing Thoughts

When working on programming problems your first solution is rarely perfect, and it’s possible for your solution to have its own problems that you need to solve. 

There is far more that went into the development of this feature that I’d love to share here, from implementation details like additional heuristics, culling optimizations and choosing the right data structure for efficient point insertions, to how this can be used for more than just Keplerian orbits, but this is enough for one post. 

If you enjoyed this technical deep dive and want to see more stuff like it please let us know! Having the chance to share something like this with you all is very special to me. I wouldn’t be in this industry if it wasn’t for so many developers sharing their passions and inspiring me through their work online. 

I’ll leave you with one last debug visualization, showing the spatial partitioning used to decide when points are worth subdividing even if they’re not on screen (but one of their descendants might be):

<div class="video-container">
    <video class="video-content" autoplay="" controls="controls" playsinline="" preload="none" poster="{{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-10_bounds-demo_thumb.png" onclick="if(this.readyState == 0) this.play()">
    <source src="{{site.baseurl}}/public/assets/2022-06-30-KSP2-Dev-Diary_Orbit-Tessellation/orbit-tessellation-10_bounds-demo.mp4" type="video/mp4">
  </video>
</div>
