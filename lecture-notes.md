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