---
name: foundation-cheatsheet
description: Cheatsheet for the foundation python lib and writing python as CAD code.
---

# Foundation Cheatsheet

Use reliable primitives first, then compose.

- Start from `Part`/`Assembly` and explicit `Sketch` planes.
- Prefer clear, dimension-driven constants for repeatability.
- Use `Extrusion`, `Lathe`, and `Hole` as the default operation set.
- Keep transforms explicit with `translate_*`/`rotate` or `TFHelper`.
- End scripts with a direct `show(...)` call for local validation.

## Canonical imports

```python
from cadbuildr.foundation import (
    Part,
    Assembly,
    Plane,
    Sketch,
    Point,
    Line,
    Axis,
    Polygon,
    Circle,
    Ellipse,
    Rectangle,
    Square,
    SVGShape,
    Lathe,
    Extrusion,
    Hole,
    Material,
    TFHelper,
    show,
)
```

## Parts and assemblies

```python
Part() -> Part
Assembly() -> Assembly
Part.add_operation(operation) -> bool | None
Assembly.add_component(component, tf=None) -> bool | None
```

## Planes

```python
(Part | Assembly).xy() -> Plane
(Part | Assembly).yz() -> Plane
(Part | Assembly).xz() -> Plane
Plane.get_parallel_plane(distance: float, name: str = "parallel") -> Plane | None
Plane.get_angle_plane_from_axis(axis: list[float], angle: float, name: str = "rotated") -> Plane | None
```

## Sketches and elements

```python
sketch = Sketch(plane)
Point(sketch, x, y) -> Point
Line(p1, p2) -> Line
Axis(line) -> Axis
```

## Sketch shapes

```python
Polygon(lines) -> Polygon
Circle(center, radius) -> Circle
Ellipse(center, a, b) -> Ellipse
Rectangle.from_center_and_sides(center, length, width) -> Rectangle | None
Rectangle.from_2_points(p1, p2) -> Rectangle | None
Square.from_center_and_side(center, size) -> Square | None
```

## SVG import in sketches

```python
SVGShape(
    sketch,
    svg,
    xshift=0.0,
    yshift=0.0,
    angle=0.0,
    scale=1.0,
) -> CustomClosedShape
```

- Use `SVGShape` to convert an SVG string into a sketch shape, then pass it to `Extrusion` or `Lathe`.
- Keep `viewBox` explicit in the SVG string; the viewbox center maps to the sketch origin before shifts.
- Supported SVG elements: `path`, `text`, `rect`, `circle`, `ellipse`, `line`, `polygon`.

## Pencil API

`Sketch` exposes `sketch.pencil` for draw helpers.

```python
sketch.pencil.move(dx, dy)
sketch.pencil.move_to(x, y)
sketch.pencil.line(dx, dy)
sketch.pencil.line_to(x, y)
sketch.pencil.arc(dx, dy, radius)
sketch.pencil.arc_to(x, y, radius)
sketch.pencil.tangent_arc(dx, dy)
sketch.pencil.tangent_arc_to(x, y)
sketch.pencil.rounded_corner_then_line(dx, dy, radius)
sketch.pencil.rounded_corner_then_line_to(x, y, radius)
sketch.pencil.get_closed_shape()
sketch.pencil.close_with_mirror()
```

## Operations

```python
Lathe(shape, axis, cut=False) -> Lathe
Extrusion([shape], end, start=0, cut=False) -> Extrusion
Hole(point, radius, depth) -> Hole
```

## Transforms

```python
(Part | Assembly).translate([x, y, z]) -> bool | None
(Part | Assembly).translate_x(x) -> bool | None
(Part | Assembly).translate_y(y) -> bool | None
(Part | Assembly).translate_z(z) -> bool | None
(Part | Assembly).rotate(angle, axis=[0.0, 0.0, 1.0]) -> bool | None
```

## Materials

```python
material = Material()
material.set_diffuse_color("red")  # or [r, g, b]
part.set_material(material)
part.paint("blue", transparency=0.5)
```

## TFHelper

```python
tf = TFHelper()
tf.get_tf() -> dict
tf.set_tf(value)
tf.translate([x, y, z])
tf.translate_x(x)
tf.translate_y(y)
tf.translate_z(z)
tf.rotate(axis, angle)
```

## Good practice

- For reusable parts, define subclasses of `Part` and split build logic into methods.
- For reusable assemblies, define subclasses of `Assembly` and compose children explicitly.
