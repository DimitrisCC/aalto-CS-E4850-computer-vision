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

### Perspective Camera: 3D to 2D projection
Digital cameras use a perspective projection principle. If you put a piece of film in front of an object, do you get a reasonable image? Not really, since the light from the object would cover the entire film surface and everything.

All cameras modelling a central projection are specializations
of the general projective camera. The geometric entities of the
camera can be computed from its matrix representation.

### Pinhole Camera Model
Main idea: use a barrier to block some of the rays and "focus" light through one point. This is called
a "pinhole camera". Only light rays having a particular direction reach a particular point
in the surface. (Note that the image will be y-inveted).

Parameters:
  - Apeture
  - **Centre of Projection** also known as the **Camera Center**
  - **Principal Axis**: Line frm the camera center perpendicular to the image plane
  - Optical Center
  - Focus Point

One of the most important features is that it forms a projection, eg a reduciton of dimensionality.

What properites are preserved in this process?
- Straightness of lines. If there are two lines that intersect, the intersection is preserved. Eg,
  we preserve linearities.
- However, angles and lengths are not preserved. When the Z-dimension is flattened we need to distort
  on the X and Y axis by some frustum in order give the illusion of depth.

A more mathematical approach:
- If you want to compute an image point corresponding to a scene point, draw a line from
  the object to the point on the image plane. All scene points that lie on this visual ray
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

#### Representation using homogeneous co-ordinates
If the world and image points are represented by homogeneous vectors, then
the central projection is a linear mapping between their homogeneous
co-ordinates that can be written in terms of matrix multiplication:

$K = \begin{pmatrix} f && && && 0 \\ && f && && 0 \\ && && 1 && 0\end{pmatrix} \cdot \begin{pmatrix} x \\ y \\ z \\ 1 \end{pmatrix}$

We can introduce the notation $\bold X$ for the world point represented by
the homogeneous vector $(x, y, z, 1)^T$ and $x$ for the projected point $(x, y, 1)^T$ and $P$ for the 3x4 homogeneous camera projection matrix. Then we have:

$x = P \bold X$

##### Principal point offset:
The above expression assumed that the origin of co-ordinates was at
the "principal point", eg, that there was no translation of the pinhole
itself on the viewing frame. In reality there may be some translation
in the viewing frame:

$(x, y, z)^T \implies (f\frac{X}{Z} + p_x, f\frac{Y}{Z} + p_y)$

We can represent that using homogenous co-ordinates by adding an x-for-z
and y-for-z translation in the projection matrix:

$K = \begin{pmatrix} f && && p_x && 0 \\ && f && p_y && 0 \\ && && 1 && 0\end{pmatrix} \cdot \begin{pmatrix} x \\ y \\ z \\ 1 \end{pmatrix}$

##### Camera Calibration Matrix
The matrix above is called the *camera calibration matrix*.
We give it the name "calibration" since it is merely
the configuration of the camera itself and has nothing to do with
the physical position of the camera in space. The *camera calibration matrix*
is denoted by $K$

##### Modelview Matrix applied to Camera
If we want to move the camera around in world space, we can apply a modelview matrix to it.

To compute this modelview matrix, we do so in relation to a point in
space, $\bar X$ and the location of the camera co-ordinate center, $\bar C$.
Then, if the rotation of the camera frame is given by $R$, we have
$X_{cam} = R \cdot (\bar X - \bar C)$, or in matrix form:

$X_{cam} = \begin{pmatrix} R && -R\bar C \\ 0 && 1 \end{pmatrix} \cdot \begin{pmatrix} X \\ Y \\ Z \\ 1) \end{pmatrix}$

And in shorthand form:

$x = KR[I | -\bar C]\bold x$

Usually we would represent the world-to-image transformation using a
translation offset as opposed to making the center of the pinhole
explicit, so in this case the camera matrix would be:

$P = K[R | \bold t]$

#### Parameterization and Degrees of Freedom
As explained above, there are in general nine degrees
of freedom for the pinhole camera model.

The elements $f$, $p_x$ and $p_y$ are called the *internal camera parameters*
represented by the *camera calibration matrix*.

The elements of $R$ and $\bar C$ which relate to camera orientation
and position to a world co-ordinate system are called the *external camera
parameters* or *external orientation*.

Thus, there are *9* degrees of freedom.

### CCD Cameras
The pinhole camera model assumes that the image co-ordinates are
euclidean having equal scales in both directions. It is entirely
possible that pixels could be non-square, which introduces an extra
scale factor in each direction.

Thus, CCD cameras have a *camera calibration matrix* of the form:

$K = \begin{pmatrix} \alpha_x && && p_x && 0 \\ && \alpha_y && p_y && 0 \\ && && 1 && 0\end{pmatrix} \cdot \begin{pmatrix} x \\ y \\ z \\ 1 \end{pmatrix}$

Where $(\alpha_x, \alpha_y) \in R^2$ are the scale factors in each
direction on the image plane.

This can again be represented in shorthand as:

$P = K[R | \bold t]$

Noting that $K$ has two extra degrees of freedom.

CCD camera's have *10* degrees of freedom (we gained on extra scale factor).

### Finite projective camera

In some cases, a camera might skew x-for-y, so we add a skew parameter. Usually
this is just zero.

$\begin{pmatrix} \alpha_x && s && p_x && 0 \\ && \alpha_y && p_y && 0 \\ && && 1 && 0\end{pmatrix} \cdot \begin{pmatrix} x \\ y \\ z \\ 1 \end{pmatrix}$

Shorthand this is:

$P = KR[I | -\bar C]$

A finite projective camera has *11* degrees of freedom.

#### Invertibility of projection matrix, implications for FPC

Note that since the left-hand 3x3 submatrix of $P = KR$ is non-singular,
any 3x4 matrix P for which the left hand submatrix is non-singular is
the camera matrix of some finite projective camera.

If we were to say that $M$ was the 3x3 upper left hand corner of
$P$ then one can decompose $M$ as $M = KR$, which means that $P$
canbe written as:

$P = M[I | M^{-1} \bold p_4] = KR[I | -\bar C]$ where $p_4$ is the last
column of $P$.

Thus, the set of camera matrices of finite projectve cameras is
identical with the set of homogenous 3x4 matrices for which the left hand
3x3 submtraix is non-singular.

### General projective cameras
Now we can remove the non-singularity restriction, so a
*general projective camera* is one represented by an arbitrary
homogeneous 3x4 matrix of rank 3. It *must* be rank 3 since
otherwise the matrix mpping will be a line or a point and not
the whole plane (not a 2D image).

### Anatomy of the general projective camera

#### Camera centre
$P$ has a 1-dimensional right null space. We can generate this null-space
with the expression $P \bold C = \bold 0$.

Consider a line from $\bold C$ to $\bold A$. You can represent this
line using a linear interpolation:

$X(\lambda) = \lambda \bold A + (1 - \lambda) \bold C$

Now, under a projective mapping $x = P \bold X$, points on the line
are projected as such:

$x = P \bold A(\lambda) + (1 - \lambda)P \bold C$

Now, since $P\bold C = 0$ as per our definition to solve for the
null-space above, we can remove the $(1 - \lambda)P \bold C$ and
are left with $x = \lambda P \bold A : \forall \lambda$.

