# Bridging Mathematics and Graphics: A Dive into 3D Engines with Linear Algebra

![gif](https://github.com/user-attachments/assets/e739e446-7e70-4158-8abf-1b3d9dcc05d3)

## Introduction

This mini-project explores the intersection of 3D graphics and Linear Algebra (LA). Inspired by concepts learned in [MATH0280](https://catalog.upp.pitt.edu/preview_course_nopop.php?catoid=5&coid=6923) during my freshman year, I set out to understand the implementation of LA in a practical setting. Concepts like tensors and LA operations have always fascinated me, and I wanted to refamiliarize myself with them while solving related problems. This journey led me to develop a basic 3D engine in C++.

## Key Concepts

### Purpose of a 3D Engine
A 3D engine's primary goal is to take geometrical data in three dimensions and render it as 2D shapes on a screen. Since screens are inherently 2D, this requires:

- Transforming 3D data into 2D space using LA.
- Leveraging triangles as the simplest 2D shape for building complex structures.
- Implementing algorithms optimized for rendering triangles.

### Core Structures in C++

#### Representing Points and Shapes
```cpp
struct vec3d {
    float x, y, z;
};
```
A 3D point requires `x`, `y`, and `z` coordinates.

```cpp
struct triangle {
    vec3d p[3];
};
```
A triangle consists of three points that define its edges.

```cpp
struct mesh {
    vector<triangle> tris;
};
```
A mesh is a collection of triangles, allowing us to form complex 3D shapes.

#### Projection Matrix
```cpp
struct mat4x4 {
    float m[4][4] = { 0 };
};
```
A 4x4 projection matrix is used to map 3D objects onto a 2D subspace.

---

## Implementation Details

### 3D Cube Representation
#### Cube Points in 3D Space
![cube](https://www.math.brown.edu/tbanchof/Beyond3d/Images/chapter8/image04.jpg)

A cube's location is defined by eight points, each requiring three coordinates (`x`, `y`, `z`). Using a clockwise winding order, we build each face of the cube from two triangles, requiring a total of 12 triangles.

#### Hardcoding Cube Mesh
```cpp
meshCube.tris = {
    // SOUTH
    { 0.0f, 0.0f, 0.0f, 0.0f, 1.0f, 0.0f, 1.0f, 1.0f, 0.0f },
    { 0.0f, 0.0f, 0.0f, 1.0f, 1.0f, 0.0f, 1.0f, 0.0f, 0.0f },

    // EAST
    { 1.0f, 0.0f, 0.0f, 1.0f, 1.0f, 0.0f, 1.0f, 1.0f, 1.0f },
    { 1.0f, 0.0f, 0.0f, 1.0f, 1.0f, 1.0f, 1.0f, 0.0f, 1.0f },

    // NORTH
    { 1.0f, 0.0f, 1.0f, 1.0f, 1.0f, 1.0f, 0.0f, 1.0f, 1.0f },
    { 1.0f, 0.0f, 1.0f, 0.0f, 1.0f, 1.0f, 0.0f, 0.0f, 1.0f },

    // Remaining faces omitted for brevity...
};
```
This defines the cube's geometry as a collection of 12 triangles.

### Projection onto a 2D Screen

To render 3D objects on a 2D screen, we use a projection matrix. Key parameters include:

- **Aspect Ratio**: Relation between screen width and height.
- **Field of View (FOV)**: Angle determining how much of the 3D space is visible.
- **Near and Far Clipping Planes**: Define the visible range of the 3D scene.

#### Projection Matrix Calculation
```cpp
float fNear = 0.1f;
float fFar = 1000.0f;
float fFov = 90.0f;
float fAspectRatio = (float)ScreenHeight() / (float)ScreenWidth();
float fFovRad = 1.0f / tanf(fFov * 0.5f / 180.0f * 3.14159f);

matProj.m[0][0] = fAspectRatio * fFovRad;
matProj.m[1][1] = fFovRad;
matProj.m[2][2] = fFar / (fFar - fNear);
matProj.m[3][2] = (-fFar * fNear) / (fFar - fNear);
matProj.m[2][3] = 1.0f;
matProj.m[3][3] = 0.0f;
```

#### Matrix Multiplication for Transformation
```cpp
void MultiplyMatrixVector(vec3d& i, vec3d& o, mat4x4& m) {
    o.x = i.x * m.m[0][0] + i.y * m.m[1][0] + i.z * m.m[2][0] + m.m[3][0];
    o.y = i.x * m.m[0][1] + i.y * m.m[1][1] + i.z * m.m[2][1] + m.m[3][1];
    o.z = i.x * m.m[0][2] + i.y * m.m[1][2] + i.z * m.m[2][2] + m.m[3][2];
    float w = i.x * m.m[0][3] + i.y * m.m[1][3] + i.z * m.m[2][3] + m.m[3][3];

    if (w != 0.0f) {
        o.x /= w; o.y /= w; o.z /= w;
    }
}
```
This function applies the projection matrix to 3D points, transforming them into normalized screen coordinates.

---

## Findings and Insights
- The field of view dramatically affects the visualization of 3D objects.
- Normalization ensures that only objects within the screen’s dimensions are rendered.
- Matrix multiplication is a cornerstone of rendering pipelines, showcasing the practicality of LA in graphics.

---

## Glossary

- **Vector**: A quantity defined by magnitude and direction.
- **Matrix Multiplication**: Combines two matrices into a new matrix, enabling transformations.
- **Normalization**: Scaling a vector to have a magnitude of 1 while maintaining direction.
- **Projection Matrix**: Maps 3D points to 2D space.
- **Field of View (FOV)**: Angular extent of the visible scene.
- **View Frustum**: The 3D region visible from the camera, defined by clipping planes.

---

## Licensing Information

- Portions of this project, specifically `Engine3D.c` and `olcConsoleGameEngine.h`, are licensed under the OLC-3 License:
  [OLC-3 License](https://github.com/OneLoneCoder/Javidx9/blob/master/LICENCE.md)
  © 2018-2022 OneLoneCoder.com.

