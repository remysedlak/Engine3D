# Exploring 3D Graphics in C++ Through Linear Algebra Concepts

![gif](https://github.com/user-attachments/assets/e739e446-7e70-4158-8abf-1b3d9dcc05d3)

#### This mini project aimed to explain past concepts from [MATH0280](https://catalog.upp.pitt.edu/preview_course_nopop.php?catoid=5&coid=6923)
- I took LA in the spring of my Freshman year.
- I was really fascinated with tensors and the powers of LA operations.
- While practicing LeetCode problems, I ran into past concepts that I wanted to refamiliar myself with.
- My curiosities led me to explore the real world implementations of LA.

# My Insights and Findings

#### The purpose of a 3D engine is to take a 3D geoemtical data and convert it into 2D shapes.
- 3D graphics techincally cannot exist since a screen is 2D.
- Linear algebra  helps transform our data to the proper dimension.
- A triangle is the most simple 2D shape, so it would make sense to combine them to make any other 2D shape.
- A triangle only consists of three lines, so there are amazing algorithms already built to build and draw triangles.

#### C++ 3D representation
---
```
struct vec3d
{
	float x, y, z;
};
```
- A point in the 3D plane requires an x, y, and z value.
---
```
struct triangle
{
	vec3d p[3];
};
```
- A triangle has 3 points that can be used to draw it's lines
---
```
struct mesh
{
	vector<triangle> tris;
};
```
- A mesh is a collection of triangles with points connected together to form a more complex shape.
---
```
struct mat4x4
{
	float m[4][4] = { 0 };
};
```
- A 4x4 projection matrix will also be needed for mapping our shapes onto a 2D subspace
---

#### 3D cube points in normal field
![cube](https://www.math.brown.edu/tbanchof/Beyond3d/Images/chapter8/image04.jpg)

#### We can describe a cube's location with it's 8 points
- This requires 3 coordinates per point (x,y,z)
- The cube graphics use a clockwise winding order (triangle points are stored in clockwise order)
- Each face of the cube is built from two triangles
- To build a cube, 12 triangles will be needed

#### C++ 
```
# Declaration of the 12 triangles needed to form a 3D cube.

meshCube.tris = {

	// SOUTH
	{ 0.0f, 0.0f, 0.0f,    0.0f, 1.0f, 0.0f,    1.0f, 1.0f, 0.0f },
	{ 0.0f, 0.0f, 0.0f,    1.0f, 1.0f, 0.0f,    1.0f, 0.0f, 0.0f },

	// EAST                                                      
	{ 1.0f, 0.0f, 0.0f,    1.0f, 1.0f, 0.0f,    1.0f, 1.0f, 1.0f },
	{ 1.0f, 0.0f, 0.0f,    1.0f, 1.0f, 1.0f,    1.0f, 0.0f, 1.0f },

	// NORTH                                                     
	{ 1.0f, 0.0f, 1.0f,    1.0f, 1.0f, 1.0f,    0.0f, 1.0f, 1.0f },
	{ 1.0f, 0.0f, 1.0f,    0.0f, 1.0f, 1.0f,    0.0f, 0.0f, 1.0f },

	// WEST                                                      
	{ 0.0f, 0.0f, 1.0f,    0.0f, 1.0f, 1.0f,    0.0f, 1.0f, 0.0f },
	{ 0.0f, 0.0f, 1.0f,    0.0f, 1.0f, 0.0f,    0.0f, 0.0f, 0.0f },

	// TOP                                                       
	{ 0.0f, 1.0f, 0.0f,    0.0f, 1.0f, 1.0f,    1.0f, 1.0f, 1.0f },
	{ 0.0f, 1.0f, 0.0f,    1.0f, 1.0f, 1.0f,    1.0f, 1.0f, 0.0f },

	// BOTTOM                                                    
	{ 1.0f, 0.0f, 1.0f,    0.0f, 0.0f, 1.0f,    0.0f, 0.0f, 0.0f },
	{ 1.0f, 0.0f, 1.0f,    0.0f, 0.0f, 0.0f,    1.0f, 0.0f, 0.0f },

};
```
- This declares a hardcoded mesh for a 3D cube.
- These triangle lists declare the points AND the triangles that connect and build more complex shapes.

#### These triangles cannot be drawn onto the 2D screen yet
- Ultimately, projection is required to display 3D data on a 2D subspace.
- The aspect ratio is vital and describes the relation between the **width** and **height** of the projected image.
- A screen is a rectangle but can come in many different aspect ratios, so it's useful to reduce the 3D objects into a normalized screen space. 
![normalized space](https://i.sstatic.net/WNfIA.png)
- Now anything past our domain (-1,1), will not be displayed to the screen, so the field of view will now determine how much of the 3D space is seen.
- A narrow FOV causes the effect of zooming in, and wide FOV's zoom out, allowing more of a field to be seen. This is the design of a [view frustrum](https://github.com/remysedlak/Engine3D?tab=readme-ov-file#view-frustum).


- By accessing our far Z value, and drawing a perpendicular line, we can access an opposite and adjacent angle, so we can use tangent

#### Perspective Projection Matrix

![static](https://i.sstatic.net/1qkwc.png)
#####
- Therefore, by multiplying our 3D matrix by this projection matrix, we can plot our points on a 2D setting; 
- We can perform the multiplication with C++ array manipulation.

#### C++
```
// Projection Matrix

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
matProj represents the matrix we will use on our 3D vector.
- To transform our matrix, we will use **matrix multiplication**


```
void MultiplyMatrixVector(vec3d& i, vec3d& o, mat4x4& m)
{
	o.x = i.x * m.m[0][0] + i.y * m.m[1][0] + i.z * m.m[2][0] + m.m[3][0];
	o.y = i.x * m.m[0][1] + i.y * m.m[1][1] + i.z * m.m[2][1] + m.m[3][1];
	o.z = i.x * m.m[0][2] + i.y * m.m[1][2] + i.z * m.m[2][2] + m.m[3][2];
	float w = i.x * m.m[0][3] + i.y * m.m[1][3] + i.z * m.m[2][3] + m.m[3][3];

	if (w != 0.0f)
	{
		o.x /= w; o.y /= w; o.z /= w;
	}
}
```
The fourth value, w, is used for properly scaling our objects to the normalized screen
- For safety, we won't risk performing divison by zero.
# Glossary

### Vector
In mathematics and physics, vector is a term that refers to quantities that cannot be expressed by a single number, or to elements of some vector spaces. 
- They have to be expressed by both magnitude and direction.

### Matrix multiplication
In mathematics, specifically in linear algebra, matrix multiplication is a binary operation that produces a matrix from two matrices. 
- For matrix multiplication, the number of columns in the first matrix must be equal to the number of rows in the second matrix.

![matrix mult](https://www.mathsisfun.com/algebra/images/matrix-multiply-a.svg)

### Normalizing a vector
To normalize a vector means to scale it so that its magnitude (length) becomes 1 while maintaining its direction. (Think about the cube's 3D coordinates)



### Projection matrix
In linear algebra, a projection matrix is a matrix associated to a linear operator that maps vectors into their projections onto a subspace.

### Field of view (theta)
The field of view specifies the angle of the camera's view. It describes the angular extent of the scene that a camera can capture, meaning a wider FOV indicates a wider viewing angle and a narrower FOV indicates a more zoomed-in angle;

### View Frustum 
A view frustum is the 3D region that represents the visible area of the scene from the camera's perspective. It is shaped like a truncated pyramid and is bounded by:
- Near clipping plane (z near): The plane closest to the camera where objects start to be rendered. 
- Far clipping plane (z far): The plane furthest from the camera where objects stop being rendered. 
Objects outside this range (closer than z near​ or further than z far) are clipped and won't be displayed.

![frustrum](https://learnopengl.com/img/guest/2021/Frustum_culling/VisualCameraFrustum.png)
---
# Licensing Information

- Portions of this project, specifically `Engine3D.c` and `olcConsoleGameEngine.h`, are licensed under the OLC-3 License:
  [OLC-3 License](https://github.com/OneLoneCoder/Javidx9/blob/master/LICENCE.md)
  © 2018-2022 OneLoneCoder.com.