All points on the line are mapped to the same image point, $P\bold A$,
meaning that the line must be a ray through the camera center. Thus, we
have a line that is collapsed into a point under the transformation
which is the null space.

Because of the fact that $C$ is the null-vector of $P$, any point in
the camera frame doesn't have a defined location in the world space
since we cannot divide by zero. Thus, the camera center is the point
at infinity.

#### Column Vectors of the projective camera
The columns of the projective camrea are 3-vectors which have
a geometric meaning as particular image points. The points
$p_1$, $p_2$ and $p_3$ are the *vanishing points of the world
co-ordinate X, Y and Z* respectively.

The column $p_4$ is the image of the world origin under
the transformation.


#### Row vectors of the projective camera
The row vectors are 4-vectors which can be seen as particular
world planes (denoted by $P^1$, $P^2$ and $P^3$).

#### Principal plane ($P^3$)
The principal plane is the plane facing the camera center
parallel to the image plane. This is the set of points
which are imaged on the line at infinity on the plane.

To think about this, imagine that we have some plane facing the
camera and parallel to the image plane at distance
infinity away. A point is only *on* that plane, if it is
also infinity distance away, eg $P^{3T} \bold X = 0$. Note
that points at the center of the camera are always infinity
distance away, since $P\bold C = \bold 0$ so $C$ always
lies on the principal plane.

#### Axis-aligned planes ($P^1$ and $P2$)
These planes extend parallel to the x and y axis of the
principal plane and are orthogonal to the principal
plane itself.

The set satisfies $\bold P^{1T}\bold X = 0$ and
$\bold P^{2T}\bold X = 0$.

$P^{1T}$ is imaged at $(0, y, w)^T$ and
$P^{2T}$ is imaged at $(x, 0, w)^T$.

$P^{1T}$ is defined by the line $x = 0$ and the camera
center. $P^{2T}$ $y = 0$ and the camera center.

$P^{1T}$ and $P^{2T}$ are dependent on the image $x$ and $y$
axes.

The line of intersection for both planes does not coincide
with the camera prinicpal axis.

The camera center lies at the intersection of all three planes,
which makes sense since the condition for the camera to lie
on all three lanes is $P\bold C = \bold 0$.

#### Principal point
The principal axis is the line passing through the camera
centre $\bold C$ with a direction perpendicular to the principal
plane ($P^3$). Thus, it is the *surface normal* of the principal
plane. If we recall that the plane equation may be given by
the parameterization $ax + by + cz + d= 0$, then the normal
to that plane is the vector $(a, b, c, 0)^T$ lying at infinity
on the plane. Thus, the normal to the plane is
$\hat P = (p_{31}, p_{32}, p_{33}, 0)^T$.

Projecting that point gives $P \hat P$, which is the
*principal point*.

#### Principal axis vector
In theory, any point $X$ not on the principal plane may be mapped
to an image point $\bold x = P\bold X$, but in reality, only points
in front of the camera frame are actually visible.

The vector $m_3$ (from the 3x3 portion $M$ of $P$) points in the
direction of the principal axis, but we don't know whether it
points towards the front of the camera, since $P$ is not defined
up to the sign.

Therefore, we need to consider co-ordinates with respect to the
camera co-ordinate frame.

Now, remember that the projection of a 3D point to a point in
the image is given by
$\bold x = P_{cam}\bold X_{cam} = K[I | \bold 0]X_{cam}$ where
$X_{cam}$ is the 3D point expressed in camera co-ordinates.

Now, if we take $\bold v = \det M \bold m^3$ then that vector
will always be pointing towrads the camera. The reason why is that
$\det R$ can never be negative.


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

- $p = K[R t] (x, y, z, 0)$
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

## Action of a projective camera on points
### Forward Projection
A general projective camera maps a point in space $x$
to an image point $\bold X = Px$. Points
$D = (\bold d, 0)^T$ lie on the plane at infinity and
are thus vanishing points, affected only by the first
3x3 submatrix of $P$ as the fourth component is 0.

### Back-projection of points to rays
Given some point $x$ in an image, can we compute the set
of points which map to that point? We can, if we cast a
ray in space passing through the camera centre.

The two points in question are $C$ and $P^{+} \bar x$ where
$P^{+}$ is the pseudo-inverse of $P$ ($P^{+} = P^T(PP^T)^{-1})$)
and $\bar x$ is a point on the camera frame.

The point $P^{+}x$ lies on the ray as it projects to
$x$ because $P(P^{+}x) = x$. So, we can specify
the ray as:

$\bold X(\lambda) = P^{+}\bold x + \lambda C$

Where $\lambda$ is a parameter defining the input space
to our line, which is the output space. All points on this
line are projected to $x$, meaning that the object in space
that the point on the plane refers to is the one
intersectin the line at the shortest distance.

#### Alternative form for finite cameras
If the camera is only *finite* we can use an alternative
expression as the camera centre is given by $\bar C = -M^{-1}\bar p_4$.
If we back-project the ray to intersect the plane at infinity
at point $D = ((M^{-1}x)^, 0)^T$, which is the second point
on the ray. So we can write this as a parameterized expression:

$X(\lambda) = \lambda \begin{pmatrix} M^{-1}x \\ 0\end{pmatrix} + \begin{pmatrix} -M^{-1}p_4 \\ 1 \end{pmatrix}$

As above, all points on the line parameterized by $\lambda$ project
to $x$ on the image plane, so we can use the same algorithm
to find the first intersecting object.

### Determining the depth of a point
Can we work out how far away a point is from the camera?

If we have a camera matrix $P = [M | p_4]$ projecting
some point $\bold X = (x, y, z, 1)^T$ to
$P\bold X = \bold x = w(x, y, 1)^T$ then we can work out $w$.

Let $\bold C$ be the camera centre. Now,
$P^{3T}(\bold X - \bold C) = m^{3T}(\bar \bold X - \bar \bold C)$,
which is the dot product of the ray from the camera centre to the
point $X$ with the same direction as the principal ray.

If we normalize the camera matrix such that $\det M > 0$ and $\bold m^3 = 1$
then $m^3$ is a unit vector pointing in the positive axis direction,
so we can interpret $w$ as the depth of the point $X$ from
the camera centre $C$.

Of course, instead of normalizing, we can just divide the result
by the magnitude of $\bold m^3$:

$depth(X; P) = \frac{sign(\det M)w}{T||\bold m^3||}$

### Decomposing the camera matrix
Say we have $P$, a camera matrix representing the general projective
camera. We know that this is a product: $P = KR[I | -\bar C]$, so
how can we find the individual components $K$ the internal
camera configuration, $R$ the camera external position and $C$ the
centre point of the camera.

We can use a technique called RQ-decomposition. This is the decomposition
into the product of an upper-triangular and orthogonal matrix. This
will be explained later.

The skew-factor, $s$, is only nonzero if the $x$ and $y$ axes are
not perpendicular to each other, for instance if a photograph is
re-photographed, or where the image plane is not orthogonal to
the principal ray.

