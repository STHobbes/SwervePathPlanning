# Swerve Path Planning: 6831 A05annex

This project is a visual 2D editor for path planning for a swerve drive FRC robot. Read a field
description into this planner as a context for path planning, then draw, tune, and save a path
that can be used as an path input in the autonomous program.

## Field Description

The field is described in a <tt>.json</tt> file read into the planner and displayed as the context
for planning move paths. The field description file has 2 main elements:
- **components**: The field components (elements or assembles) that are generally specific
  to the competition and appear multiple times on the field.
  - **component**:
    - **name**: The name of the component. This name will be used to specify components to be
    added to the field.
    - geometry
- **field**: the description of the field, usually the field outline with minimal additional
  detail. Components can be specified for the field, generally with a position transformation
  and alliance color.
  - **name**: The name of the field, generally the name of this year's competition.
  - geometry - any of the generic geometry specifications described below.
  - **addComponent**:
    - **alliance**: The alliance color, <tt>"red"</tt>, <tt>"blue"</tt>, or <tt>"none"</tt> if the
      component is a neutral component not associated with either alliance.
    - **componentName**: The name of the component
    - **transform**: The positioning transform to be applied to the component.


