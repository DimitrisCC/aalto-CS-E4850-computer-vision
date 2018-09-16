# Aalto CS-E4850 Computer Vision

# Course Overview
Lecturer: Juho Kannala

Terminology:
 - Semantic Information: Ourdoor scene, city: European
 - Object Information: Where are the objects in pixel regions and what are they
 - Geometric Information: Certain surfaces, buildings, walls, vertical/horizontal. Where is the vanishing point / horizon?

Applications:
 - Safety: Self-driving vehicles
 - Health: Telemedicine
 - Security: Surveillance
 - Access: Image-based search, search within image. Search among images.

Where are we?
 - OCR
 - Pattern Recognition
 - Sign Recognition (from different viewing angles, weather variations)
 - Facial recognition
 - Object Detection with ResNets (SSD)
 - 3D Reconstruction

Books:
 - Szeliski: Computer Vision: http://szeliski.org/Book
 - Hartley & Zisserman: Multiple View Geometry in Computer Vision (Math): http://www.robots.ox.ac.uk/~vgg/hzbook
 - Forsyth & Ponce: Computer Vision
 - Gonzalez & Woods: Digital Image Processing

Homework: Do it before the exercises, since the solutions are presented in the session. Both calculation and programming.

Required: Non-zero points from at least 8 weekly assignment rounds out of 12.

# Image Formation (Szeliski 2, Hartley & Zisserman 2, 3 and 6)

**tl;dr**

 - Vanishing points and vanishing lines - parallel lines in 3D intersect at a point under perspective transformation
 - All vashing points for perpendicular lines form a line.
 - Pinhole camera model and projection matrix: $x = K[R t]X$
 - Homogenous co-ordinates

## Geometric Transformation:
### Perspective Camera: 3D to 2D projection
Digital cameras use a perspective projection principle. If you put a piece of film in front of an object, do you get a reasonable image? Not really, since the light from the object would cover the entire film surface and everything.

Main idea: use a barrier to block some of the rays and "focus" light through one point. This is called
a "pinhole camera". Only light rays having a particular direction reach a particular point
in the surface. (Note that the image will be y-inveted).

  - Apeture
  - Centre of Projection
  - Optical Center
  - Focus Point
  - Camera Center

One of the most important features is that it forms a projection, eg a reduciton of dimensionality.

What properites are preserved in this process?
- Straightness of lines. If there are two lines that intersect, the intersection is preserved. Eg,
  we preserve linearities.
- However, angles and lengths are not preserved. When the Z-dimension is flattened we need to distort
  on the X and Y axis by some frustum in order give the illusion of depth.

A more mathematical approach:
- If you want to compute an image point corresponding to a scene point, draw a line from
  the object to the point on thei mage plane. All scene points that lie on this visual ray
  have the same projection in the image - you only see the one closest to the camera, unless
  transparent.
- Are there scene points for which the projection is undefined:
    - Anything on the backside (physically)
    - Light rays parallel to the image plane - they never intersect so there is no solution.
- We define a co-ordinate system for the camera.
    - Origin is the optical center
    - Image plane is perpendicular to the Z axis and parallel to XY axis.
    - Distance between the XY plane to `O` is `f`, the focal length.
    - Distance between `Py` and `O` is `z`, which is equal to `f`.
    - We have `P` and `P'`. The angle `Pyz` and `P'fy` is equal, though upside down.
    - The focal length defines the magnification ratio.
    - For projections of a pattern on a plane parallel to the image plane: they are at a fixed depth `z`.
    - Pattern gets scaled by ${f \over z}$ eg $(x, y, z)$ -> ($f {x \over z}$, f ${y \over z}$)
    - But what happens if the projected object is not parallel to the image plane (non-fronto-parallel planes).
      - Trickier, depents on your viewpoint.
      - For anything parallel to the image plane, length is not preserved. We cannot infer actual lengths from observedl lengths - you would have to know the distance from the camera.
      - Things that are closer to the camera can appear larger than things which are further away.
      - Angles are not preserved. Parallelism is not preserved either - everything intersects
        at the vanishing point.

### Vanishing points:
- Lines that are parallel in the real world may intersect in the image - if a human observes sees
  two parallel lines that are going away in the Z direction, they appear to intersect.