Although a 3x4 matrix can always be decomposed to obtain $R$ and $K$,
the euclidean interpretations of those parameters are only meaning if
the image and space co-ordinates are in an appropriate frame, meaning
that a Euclidean frame is required for both image and 3-space. That said,
the interpretation of the null-vector of $P$ is always valid even if
both frames are projective.

## Cameras where the centre lies on the plane at infinity
The difference between these and finite cameras is that the upper-left
hand matrix $M$ is singular.

The essential differences between an infinite camera and a finite
camera are:
 - The parallel projection matrix $\begin{pmatrix} 1 && 0 && 0 && 0 \\ 0 && 1 && 0 && 0 \\ 0 && 0 && 0 && 1\end{pmatrix}$ (eg, zeroes in the 3rd column) replaces the canonical projection matrix $[I | 0]$ of a finite camera.
 - The calibration matrix $\begin{pmatrix} K_{2\times2} && \hat \bold 0 \\ \hat \bold 0^T && 1\end{pmatrix}$ replaces $K$ of a finite camera (eg, the only relevant parameters are in the 2x2 upper left hand corner submatrix, there is no parameterization on the third column or row).
 - The principal point is not defined (as it lies at infinity).

### Affine cameras
This is a camera matrix $P$ in which the last row is of the form
$(0, 0, 0, 1)$, because points at infinity are mapped to points
at infinity.

#### Orthographic projection
If we project along the z-axis, then there no pinhole projection - every
point in the camera frame just casts a straight line in the world. The only
thing that matters is the exterior location of the camera, $R$ and its
position $t$.

Orthographic cameras have only five degrees of freedom, the three parameters
ascribing the rotation matrix and two offset parameters.

#### Scaled orthographic projection
Similar to orthographic projection, but we have an isotropic scale factor
applied in the $x$ and $y$ output space:

$P = \begin{pmatrix} k && && \\ && k && \\ && && 1 \end{pmatrix} \begin{pmatrix} r^{1}_1, && r^{1}_2 && r^{1}_3 && t_1 \\ r^{2}_1, && r^{2}_2 && r^{2}_3 && t_2 \\ 0 && 0 && 0 && 1 \end{pmatrix}$

There are six degrees of freedom. We gained an extra one, the isotropic
scale factor.

#### Weak perspective projection
Similar to orthographic projection but we now have independent sacle factors
for the $x$ and $y$ axes.

$P = \begin{pmatrix} \alpha_1 && && \\ && \alpha_2 && \\ && && 1 \end{pmatrix} \begin{pmatrix} r^{1}_1, && r^{1}_2 && r^{1}_3 && t_1 \\ r^{2}_1, && r^{2}_2 && r^{2}_3 && t_2 \\ 0 && 0 && 0 && 1 \end{pmatrix}$

There are seven degrees of freedom. We gained an extra one in the form
of an additional scale factor.

#### Affine camera
Similar to weak perspective projection, but we have one additional skew
factor, $s$.

$P = \begin{pmatrix} \alpha_1 && s && \\ && \alpha_2 && \\ && && 1 \end{pmatrix} \begin{pmatrix} r^{1}_1, && r^{1}_2 && r^{1}_3 && t_1 \\ r^{2}_1, && r^{2}_2 && r^{2}_3 && t_2 \\ 0 && 0 && 0 && 1 \end{pmatrix}$

This has eight degrees of freedom, the where $M$ is now the top left
submatrix $M_{2\times3}$.

Projection under an affine camera is a linear mapping on inhomogeneous
co-ordinates composed with a translation:

$\begin{pmatrix} x \\ y \end{pmatrix} = \begin{bmatrix} m_{11} && m_{12} && m_{13} \\ m_{21} && m_{22} && m_{23} \end{bmatrix} \bold x + \begin{bmatrix} t_1 \\ t_2 \end{bmatrix}$

The point $(t_1, t_2)^T$ is the image of the world origin.

##### Properties of the affine camera

The plane at infinity in space is mapped to points at infinity in the image, so
the principal plane of the camera lies at infinity:
 - Any projective camera matrix for which the principal plane is at infinity is an affine camera matrix
 - Parallel world line are projected into parallel image lines, since they
   only intersect in the plane at infinity, hence the image lines are parallel.
 - The vector $\bold d$ satisfying $M_{2\times3}\bold d$ is the direction
   of the parallel projection and $(\bold d, 0)^T$ is the camera centre since
   $P_A\begin{pmatrix} \bold d \\ 0 \end{pmatrix} = \bold 0$.

## Radial Distortion:
- Very common if you have a wide angle lens in your digital camera - think of fisheye lens.
- Don't fit with the perspective projection model.
- We can compensate for this by modelling the lens distortion and compensating for the nonlinear part.

## Photometric image formation:
- See lecture slides

## Digital Cameras:

# Image Processing (3)
**tl;dr**:
 - Sometimes it makes sense to think of images and filtering in the frequency domain
 - Can be faster to filter using FFT for large images ($N \log N$ vs $N^2$)
 - Images are mostly smooth which is why compression works, not much high
   frequency content.
 - Remember to perform a low-pass before subsampling.

Three views of image filtering:
 - Spacial domain (mathematic operation on a grid of numbers): smoothing,
   sharpening, edge detection
 - Image filters in the frequency domain (modify the frequencies of images):
   hybrid images, sampling, resizing
 - Templates and image pyramids (applications of filtering):
   template matching, finding certain structures from images

## Image Filtering in the spacial domain
Compute some function $f$ of local neighbourhood at each position. A convolution
generally.

This is the basic building block in many more complicated systems, we do
some low-level image processing based on these operations, or extract
information from the images (edges, distinctive points, discontinuities
etc).

We can then feed this either to a template matcher or to a CNN.

Note that when the filter is placed on the boundary, the pixels outside
the boundary get some pre-defined value like 0.

### Denoising
How can we reduce noise in a photograph?

 - Solution 1: Moving average where the weights are lower for
               surrounding pixels. The weights are called
               a *filter kernel*. Note that the weights
               must sum to 1 as we are doing a sum. Pixels
               with high values that are outliers are smoothed
               into much lower values, but at the cost of blurring
               everything else in the image.
  $h[m, n] = \sum_{k, l} g[k, l] f[m + k, n + l]$
   - Lots of different weight configurations:
     - *box filter*: Constant weights regardless of whether
                     the weights are in the kernel. Really
                     blurs the image. The larger the kernel
                     dimensions, the blurrier the image.
     - *gaussian filter*: Weights distributed by gaussian distribution

### Example convolutional filters
What about the following filters:

    0 0 0
    0 1 0 -> Identity mapping, nothing changes since only
    0 0 0    center pixel has non-zero value

    0 0 0
    0 0 1 -> Shifts everything left by 1 pixel
    0 0 0

    1 0 -1
    2 0 -2 -> Detects right vertical edges. If there is
    1 0 -1    a large difference between a pixel and its
              right neighbour, this difference is highlighted

    1  2  1
    0  0  0  -> Detects horizontal edges by the same principle
    -1 -2 -1

### Properties of filter kernels
Linearity: $g(f_1) + g(f_2) = g(f_1 + f_2)$

