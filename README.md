# Swerve Path Planning: 6831 A05annex

![alt text](./resources/swerve-path-planner.jpg "Swerve Path Planner")
This project is a visual 2D editor for path planning for a swerve drive FRC robot. Read a robot description
and field description into this planner as a context for path planning, then draw, tune, and save a path
that can be used as a path input in the autonomous program.

## Path Spline

The path planner uses an implementation of the
[Kochanek-Bartels Spline](https://en.wikipedia.org/wiki/Kochanek%E2%80%93Bartels_spline) modified
for interactive editing of the tangent vector to implicitly control bias and tension. There is no
continuity control because we want our robot paths to be continuous. The original reference for this
spline can be found at
[Interpolating Splines with Local Tension, Continuity, and Bias Control](https://www.engr.colostate.edu/ECE455/Readings/TCB.pdf).

When control points are created the tangent (derivatives) at that control point and surrounding
control points are computed using the [Cardinal-Spline](https://en.wikipedia.org/wiki/Cubic_Hermite_spline)
formulation with the default tension specified by a program constant. The tangent is adjusted using a
control handle which intuitively manipulates the shape of the spline at the control point to implicitly
edit tension and bias.

### Path Description Format

The path is saved as a list of control points:
- **<tt>"title"</tt>**: (optional, string) A title or name for the path, primarily used as file documentation
  to refresh you on the path this file represents.
- **<tt>"description"</tt>**: (optional, string) A more verbose description if the path, again primarily used as file
  documentation to refresh you on the path this file represents.
- **<tt>"controlPoints"</tt>**: (required, list) The list of control points. A control point is a dictionary
  containing these fields.
  - **<tt>"fieldX"</tt>**: (optional, double, default=0.0) The field X position in meters.
  - **<tt>"fieldY"</tt>**: (optional, double, default=0.0) The field Y position in meters.
  - **<tt>"fieldHeading"</tt>**: (optional, double, default=0.0) The field heading position in radians.
  - **<tt>"time"</tt>**: (optional, double, default=0.0) The time at which this control point should be reached.
  - **<tt>"derivativesEdited"</tt>**: (optional, boolean, default=<tt>false</tt>) Whether the derivatives of the
    control point have been explicitly set. If <tt>false</tt>, then the X,Y velocities at the control point are set
    algorithmically. If <tt>false</tt> then the X,Y valocities specified here are used for the point.
  - **<tt>"field_dX"</tt>**: (optional, double, default=0.0) The field X velocity in meters per second.
  - **<tt>"field_dY"</tt>**: (optional, double, default=0.0) The field Y velocity in meters per second.
  - **<tt>"field_dHeading"</tt>**: (optional, double, default=0.0) The field angular velocity in
    radians per second. Currently, ignored as the derivative is always generated from the heading of the adjacent
    control points.

### Path Description Example

This is the path description for a 2m diameter calibration path:

```json
{
  "description": "2m diameter test circle.",
  "title": "The path for a 2 meter diameter test circle with the robot facing the center of the circle.",
  "controlPoints": [
    {
      "fieldY": 0.0,
      "fieldX": 0.0,
      "fieldHeading": 0.0,
      "time": 0.0
    },
    {
      "fieldY": 1.0,
      "fieldX": -1.0,
      "fieldHeading": 1.5708,
      "time": 1.0
    },
    {
      "fieldY": 2.0,
      "fieldX": 0.0,
      "fieldHeading": 3.1416,
      "time": 2.0
    },
    {
      "fieldY": 1.0,
      "fieldX": 1.0,
      "fieldHeading": 4.7124,
      "time": 3.0
    },
    {
      "fieldY": 0.0,
      "fieldX": 0.0,
      "fieldHeading": 6.2832,
      "time": 4.0
    }
  ]
}
```

### Using a Path Description in Your Autonomous

*To be written.*

## Robot Description

The robot is described in a <tt>.json</tt> file read into the path planner and displayed as the robot during
the path planning, as well as providing the drive geometry and max speed for the modules of the swerve
drive. Having a good description of the robot is helpful in identifying when the planned path exceeds the
capability of the robot (i.e. it just cannot go that fast), and detecting collisions or near collisions
between the robot and game elements.

### Robot Description Format

The robot description is divided into 3 sections:
- **<tt>"title"</tt>**: (optional, string) A title or name for the robot, primarily used as file documentation to refresh
  you on the robot this file represents.
- **<tt>"description"</tt>**: (optional, string) A more verbose description if the robot, again primarily used as file
  documentation to refresh you on the robot this file represents.
- **<tt>"drive"</tt>**: (optional, dictionary) describes the geometry of the drive
  - **<tt>"length"</tt>**: (optional, double, default=0.7) The length of the drive (pivot axis to pivot axis) in meters.
  - **<tt>"width"</tt>**: (optional, double, default=0.3) The width of the drive (pivot axis to pivot axis) in meters.
  - **<tt>"maxSpeed"</tt>**: (optional, double, default=3.0) The maximum module speed (meters/sec)
- **<tt>"chassis"</tt>**: (optional, dictionary) describes the geometry of the chassis (it is currently assumed the drive
  and chassis share the same centroid)
  - **<tt>"length"</tt>**: (optional, double, default=0.9) The length of the chassis in meters.
  - **<tt>"width"</tt>**: (optional, double, default=0.5) The width of the chassis in meters.
- **<tt>"bumpers"</tt>**: (optional, dictionary)
  - **<tt>"length"</tt>**: (optional, double, default=1.1) The length of robot with bumpers in meters.
  - **<tt>"width"</tt>**: (optional, double, default=0.7) The width of the robot with bumpers in meters.

### Example Robot Description file

This is a robot file which describes our 2020 prototype swerve base:

```json
{
  "title": "prototype base, summer 2020",
  "description": "This is the prototype base for A05 annex, FRC 6831, our first experience programming a swerve drive",
  "drive": {
    "length": 0.574,
    "width": 0.577,
    "maxSpeed": 3.1951
  },
  "chassis": {
    "length": 0.762,
    "width": 0.762
  },
  "bumpers": {
    "length": 0.9144,
    "width": 0.9144
  }
}
```

## Field Description

The field is described in a <tt>.json</tt> file read into the path planner and displayed as the background
field context during path planning. The Path Planner always displays the field axes ([0.0,0.0] is center field),
and the standard field outline. The field description adds the game elements for the season-specific game.

To simplify field description there is a section of the description for
game <tt>components</tt> where you describe game elements like the scoring pieces, scoring targets, scoring
piece depots, etc; and a <tt>field</tt> section that lets you position components and describe which
alliance (if any) they belong to.

### Field Description Format

The field is described in a <tt>.json</tt> file read into the planner and displayed as the context
for planning move paths. The field description file has 4 main elements:
- **<tt>"title"</tt>**: (optional, string) A title or name for the field, primarily used as file documentation
  to refresh you on the field this file represents.
- **<tt>"description"</tt>**: (optional, string) A more verbose description if the field, again primarily used as file
  documentation to refresh you on the field this file represents.
- **<tt>"components"</tt>**: (required, list) The list of field components (elements or assembles) that are
  generally specific to the competition for the year, and often appear multiple times on the field. Within
  this list are dictionaries describing the components as:
  - **<tt>"name"</tt>**: (required, string) The name of the component. This name will be used in the field
    description to specify components to be added to the field and must be unique in the list of components.
  - **<tt>"lineColor"</tt>**: (optional, string, default=<tt>"white"</tt>) The outline color or <tt>null</tt> if
    no outline should be drawn, see [Color Description](#Color-Description) for valid color values.
  - **<tt>"fillColor"</tt>**:  (optional, string, default=<tt>null</tt>) The fill color or <tt>null</tt> if
    the geometry should not be filled, see [Color Description](#Color-Description).
  - **<tt>"shapes"</tt>**: A list of shapes which will be rendered using the <tt>"lineColor"</tt> and
    <tt>"fillColor"</tt> directives, see [Shapes Description](#Shapes-Descriptions) for the formats of the
    shapes that are currently supported.
- **<tt>"field"</tt>**: The drawing of the field. By default, the path planner draws the field axes and outline.
  This section describes the things that should be drawn on the field, specifically: components as describes in
  the previous section and additional field geometry. Components are specified by name, an optional alliance color,
  and positioning translation and/or rotation.
  - **<tt>"components"</tt>**: The list of components to be drawn on the field. Within this list are
  dictionaries describing the field placement of components as:
    - **<tt>"component"</tt>**: (required, string) The name of the component which must have been defined in the
      components section of the field.
    - **<tt>"alliance"</tt>**: (optional, string, default=<tt>null</tt>) If this component is being drawn as
      an alliance specific game element, specify the alliance as <tt>"red"</tt> or <tt>"blue"</tt>.
    - **<tt>"translate"</tt>**: (optional, [*x*,*y*], default=[0.0,0.0]) The translation for this component (in
      meters). NOTE: rotations are applied before translations.
    - **<tt>"rotate"</tt>**: (optional, double, default=0.0) The rotation for this component (in radians). NOTE:
      rotations are applied before translations.

#### Color Description

We could have built an interface for describing color by RGB components, but, instead we used the defined
Java [Color](https://docs.oracle.com/en/java/javase/11/docs/api/java.desktop/java/awt/Color.html) names for
simplicity and readability, and to allow us to use <tt>alliance</tt> as the name of a component
colored by the alliance it belongs to. The recognized colors are:
- **<tt>"alliance"</tt>**: Use the alliance color when the component is drawn or filled. This should be used
  for any components that represent alliance specific field markings, goals, pieces, etc. When the
  component is drawn into the field, the alliance color will be specified.
- **<tt>"white"</tt>**: Use white when the component is drawn or filled.
- **<tt>"red"</tt>**: Use red when the component is drawn or filled.
- **<tt>"blue"</tt>**: Use blue when the component is drawn or filled.
- **<tt>"light-gray"</tt>**: Use light gray when the component is drawn or filled.
- **<tt>"gray"</tt>**: Use gray when the component is drawn or filled.
- **<tt>"dark-gray"</tt>**: Use dark gray when the component is drawn or filled.
- **<tt>"black"</tt>**: Use black gray when the component is drawn or filled.
- **<tt>"orange"</tt>**: Use orange when the component is drawn or filled.
- **<tt>"green"</tt>**: Use green when the component is drawn or filled.
- **<tt>"cyan"</tt>**: Use cyan when the component is drawn or filled.
- **<tt>"magenta"</tt>**: Use magenta when the component is drawn or filled.

#### Shapes Descriptions

Shapes are generally very simple descriptions of different types of geometries. Shape representations are
intended to be extended to be extended (i.e. new types of shapes added) if required to represent the game
arena for the season's competition. Each shape is represented by a dictionary with a <tt>"type"</tt> key
describing the shape type, and then type-specific keys and values. These are the shape types, and the
corresponding keys that describe the shape:
- **<tt>"circle"</tt>**: A circle
  - **<tt>"center"</tt>**: (required, [*x*,*y*]) The local X and Y coordinates, in meters, of the center
  of the circle.
  - **<tt>"radius"</tt>**: (required, double) The radius, in meters.
- **<tt>"rect"</tt>**:
  - **<tt>"lower left"</tt>**:
  - **<tt>"upper right"</tt>**:
- **<tt>"polygon"</tt>**:
  - **<tt>"points"</tt>**: A list of points describing the polygon, each point is described as a list
    containing the [*x*,*y*] local X and Y coordinates, in meters, of the point. The points describe a
    path that will be automatically closed.

### Example Field Description file

This is the part of the description of the Infinite Recharge 2020 field:

```json
{
  "title": "Infinite Recharge 2019-2020",
  "description": "The Infinite Recharge arena with only start lines, trenches, pickup, and scoring.",
  "components": [
    {
      "name": "power cell",
      "lineColor": "yellow",
      "fillColor": "yellow",
      "shapes": [
        {
          "type": "circle",
          "center": [ 0.0, 0.0 ],
          "radius": 0.0889
        }
      ]
    },
    {
      "name": "start line",
      "lineColor": "white",
      "fillColor": "white",
      "shapes": [
        {
          "type": "rect",
          "lower left": [ -4.1050, -0.0254 ],
          "upper right": [ 4.1050, 0.0254 ]
        }
      ]
    },
    {
      "name": "ball pickup",
      "lineColor": "alliance",
      "fillColor": "alliance",
      "shapes": [
        {
          "type": "polygon",
          "points": [
            [-2.4626,7.9900],
            [-2.3968,7.9900],
            [-1.7006,7.2998],
            [-1.0104,7.9900],
            [-0.9386,7.9900],
            [-1.7006,7.2280]
          ]
        }
      ]
    }
  ],
  "field": {
    "components": [
      {
        "component": "power cell",
        "translate": [ -3.4001, 0.0]
      },
      {
        "component": "power cell",
        "translate": [ -3.4001, -0.9144]
      },
      {
        "component": "power cell",
        "translate": [ -3.4001, -1.8288 ]
      },
      {
        "component": "start line",
        "translate": [ 0.0000, 4.9766 ]
      },
      {
        "component": "start line",
        "translate": [ 0.0000, -4.9766 ]
      },
      {
        "component": "ball pickup",
        "alliance": "red"
      },
      {
        "component": "ball pickup",
        "alliance": "blue",
        "rotate": 3.1416
      }
    ]
  }
}
```