- We can construct the vanishing point. Place the image plane in front of the camera, so we
  still get a perspective projection (not upside down). When we move a point along the line further
  and further away from the camera, it appears to converge on a single point - eg, the *limit* of
  the projection function is the projection function. Take the projection of the line that has
  the same direction of the scene line, the vanishing point is the intersection of that line
  with the plane.

  All scene lines that have the same direction in a 3D world intersect at a single vashing point.

  A *vanishing line* is the line in the image where all planes that have the same direction
  intersect with the same line (eg, all horizontal lines in 3D converge towards a single line
  in the center of the image). Vertical lines that are parallel generally get a parallel projection
  so their vanishing line is at infinity.

- Modelling projection:
- Units for image points are in the same metric points.
- But on a digital camera we can't use analogue measurements - we only have pixels.
- Homogenous co-ordinates are pretty good at that.

### Contra-varaint and Co-variant Components of a Vector
Covariance and contravariance are properties of an operation.

If a value is changing *covariantly* with a second value, then it
means that as the second value increases, the value also increases
and vice versa.

If a value is changing *contravariantly* with a second value, then
it means that as the second value increases, the first value
decreases and vice versa.

Vectors can be described in two ways - through their contravariant
and covaraint components.

The default notation is the "contravaraint" notation.
The reason for this is because we describe a vector in terms
of the number times you would have to repeat each basis vector in order
to reach that point in space.
 - For instance, if we use the standard basis vectors $i = (1, 0, 0)^T$,
   $j = (0, 1, 0)^T$ and $k = (0, 0, 1)^T$ then describe a vector
   $v = (3, 2, 4)^T$ with it, we would say that it is $3i + 2j + 4k$.
 - However, if our basis vectors change to $i' = (2, 0, 0)^T$,
   $j' = (0, 2, 0)^T$ and $k' = (0, 0, 2)^T$ then we can describe
   the same point in space using the vector $1.5i + j + 2k$, eg
   the coefficients on $i$, $j$ and $k$ have halved.

Another way to describe a vector is taking its dot product
with each of the basis vectors such that the dot products form
the components. This is known as the contravariant notation.
 - In the case of scaling standard basis vectors, the values for
   the contravariant notation will necessarily be the same,
   because of the fact that we only add each individual component
   and the other components are set to 0.
 - However, consider nonstandard basis vectors $s = (1, 1, 0)^T$,
   $t = (0, 1, 1)$ and $p = (0, 0, 1)$. All three are linearly
   independent. We can describe the vector $v = (3, 2, 4)$ as
   $3s - t + 5p$ using the basis vectors. As the basis vectors
   shrink, the coefficients must increase, as above.
 - We can also describe the vect in terms of its dot products
   with each of the basis vectors:
   $\begin{bmatrix} (3 \times 1 + 2 \times 1 + 4 \times 0) \\ (3 \times 0 + 2 \times 1 + 4 \times 1) \\ (3 \times 0 + 2 \times 0 + 4 \times 1) \end{bmatrix} = \begin{bmatrix} 5 \\ 6 \\ 4 \end{bmatrix}$
 - As the basis vectors expand, so do the coefficients of the contravariant
   form. For instance, if our basis vectors were instead:
   $s = (2, 2, 0)^T$, $t = (0, 2, 2)$ and $p = (0, 0, 2)$ then
   we would have: $\begin{bmatrix} (3 \times 2 + 2 \times 2 + 4 \times 0) \\ (3 \times 0 + 2 \times 2 + 4 \times 2) \\ (3 \times 0 + 2 \times 0 + 4 \times 2) \end{bmatrix} = \begin{bmatrix} 10 \\ 12 \\ 8 \end{bmatrix}$.

### Homogenous Co-ordinates:
Homogenous co-ordinates can be used to represent both
points and lines within the same co-ordinate system.
Operations between vectors that are points and
vectors that are lines are well-defined.

Converting points to homogenous co-ordinates:
- $(x, y) => (x, y, 1)$
- $(x, y, z) => (x, y, z, 1)$

Converting lines to homogenous co-ordinates:
- $ax + by + c = 0 -> (a, b, c)$
- $ax + by + cz + d = 0 -> (a, b, c, d)$

