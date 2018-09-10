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

Geometric Transformation:
 - Perspective Camera: 3D to 2D projection

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
    - on the X and Y axis by some frustum in order give the illusion of depth.

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
   - Vanishing points:
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

 - Homogenous Co-ordinates:
    To homogenous co-ordinates:
    - $(x, y) => (x, y, 1)$
    - $(x, y, z) => (x, y, z, 1)$

    All homogenous co-ordinates can be scaled by some scalar since they are invariant to scaling.
    Converting back to cartesian co-ords just involves scaling by `w`.

    $k(x, y, w)$ = $(kx, ky, kz)$ => (${kx \over kw}$, ${ky \over kw}$) = (${x \over w}$, ${y \over w}$)

    Scaling the homogenous co-ordinates by a non-zero scalar does not change the point you represent.

    The reason why we use them is that many calculations become much simpler. We can represent infinity
    by setting the last element to 0, since converting back to cartesian would be a divison by zero.


    Basic geometry:
     - Line Equation: ${ax + by + c = 0}$ - just scale the vector representing the line and you don't actually change the line. Eg, you don't have to change all the co-efficients to change its length.
     - If you want to work out a line between two points: $line_ij = p_i \times p_j$
     - Intersection of two lines is the cross product of the two lines: $q_ij = line_i \times line_j$
     - Don't need special cases to represent infinity - just specify for instance $(1, 1, 0)$

    Projection Matrix:
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

 - Transformations:
    See slide 44 for affine transformations and degrees of freedom.

 - Radial Distortion:
    - Very common if you have a wide angle lens in your digital camera - think of fisheye lens.
    - Don't fit with the perspective projection model.
    - We can compensate for this by modelling the lens distortion and compensating for the nonlinear part.

Photometric image formation:
    - See lecture slides

Digital Cameras:

# Image Processing (3)
# Feature Detection & Matching (4)
# Feature Based Alignment & Image Stitching (6, 9)
# Dense Motion Estimation (8)
# Structure From Motion (7)
# Stereo & 3D Reconstruction (11, 12)
# Recognition (14)