Shift Invariant: $g(s(f)) = s(g(f))$ where $s$ is a shift function
 - Note that any linear shift-invariant operator can be
   represented as a convolution.

Commutative

Associative

Distributive over Addition

Scalar factors out

Identity: unit impulse


### Definition of Convolution
$(f * g)[m, n] = \sum f[m - k, n - l] g[k, l]$

In a convolution the kernel is "flipped" by convention.

### Filtering vs Convolution
In a filter you have you have a +, in convolution you have a -. Note
that in a filter you don't have flipping, in a convolution you do
have flipping.

This is good to remember since there are two functions in a lot
of image processing libraries `conv2` and `filter2`, the outputs
will differ because the former flips and the latter does not.

### Gaussian filters

$G_{\sigma} = \frac{1}{2\pi\sigma^2}e^{-\frac{(x^2 + y^2)}{2\sigma^2}}$

The weights decay exponentially but they alwasy stay positive.
The larger $\sigma$ is, the larger the kernel is, eg the larger
the area where the kernel has a positive and nonzero value.

Compared to the box filter, the box filter has artifacts like
a grid-effect.

#### Properties
Removes the "high frequency" components from the image (low-pass)
 - Makes the images more smooth

Convolution with itself is another Gaussian but with a standard deviation. So
we can smooth with a small-width kernel, repeat and get the same result as
a larger width kernel.

Convolving twice with a Gaussian kernel of width $\sigma$ is
the same as doing $\sigma \sqrt 2$

Gaussian kernels are also *separable*, you can factor it
into products of two 1D Gaussians (horizontal and vertical passes).

#### Separability of a Guassian filter

$G_{\sigma}(x, y) = \frac{1}{2\pi\sigma^2}e^{-\frac{x^2}{2\sigma^2}} \times \frac{1}{2\pi\sigma^2}e^{-\frac{y^2}{2\sigma^2}}$

This is useful in practice because $k * k$ requires $k^2$ operations per
pixel, but if we do it separately it only requires $2k$ operations for
separable kernels.

However, it creates a data dependency between the horizontally
and vertically blurred images, eg the horizontal filtering must
be complete before the vertical filtering can continue
(or vice versa).

$K = vh^T$ If you think of it as a column vector
and a row vector you can do the matrix multiplication
between the column and the row and recover the same
convolution matrix.

$\begin{pmatrix} a \\ b \\ c\end{pmatrix} \begin{pmatrix} x && y && z\end{pmatrix} = K$

#### Practical matters
What happens when you get to the edge? You need to extrapolate, some
methods are:
 - Treat every pixel outside the image as black (creates a border)
 - Wrap around (good for patterns)
 - Copy the edge value (good for images, but highlights
   bright areas around the edge of the image)
 - Reflect across the edge (good for images, but technically
                            inaccurate).
 - Overkill: Use a generative network to generate pixels
             on the edges then blur them.

What is the size of the output?
 - In some cases you just truncate the edges of the image where
   the filter would have to go outside the boundary.
 - In MATLAB you have `filter2(g, f, shape)`, where `shape` is:
   - `'full'`: sum of sizes and `f` and `g`.
   - `'same'`: output size is `f`.
   - `'valid'`: difference between `f` and `g`.


## Image filtering in the frequency domain
### Fourier Transform
Any univariate function can be rewritten as a sum
of sine and cosines of different frequencies (with
the restriction that the fourier series is not the
function at the bounardies of where the series
repeats).

The basic building block is:

$A\sin(\sigma x + \phi)$

Add enough of them and you get any signal that you want.

Generally speaking we do this for audio processing, but
we can think of other signals in the same way.

We can decompose a signal into all the frequencies
that make it up and then plot the amplitude of each
individual frequency term. Eg:

$g(t) = \sin(2\pi f t) + \frac{1}{3}\sin(2\pi (3f) t)$

We have:
 - $1f$
 - $\frac{1/3}3f$

Every bar in the histogram corresponds to a particular
sinusoid function which is composed with all the other
sinusoids to make up the signal.

If we eventually start adding sine and cosine terms
and take the limit, we have something like:

$\lim{k \to \infty} A\sum_{k = 1}^{\infty} \frac{1}{k} \sin (2\pi kt)$

The highest frequency that you can measure in a signal
depends on the *sampling resolution*. Higher sampling
resolution allows us to measure higher frequencies but
takes longer.

Note the dimensions of the fourier transform histogram. It is
the `number_of_frequencies x max_signal_amplitude`.

#### 2D Fourier transform
In this case, we have the Fourier transform in the 2D case.

Imagine that we have a sine wave going from white to black
in a 2D image where each pixel is defined by
some function $f(x, y)$ where $f(x, y)$ is a sum of
weighted sines and cosines.

2D signals can be composed by adding together their
generated result over some domain.

A fourier base teases away fast and slow cahnges in
the image. High frequencies represent very fast
changes, whereas low frequencies represent a gradual
change.

The fourier *image* is like a 2D histogram. It captures
both the orientation and intensity of the wave
(usually this is represented with a different color,
or with depth).

Its worth noting that fourier transforms always
add an extra dimension, eg, a 1D signal gets
represented as 2D histogram, a 2D signal gets
represented as a 3D histogram.

The high frequencies are dispersed around the
edges of the fourier basis image and the
low frequencies congregate around the centre. Colour
measures amplitude.

Note the dimensions of the 2D fourier transform histogram. It is
the `number_of_frequencies x number_of_frequencies x max_signal_amplitude`.

#### Mathematical definition

The fourier transform $\bar F$ takes some
function and returns another function in terms of
frequency.

$\bar F(f(x)) = F(\phi)$

For every $\phi$ from 0 to infinty, $F(\phi)$ holds
both the amplitude and phase of the coresponding
sine:

$Asin(\phi x + \mu)$

This is through a complex number trick:

$F(\phi) = R(\phi) + iI(\phi)$

Thus, $A = +- \sqrt{R(\phi)^2 + I(\phi)^2}$ and $\mu = \tan^{-1} \frac{I(\phi)}{R(\phi)}$

It stores the magnittude and phase at each frequency.

The magnitude encodes how much signal there is at a particular
frequency and the phase encodes the spacial information
indirectly.

If we want to compute the fourier transform, take the
integral of $h(x)e^{j\phi x} dx$. Of course, we only
take this to a certain extent $N$ since we can't integrate
continuously. Use a Riemann sum:

$H(k) = \frac{1}{N} \sum_{x = 0}^{N - 1} h(x)e^{-j \frac{2\pi kx}{N}}$

In the 2D space it extends fairly easily, we just have two
sums:

$y(u, v) = \frac{1}{MN} \sum_{m = 0}^{M - 1}\sum_{n = 0}{N - 1} x(m, n) e^{-j \frac{2\pi um}{M}} e^{-j \frac{2\pi vn}{N}}$

#### Convolution theorem

The fourier transform of the convolution of two
functions is the product of their Foruier transforms:

$F[g * h] = F[g]F[h]$

The inverse Fourier transform of the product of two
fourier transforms is the convolution of the two
inverser Fouier transforms:

$F^{-1}[gh] = F^{-1}[g] * F^{-1}[h]$