Some common operations with homogenous co-ordinates:
  - Computing the *perpendicular distance* between a line and a point: $l \cdot p$.
  - Normalization of homogenous co-ordinates:
    - *Points*: $(x, y, z) -> (\frac{x}{z}, \frac{y}{z}, 1)$
    - *Lines*: $(a, b, c) -> (\frac{a}{\sqrt{a^2 + b^2}}, \frac{b}{\sqrt{a^2 + b^2}}, \frac{c}{\sqrt{a^2 + b^2}})$
  - Intersection between two lines $l_1 \times l_2$
    - The reason for this comes from the *perpendicular distance* formula above.
      Essentially, if a point is co-linear with a line, its
      perpendicular distance will be zero, so what
      we want is some point $p$ that is co-linear with
      both lines, eg both the following equations are satisfied:
      - $a_1p + b_1p + c_1 = 0$
      - $a_2p + b_2p + c_2p = 0$

      The easiest way to ensure this is with by equating both
      as though there was some point $p$ and then solving for
      $p$. Because the cross-product of a vector with itself
      is zero, we can just take the cross product and end up
      with $p$ as follows:
      - $l_1 \cdot p = l_2 \cdot p = 0$
      - $l_1 \times l_2 \cdot p = 0$
    - Example: Intersection of the lines $x = 1$ and $y = 1$.
      - $x = 1$ is equavilent to $1 - x = 0$ and has a homogenous
        representation $(-1, 0, 1)^T$.
      - $y = 1$ is equavilent to $1 - y = 0$ and has a homogenous
        representation $(0, -1, 1)^T$.
      - To take the intersection consider the cross product, which is
        $(1, 1, 1)$ in homogenous co-ordinates.
  - Line through two points: $p \times q$.
    - The reason for this result comes from the perpendicular distance
      fact above. The cross product finds a vector perpendicular to both
      points, such a vector is a line that joins both of them.

A point $x = (x, y)^T$ lies on the line $l = (a, b, c)^T$ iff $ax + by + c = 0$. You can write this as a linear product: $(x, y, 1) \times \begin{bmatrix}a \\ b \\ c \end{bmatrix} = 0$

All homogenous co-ordinates can be scaled by some scalar since they are invariant to scaling.
Converting back to cartesian co-ords just involves scaling by `w`.

$k(x, y, w)$ = $(kx, ky, kz)$ => (${kx \over kw}$, ${ky \over kw}$) = (${x \over w}$, ${y \over w}$)

Scaling:
  - *Points*: Scaling the homogenous co-ordinates by a non-zero scalar does not change the point you represent.
  - *Lines*: No need to scale the coefficients, just scale the last component.
  - *Infinity*: To represent infinity, you can use `0` as the last component.

The reason why we use them is that many calculations become much simpler. We can represent infinity
by setting the last element to 0, since converting back to cartesian would be a divison by zero.

### Projection Matrix:
- A four-vector matrix
- x = $K[R t]X$

  - x: Image co-ordinates (u, v, 1)
  - K: Intrinsic Matrix (3x3): Internal parameters of the camera (focus length, etc)
  - R: Rotation (3x3): Rotation and translation of camera with respect to world frame.
  - t: (Translation (3x1)
  - X: World co-ordinates $(X, Y, Z, 1)$

**Deriving this:**

- Intrinsic assumptions:
  - Using the same units on the image plane as the scene co-ordinate frame. Unit length in X and Y dimension is the same (1:1 aspect ratio)
  - Origin of the image co-ordinate frame is at (0, 0)
  - No skew (orthogonality)

- Extensic assumptions:
  - No rotation
  - Camera at (0, 0, 0)

So in reality: $K[I 0]X$

We can convert $(x, y, z)$ -> ($f {x \over z}$, f ${y \over z}$) to homogenous co-ords.

$w(u, v, 1)$ yields:

      f 0 0 0
      0 f 0 0  => K
      0 0 1 0

Now, lets get rid of one assumption, that the origin of the image co-ordinate frame is at $(0, 0)$

$w(u, v, 1)$ yields:

      f 0 u 0
      0 f v 0  => K
      0 0 1 0

Now, lets get rid of the 1:1 aspect ratio assumption:

$w(u, v, 1)$ yields:

      a 0 u 0
      0 b v 0  => K
      0 0 1 0

Now lets get rid of the no-skew assumption:

$w(u, v, 1)$ yields:

      a s u 0
      0 b v 0  => K
      0 0 1 0

- This kind of triangular matrix is denoted by `K`, upper triangular matrix.

Now for the **extrinsic** assumptions. In order to model that we need to add some sort
of model-view transformation.

Translation is done by adding a column vector (3x4).

    1 0 0 t_x
    0 1 0 t_y
    0 0 1 t_z

Vector `t` is from the camera to the point in the world.

Lots of ways to represent 3D rotation by rotating around each of the co-ordinate axes
in turn (counter clockwise). Just put a unit vector in each row then use `sin` and `cos` on
the other rows by the angles to rotate around each axis.

  $w(u, v, 1)$ yields:

                a s u 0    r_11 r_12 r_13 t_x
  x = K[R t]X  0 b v 0  x r_21 r_22 r_23 t_y
                0 0 1 0    r_31 r_32 r_33 t_z

  In practice we need to recover these parameters from the 2D scene.

  11 degrees of freedom. 2 degrees of freedom in the camera position, 9 in the modelview matrix.

  Looking at the vanishing point:

`- $p = K[R t] (x, y, z, 0)$
- (translation doesn't matter as it corresponds to zero)
- $p = KR(x, y, z)$
- $p - K(x_R, y_R, z_R)$

      f 0 u_0   x_R
      0 f v_0 x y_R
      0 0 1     z_R

$u = {fx_R \over z_R} + u_0$
$v = {fx_R \over z_R} + v_0$

If we have a 2D plane in the scene and we want to know the points on that plane project, the transformation matrix can be simplified. Just get rid of the `z` column vector.

  z_11 z_12 z_14
  z_21 z_22 z_24
  z_31 z_32 z_34

### Common Transformation Sets
#### Translation

$\begin{bmatrix} 1 && 0 && t_u \\ 0 && 1 && t_v \\ 0 && 0 && 1 \end{bmatrix}$

Two degrees of freedom in $xz$ and $yz$. Translation is applied to homogenous point vectors at scale of $z$ co-ordinate.

Concurrency, colinearity, order of contact, intersection,
tangency, inflections invariants. are preserved.

#### Euclidean

$\begin{bmatrix} r_{11} && r_{12} && t_u \\ r_{21} && r_{22} && t_v \\ 0 && 0 && 1 \end{bmatrix}$

Three degrees of freedom. Only three as opposed to six because
we can only change the rotation angle.

Length and area are preserved.

#### Similarity

$\begin{bmatrix} \alpha r_{11} && \alpha r_{12} && t_u \\ \alpha  r_{21} && \alpha r_{22} && t_v \\ 0 && 0 && 1 \end{bmatrix}$

Four degrees of freedom. We can change the rotation angle
and a single scale factor, $\alpha$.

The *ratio* of lengths and angle between points are preserved.

#### Affine

$\begin{bmatrix} a r_{11} && b r_{12} && t_u \\ c r_{21} && d r_{22} && t_v \\ 0 && 0 && 1 \end{bmatrix}$

Six degrees of freedom. We can skew on any axis, change
scale factors on any axis and translate.

Parallelism is preserved, as is the ratio of areas and
ratio of lengths on parallel lines (since all areas
are scaled by the determinant).

#### Projective

$\begin{bmatrix} h_{11} && h_{12} && h_{13} \\ h_{21} && h_{22} && h_{23} \\ h_{31} && h_{32} && 1 \end{bmatrix}$

Eight degrees of freedom. Only eight because in order for
homogenous co-ordinates to work, they must be scale invariant
and thus we can't independently change $h_{33}$.

Concurrency, colinearity, order of contact, intersection,
tangency and inflections are preserved.

The key invariant in projective transforms is the *cross-ratio*
invariant:
  - The value of the cross ratio is not depent on a particular
    homogenous representative of point $p$ since the scale cancels out the difference between the numerator and the denominator.
  - If each point $p$ is a fininte point and its homogenous
    representative is chosen such that $p_2 = 1$ then
    $|x_i x_j|$ represents the signed distance from $x_i$ to $x_j$.
  - Definition of the cross ratio is also valid if one of the points is an ideal point.
  - The value of the cross ratio is invariant under any projective transformation of the line.


## Radial Distortion:
- Very common if you have a wide angle lens in your digital camera - think of fisheye lens.
- Don't fit with the perspective projection model.
- We can compensate for this by modelling the lens distortion and compensating for the nonlinear part.

## Photometric image formation:
- See lecture slides

## Digital Cameras:

# Image Processing (3)
# Feature Detection & Matching (4)
# Feature Based Alignment & Image Stitching (6, 9)
# Dense Motion Estimation (8)
# Structure From Motion (7)
# Stereo & 3D Reconstruction (11, 12)
# Recognition (14)