Thus, a *convolution* in the spacial domain is
equivalent to *multiplication* in the frequency domain.

Depending on which domain we are in, we can use either
convolution or take a the fourier transform and multiply
which might be simpler.

#### Properties of Fourier Transforms
Linear: $\bar F[ax(t) + by(t)] = a\bar F[x(t)] + b \bar F[y(t)]$

Fourier transform of a real signal is symmetric about the origin

The energy of the signal is the same as the energy of its
Fourier transform.

### Finding equivalent filters in both the spacial and frequency domain
#### Why is it that some filters produce artifacts and not others ?
Eg: consider the following convolution:

    1 0 -1
    2 0 -2
    1 0 -1

We can take the fourier transform, taking as many frequencies
as we have pixels.

Then we multiply the frequencies by the fourier transform of the convolution,
then take the inverse fourier transform. For instance, a Gaussian kernel, like:

    a a a
    a c a
    a a a

Then we take the 2D fourier transform and we notice that there is a low
frequency of high amplitude in the center, so multiplying that through
the frequency domain removes all the high frequency components
of the image, so when takeing the inverse FFT we get a blurred image.

Consider the box filter:

    1 1 1
    1 1 1
    1 1 1

When we take the fourier transform of this convolution, we
still have a lot of high frequency components. So multiplying
preserves those high frequencies, causing the artifacts
to remain.

**Subsampling resolution for FT**: What subsampling should we use
for the fourier transform when converting both the filter and the
image to the frequency domain? For the image the frequency must at
least be the number of pixels in the image. In order
for the multiplication of the two domains to be defined, we must also
sample the same number of frequencies in the filter.

#### To what extent will information be preserved in an image when we resize it?
For instance, if we subsample by throwing away every other row and column, we
get *aliasing* because the sampling density limits the space of frequencies
that we can represent.

If we have higher frequencies in the original signal you start to see
artifacts due to information loss. Examples:
 - Temporal aliasing: If the video frame rate is not fast enough you cannot
                      capture high frequency motion, which makes wheels
                      appear to spin the wrong way or stay constant
 - Disintegration of checkerboards: This is a high-frequency texture
                                    that we cannot represent.

##### Nyquist-Shannon Sampling Theorem
When sampling a signal at discrete intervals, the sampling frequency
must be $\ge 2 \times f_{max}$ where $f_{max}$ is the max frequency
of the input signal. So in order to perfectly reconstruct the image
without information loss, then you need to measure the highest frequency
in the image and sample at least two times that frequency.

##### Anti-aliasing
What if we don't want the sampling frequency to be that high?

We can get rid of all the frequencies that are greater than
half of the new sampling frequency.

Start with the image, apply a low-pass filter, eg, by blurring
it: `imfilter(image, fspecial('gaussian', 7, 1)))`.

*Then*, sample every other pixel.

### Hybrid images
If we have two input images and we take the low frequencies from
one image and high frequencies from the other we see this kind of
mixed image. If you look at it from a close distance you see
the high-frequency content, but if you look at it from a far
distance you see the low frequency image.

Why is it that the image you see depends on the viewing distance?

It seems that perceptual cues in the mid-high frequencies dominate
perception. When we see an image from far away we are effectively
subsampling it.

## Template matching

## Image pyramids

# Feature Detection & Matching (4)
## Problem definition
We need low-level features (corners, edges, texture patches) for many tasks:
 - Image matching and registration
 - Structure-from-motion
 - Image segmentation
 - Object recognition
 - Imamge retrieval

## Edge detection
Identify sudden changes in an image, since most semantic and shape
information can be encoded in the edges.

Edges are caused by a variety of factors including:
 -  surface normal discontinuity
 -  depth discontinuity
 -  surface color discontinuity
 -  illumination discontinuity

We can represent an edge across by taking the cross-section
of its intensity value across some horizontal line. It
will be represented by a "U" shape in a graph and the first
derivative will have extrema on the turning points of the "U".

### Derivatives with convolutions
For a 2D function $f(x, y)$ the partial derivative is:

$\frac{\partial f(x, y)}{\partial x} = \lim_{\epsilon \to 0} = \frac{f(x + \epsilon, y) - f(x, y)}{\epsilon}$

Because we have a discrete signal we can approximate by using $\epsilon = 1$,
eg 1 pixel

$\frac{\partial f(x, y)}{\partial x}} = \lim_{1 \to 0} = f(x + 1, y) - f(x, y)$

How do we do this as a convolution?

This is a pretty simple filter, you just have a $1$, $-1$ filter:

    1 0 -1                 1  1  1
    1 0 -1 = respect to x, 0  0  0
    1 0 -1                -1 -1 -1

If we compute the derivative with respect to $x$ and $y$, the derivative
with respect to $y$ is the horizontal edge detector, the derivative
with repsect to $x$ is the vertical edges.

#### Derivative theorem of convolution
Note that if instead of taking first the convoluton then computing
the derivative using a discrete derivative filter, we can instead
compute the derivative of the gaussian function the convolve with that,
eg:

$\frac{\partial}{\partial x} (f * g) = f * (\frac {\partial}{\partial x} g)$

This is because both the Gaussian convolution and the finite
derivative are computed using a convolution.

### Finite difference filters
Instead of taking the difference with respect to the neighbour
we can approximate with different weights:

Sobel:

          -1 0 1      1  2  1
    My_x  -2 0 2 M_y  0  0  0
          -1 0 1     -1 -2 -1


Roberts:

    M_x 0 1  M_y 1  0
       -1 0      0 -1

### Image gradients
The gradient vector of an image is:

$\triangledown f = \begin{bmatrix} \frac{\partial f}{\partial x} && \frac{\partial f}{\partial y} \end{bmatrix}$

The gradient vector tells you the *direction* where the intensity is increasing
or decreasing.

The direction of the gradient vector is $\tan^{-1} \frac{\frac{\partial f}{\partial x}}{frac{\partial f}{\partial y}}$

The *edge strength* is given by $||\triangledown f||$

### Gradient-domain image editing
The gradients of the target region and source region should match
to avoid discontinuity between image regions.

### Controlling for noise
If we consider a single role or column of the image, there is going to
be a lot of *noise*, which menas that the derivative will be changing
quite rapdily.

If we compute the gradient using one-pixel radients the noise will dominate
the gradient-domain.

We need to smooth the image first to get rid of the high frequency noise,
but would not smooth large edges out (the true edges remain).

We take a Gaussian kernel with a standard deviation of 50px and convolve
it with the image the noise will be smoothed out but the edge
gradient wil remain. If we take the derivative of the convolved
image we get a much cleaner gradient and we can find a single set
of peaks there, eg $\frac{\partial}{\partial x}(f * g)$.

Computing the gradient in this manner is always a good idea
because we can recognize edges that have different scales or
different sharpness.

### Derivative of Gaussian filter
Remember that the 2D Gaussian can be expressed as
the product of two functions, one for $x$ and one for $y$. So
the $\frac{\partial}{\partial x} G \times \frac{\partial}{\partial y} G = G'$.

The partial derivative of the Gaussian kernel in the $x$
direction finds vertical edges.

The partial derivative of the Gaussian kernel in the $y$
direction finds horizontal edges.

### Scale of Gaussian filter
The standard deviation term $\sigma$ affects the kinds of
edges that we detect - the more smoothing we apply,
the more that we detect edges that occurr over several pixels.

With $\sigma = 1$ we only detect sudden changes but not larger edges. Of
course, the more smoothing we apply the more we start to blur edges.

### Smoothing vs Derivative Filters
Smoothing filters:
 - Gaussian: Removes "high frequency" components (low-pass filter)
 - Can the values of a smoothing filter be negative: The values cannot be negative
 - The values should sum to 1

Derivative filters:
 - Derivative of Gaussian filters
 - Can the values be negative: Yes, they can be negative
 - The value should sum to 0.

## Thresholding
How do we get a binary image where the pixels are
white only on edges? If we just take the norm of the
gradient and then threshold it we have a trade off:

 - Threshold is too: We have not very sharp edges if the or
 - Threshold is too high: We lose fine edges if the

Usually it is better to turn edges into curves.

### Non-maximum suppression
Typically the gradient magnitude is quite large around
several pixels close to the edge. We can suppress other
strong gradients by taking the local maxima of the gradient.

Compute the gradient along all the pixels and check if
the pixel is a local maximum along the gradient direction,
select a single max across the width of the edge.

Need to interpolate the neighbouring values by interpolating
from the neighbouring pixels.

This way, we get rid of the "smoothness" but we keep edges
where there there are weaker gradients.

### Hysteresis Theresholding
First detect the edge pixels, then accept the edge pixels that
are local maxima in the the gradient direction if they are connected
to edge pixels with a higher threshold. We apply the low threshold
only in the vicintiy of edge pixels with a detected high threshold.

### Canny edge detector
This is called the "Canny edge detector".
 1. Compute $x$ and $y$ gradient of images
 2. Find magnittude and orientation of gradient
 3. Apply non-maximum suppression in the direction of the gradient
 4. Define two thresholds, low and high
 5. Use the high threshold to start edge curves and then low
    threshold to continue them.

MATLAB: `edge(image, 'canny')`.

Better these days is human-driven edge detection that is
learnt as some high-dimensional function.

## Keypoint extraction
### Characteristics of good keypoints
 - Repeatability: The same keypoint can be found in several
                  images despite geometric and photometric
                  transformations. It should not be too sensitive
                  to minor changes that can happen because of different
                  lighting conditions or viewpoints.
 - Saliency: Each keypoint is distinctive, it should be possible
             to distinguish between the two.
 - Compactness and efficiency: Many fewer keypoints than image pixels.
                               This saves computation time and storage space.
 - Locality; A keypoint occupies a relatively small area of the image,
             making it robust to clutter and occlusion. For instance, you
             should have to see the whole mountain in order to detect
             parts of it.

### Corner detection
The basic idea is that we should be able to easily recognize
the point by looking through a small window. Shifting the window
in any direction should give a *large change* in intensity.

For instance, if there is no change in gradient, then the region is *flat*.

If there is a change in one direction we can check if there is a change
in other directions. If there is no change in other directions then
we have an edge. Otherwise we have a corner.

#### Mathematical operators
$E(u, v) = \sum_{(x, y) \in W) [I(x + u, y + v) - I(x,y)]^2$

The $u, v$ indicates the shift. We are taking the squared intensity
difference between the pixel shifted by $u, v$ and the original
nonshifted pixels. If we evaluate the sum of the function for different
shifts within some domain $W$ then we are computing the sum of the
squared differences over some shift region.

How does this behave for small shifts? Take the first order
Talor approximation.

$I(x + u, y + v) = I(x, y) + I_x u + I_y v$, eg:

The $I_x$ indicates the partial derivative of I with respect
to x.

If we use this Taylor approximation in the formula in $E(u, v)$:

$E(u, v) = \sum_{(x, y) \in W} [I_x u + I_y v]^2$, eg we have
a function that depends on the partial derivatives of the image
and the shift.

Quadratic approximation can be written as:

$E(u, v) = [u v] M \begin{bmatrix} u \\ v \end{bmatrix}$

where $M$ is a second moment matrix computed from
image derivatives summed over the window $W$:

$M = \begin{bmatrix} \sum_{x, y} I^2_x && \sum_{x, y} I_x I_y \\ \sum_{x, y} I_x I_y && \sum_{x, y} I^2_y\end{bmatrix}$

Since the quadratic form is the equation of an ellipse, we can determine
the axis lengths by performing an eigendecomposition - the axis lengths
are the eigenvalues and the orientation is
determined by $R$ where $M = R^{-1} [\lambda] R$.

The largest eigenvalue indicates the magnitude of the fastest change,
the smallest eigenvalue indicatest the magnitude of the smallest change.

In the axis-aligned case, We want $\sum_{x, y} I^2_x$ and $\sum_{x, y} I^2_y$
to both be large, meaning that the eigenvalues should both be large.
Edges are where one eigenvalue is much closer to the other.
Where eigenvalues are both small there is not much of a change in gradient. In
the non-axis aligned case perform an eigendecomposition then look at the
magnitude of the eigenvalues.

So corners are given by large "circular" ellipses (both eigenvalues
are within a close range), but long or tall ellipses only indicatee
edges.

#### Harris Corner Detection
Instead of computing the eigenvalues directly, we can also encode this as a
kind of "corner response function":

$R = \det M - \alpha \text{trace} (M)^2 = \lambda_1\lambda_2 - \alpha(\lambda_1 + \lambda_2)^2$

Where $R > \alpha$ we have a corner ($\alpha$ is a threshold).
Where $|R|$ is small, then the region is
flag. Edges are given by $R < 0$.

$M = \begin{bmatrix} \sum_{x, y} w(x, y) I^2_x && \sum_{x, y} w(x, y) I_x I_y \\ \sum_{x, y} w(x, y) I_x I_y && \sum_{x, y} w(x, y) I^2_y\end{bmatrix}$

Then, we compute the corner response function of the second moment matrix and
apply some thresholding to find the local maximum of the response function.

Antother method instead of using a sharp box is to use a Gaussian window, where
the terms are weighted according to the Gaussian function.

To detect the actual corners just take the local maxima of $R$.

Summary
 - Compute partial derivatives at each pixel
 - Compute second moment matrix $M$ in a Gaussian window around each pixel
 - Compute corner response function $R$
 - Threshold $R$
 - Find local maxima of response function

#### Robustness of corner features
##### Affine intensity
Since the derivatives are used, it is invariant to an intensity shift,
eg $I = I + b$.

However, it is not invariant to intensity *scaling* $I = \alpha I$b
because it will cause certain things to fail the thresholding step.

Therefore it is *partially invariant* to affine intensity changes.

##### Translation
Derivatives and window funcitons are shift-invariant.

The corner locations are *covariant* to translation, they shift
in the same manner.

##### Rotation
Corner locations are *covariant* with rotation. The ellipse indicating
the direction of the corners will rotate but the eigenvalues remain
the same.

##### Scaling
If we scale the image we might start detecting *multiple* corners, so
all points will be classified as edges! Therefore if we try to match
images of different scales we would fail.

See the following [paper](https://www.cs.ubc.ca/~lowe/papers/ijcv04.pdf) by David Lowe.

## Scale-Invariant Feature Transforms
SIFT is "scale invariant", fixing the last issue with using Harriss
corner detection

THe key-idea is the kind of scale-selection. If we have two pictures
of the same scene we can detect a characteristic scale for each local
image region, so that scale is detected covariantly with the image.

### Basic idea
Convolve the image with a blob filter at multiple scale and look for
extrema of the filter response in the resulting scale space. A blob
filter is not a Gaussian filter, it has positive and negative values,
eg:

    1  0  1
    0 -1  0
    1  0  1

Maxima will be the bright regions and minima will be the dark regions.

We have different scales for each filter (eg, a 3x3 filter, a 5x5 filter, etc),
so we have minima and maxima for each scale.

Mathematically it is a Laplacian of Gaussian functions, eg:

$\triangledown^2 g = \frac{\partial^2 g}{\partial x^2} + \frac{\partial^2 g}{\partial y^2}$

Eg, second order partial derivatives of the Gaussian summed together.

The second order partial derivative is negative towards the middle
and positive towards the edges. If we filter the image with that
we have positive and negative values on both sides of the zero.

### From Edges to Blobs
A single edge gives a non-differentiable threshold.  When we convolve it
with the Laplacian we get a strong negative response if the edge has
a scale that is compatible with the scale of the filter.

### How do we take into account the scale?
We wan tto find the characteristic scale of the blob by
convolving it with Laplacians at several scales and looking
for the maximum response. However, the Laplacian response
decays as scale increases.

The idea is that we need to compensate for the scale, so
in order to keep the response the same for a step edge, we
must multiply the filter with $\sigma^2$ because the Laplacian
is the second Gaussian derivative.

Then we just take the local maxima or minima across all
the scale-normalized laplacian responses.

So the actual operator is:

$\triangledown^2_{norm} g = \sigma^0 (\frac{\partial^2 g}{\partial x^2} + \frac{\partial^2 g}{\partial y^2})$

### Detecting blobs in 2D
At what scale does the Laplacian achieve a maximum response
to a binary circle of radius $r$. When the radius of the circular
blob in the image matches the *shape* of the Laplacian, so
the maximum response must occurr at $\sigma \sqrt 2 = r$.

So when we apply the filter for different values of
$\sigma$ we convert the $\sigma$ into pixel units corresponding
to the size of the detected blob.

The stack of filtered images can be considered the "scale space"

### Summary of scale-space blob detectors
1. Convolve the image with scale-nomalized Laplacian at several scales
2. Find the *maxima* of squared Lapalcian response
   (detects maxima and minima) in scale space
3. Each detected local maxima corresponds to a circle in the image, the scale
   indicating the size of the circle.
4. Since this gives quite strong responses around edges, so we can postprocess
   by filtering on the Harris response function over the neighbourhoods
   containing the blobs. If both of the eigenvalues for the Harris response
   function are not strong then we filter them out.

### Efficient implementaton
We can approximate the Laplacian with a difference of Gaussians. We have two
Gaussian kernels with differing sigmas. If we take the difference of the
two filter kernels we get an approximation for the Laplacian.

How do we select the value for $k$?

### Describing the features
The scaled and rotated versions of the same neighbourhood will give
rise to blobs that are related by the same transformation.

How do we compare the appearnace of these image regions?
 - Normalize: transform these regions into same-size circles
 - Problem: Dealing with rotational ambiguity.

#### Eliminating rotational ambiguity
To assign a unique orientation to circular image windows,
create a histogram of local image graident directions in
the patch, and assign a canonical orientation at peak of
the smoothed histogram.

We detect the peak that corresponds to a certain angle of
the gradient and we assign it as the "characteristic directon"
of the region. Then we can rotate the detected bounding boxes.

The *detection* is *covariant* to image transformations:

`features(transform(image)) = transform(features(images))`

The *description* is *invariant*:

`features(transform(image)) = features(image)`

Once we have the geometrically normalized patches we compute
the gradients inside the patches - put these into a 128
dimensional vector so that this kind of descritor vector
for every local image region, and by comparing these
descriptor vectors we can match parts of images.

The general gist of detecting such image features is to:
 - **Scale-space extrema detection**: Search over all the scales and image locations, using a difference-of-Gaussian function to identify potential interest points that are invariant to scale and orientation.
 - **Keypoint localization**: Determine location and scale of keypoint.
 - **Orientation assignment**: One or more orientations are assigned to each ekypoint location based on local image gradient directions.
 - **Keypoint descriptor**: Local image gradients are measured at the selected scale in the region around each keypoint.

This approach generates large numbers of features
that densely cover the image over the full range of
scales and locations. For instance a 500x500 image
will give rise to about 2000 stable features.

### History of approaches
Moravec (1981) - stereo matching using a corner detctor, useful for
any image that has large gradients at a predetermined scale.

Schmid and Mohr (1997) - invariant local feature matching could
be extended to general image recognition problems
in which a feature was matched against a large database of images.

The Harris corner detector is very sensitive to changes
in image scale and do doesn't provide a good basis
for matching images of different sizes.

The main problem is finding representations that
are stable under scale change or better yet, are affine
invariant.

Carneir and Jepson (2002) describe phase-based local
features that represent the phase rather than the
magnitude of local spacial frequences, providing
invariance to illumination.

Schiele and Crowley (200) proposed the use of multidimensional
histograms summarizing the distribution of measurements
within image regions.

### Scale-space extrema detection

The only possible scale-space kernel is the Gaussian
function. The scale space of an image is defined as
a funtion $L(x, y, \sigma)$ that is produced from
the convolution of a variable-scale Gaussian with
an input image:

$L(x, y, \sigma) = G(x, y, \sigma) * I(x, y)$,

where $G(x, y, \sigma) = \frac{1}{2\pi\sigma^2}\exp (\frac{-(x^2 + y^2)}{2\sigma^2})$.

So to effieciently detect stable keypoint locations in
scale-space, Lowe proposes using scale-space extreme
in the difference-of-Gaussian functions convolved
with the image:

$D(x, y, \sigma) = (G(x, y, k\sigma) - G(x, y, \sigma)) * I(x, y)$, i.e

$D(x, y, \sigma) = L(x, y, \sigma) - L(x, y, k\sigma)$.

We choose this function because it is efficeint to compute
since we need to compute $L$ anwyay for scale space
feature description, so in the overall algorithm
it is just a matter of subtracting the two images.

In addition, the difference-of-Gaussians is a
pretty good approximator for the scale-normalized Laplacian
of Gaussian ($\sigma^2 \triangledown G$).

#### Detecting local extrema
In order to detect the local maxima and minima of $D(x, y, \sigma)$,
each sample point is compared with its eight neighbours in the
current image and nine neighbours in the scale above
and below.

The sample point is only selected if it is larger than
all of those neighbours or smaller than all of them.

Since extrema can arbitrarily close together there
is no minimum spacing of samples that will detect all
extrema. So we must settle for a solution that trades
efficiency for completenes.

#### Frequency of sampling in the spacial domain
If we pre-smooth the image before extrema detection
then we are effectively discarding the highest spacial
frequencies, so to make full use of the input the image
can be expanded to create more sample points than
were present in the original. This is done by
doubling the size of the input image using linear interpolation
prior to building the first level of the pyramid. This
doubles the number of stable keypoints found in an image but
doesn't improve feature detection overall.

### Keypoint localization
Once a keypoint candidate has been found by comparing
a pixel to its neighbours, we need to perform a detailed fit
to nearby data for location, scale and ratio of
principal curvatures. This allows us to reject points
with low contrast (sensitive to noise).

Brown approach: Use a 3D quadratic function
to the local sample points to determine the interpoalted
location of the maximum using the Taylor expansion
of the scale-space function:

$D(\bold x) = D + \frac{\partial D^T}{\partial \bold x} + \frac{1}{2}\bold x^T \frac{\partial^2 D}{\partial \bold x^2} \bold x$

Where $D$ and its derivatives are evaluated at the sample point
and $\bold x = (x, y, \sigma)^T$, which is the offset
from the point.

The Hessian and derivative of D are approximated by using the
differences of neighbouring sample points.

The function value at the extremum is useful for rejecting
unstable extrema with low constrast.

#### Eliminating edge responses.

It is not sufficient to reject keypoints with low
contrast, because the difference-of-Gaussians function
will have a strong response along the edges
even if the location along the edge is poorly determined.

A poorly defined peak in the difference-of-Gaussians will have
a large principal curvature given by the Hessian
matrix:

$H = \begin{bmatrix} D_{xx} && D_{xy} \\ D_{yx} && D_{yy} \end{bmatrix}$.

The eigenvalues of $H$ are proportional to the principal
curvatures of $D$.

Because $\trace (H) = \lambda_1 + \lambda_2$ and $\det (H) = \lambda_1 + \lambda_2$,
we can determine if the ratio of principale curvatures
is below some threshold:

$\frac{\trace (H)^2}{\det (H)^2} < \frac{(r + 1)^2}{r}

If the determinant of the Hessian is negative, then the
curvatures have different signs so the point is discarded
as not being an extremum.

### Assignment of Orientation
If we assign a consistent orientation to each keypoint based on
local image properties, the keypoint descriptor can be
represented relative to this orierntation and therefore
achieve invariance to image rotation.

The scale of the keypoint is used to select the Gaussian-smoothed-image
$L$ with the cloest scale so that all computations are performed in
a scale-invariant manner. For each image sample $L(x, y)$ at
this scale, the gradient magnitude $m(x, y)$ and orientation
$\theta(x, y)$ is precomputed using the pixel difference:

$m(x, y) = \sqrt{(L(x + 1, y) - L(x -2, y)^2) + (L(x, y + 1) - L(x, y - 1)^2}$

$\tan \theta = \frac{(L(x + 1, y) - L(x -2, y))}{(L(x, y + 1) - L(x, y - 1)}$

This forms an orientation histogram from the graident orientations of
sample points within the region around a keypoint.

### Local image descriptor
How do we assign a location, scale and orientation to each
key point?

One appriach is to sample the local imave intensities around
the key-poiint at the appropriate scale and match them to
a database of features using a normalized correlation
meausre, but this is highly sensitive to changes that
cause mis-registration of samples, such as affine or
3D viewpoint changes.

A better way to do it is to find gradients at particular
orientations and special frequencies, matching them to
gradients at a frequency that are allowed to shift
over a range of locations within the image plane.

Sample the gradient magnitudes and orientations around
the keypoint location, using the scale of the keypoint
to determine the level of Gaussian blur for the image.

Use a Gaussian weighting function with $\sigma$ equal to one
half of the width of the descriptor window to
assign a weight and magnitude to each point (eg, each key-point
gets a circle on top of it where we summarize the contents of
that circle). Therefore, a gradient sample window can shift
up to four sample positions whilst still contributing to the
summary of a key point.

The feature vector for each key point is modified to reduce the
effects of illumination change:
 - Normalize it to unit length: Adding a constant brightness term
   to all the pixels in the region won't affect the normalized
   vector, therefore it is invariant to affine changes in illunication.
 - Gradient reduction: Threshold the values in the unit feature vector to
   be no larger than 0.2. This helps to reduce the impact of large gradients
   when considering non-linear illumination changes.

### Testing the complexity of a descriptor
Two parameters can be used to vary the complexity of a descriptor:
 - Number of orientations, %r$.
 - The width $n$ of the $n \ times n$ array of orientation histograms.

We generally find a 4x4 array of histograms with 8 orientations
(resulting in a feature vector with 128 dimensions) works
best - anything more and you start to overfit, hurting matching.

### Sensitivity to affine changes
The best approach available in this paper was the Mikolajczyk approach
which had lower overall repeatability of keypoints across angle changes
but was able to retain a 40% repeatability rate in a 70 degree angle
change, unlike other approaches which tapered off more quickly. The
trade-off was higher computational cost and a reduced number of matched
keypoints overall.

## How can we use this for object recognition?
Match each keypoint independently to the database
of keypoints extracted from the training images.

The best candidate match is found by identifying the nearest
neighbour in the database of keypoints for training images (eg
minimal euclidean distance in vector space).

The metric of how many features we matched might not be that
useful though, since many features can arise from background
cluter, so we should discard features that do not
have any good match in the database by imposing a threshold
on the distance that the feature can be to its nearest neighbour.

### Hough transform
How can we recognize or highly occluded objects? The distance
ratio test described above does not remove
matches from other valid objects.

We can use something called the Hough transform to cluster features
in pose space - each feature votes for all object poses that are
consistent with the feature. Each keypoint specifies
4 parameters - a 2D location, scale and orientation. Each matched
keypoint has a similar record relative to the training
image. Clusters with at least three entries are binned together.

### Affine parameters
If we imagine placing a sphere around the object, then rotation of the
sphere by 30 degrees will move no point within the sphere
more than 0.25 times the projected diameter of the sphere.

We wish to solve for the transformation parameters, so
we can rewrite the affine transformation of a model point
to an image point:

$\begin{bmatrix} u \\ v \end{bmatrix} = \begin{bmatrix} m_1 && m_2 \\ m_3 && m_4 \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix} + \begin{bmatrix} t_x \\ t_y \end{bmatrix}$.

We want to solve for the transformation parameters so we
rewrite it as so:

$\begin{bmatrix} x && y && 0 && 0 && 1 && 0 \\ 0 && 0 && x && y && 0 && 1 \\ ... \\ ... \end{bmatrix} \begin {bmatrix} m_1 \\ m_2 \\ m_3 \\ m_4 \\ t_x \\ t_y\end{bmatrix} = \begin{bmatrix} v \\ v \end{bmatrix}$

We can get the least-squares solution by solving the following equation:

$\bold x = [A^TA]^{-1}A^T \bold b$

This minimies the sum of the squares of the distances from the projected
model locations to the corresponding image locations. We require each
match to agree within half the error range that was used for the parameters
in the Hough transform.


# Feature Based Alignment & Image Stitching (6, 9)
# Dense Motion Estimation (8)
# Structure From Motion (7)
# Stereo & 3D Reconstruction (11, 12)
# Recognition (14)
