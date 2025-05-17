
# Linear Algebra


DATE:  15-05-25


Tags:  [[Maths]]

# References:



# Content:


## [Vectors, what even are they?](https://www.youtube.com/watch?v=fNk_zzaMoSs&list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab&index=5) 

- Fundamental building block of linear algebra.

- Views:
	- **Physics Perspective:** Vectors are arrows in space, defined by their length and direction. Their specific location doesn't matter as long as length and direction are the same.
	- **Computer Science Perspective:** Vectors are ordered lists of numbers (e.g., `[square_footage, price]`). The order of the numbers is crucial.
	- **Mathematician's Perspective (Abstract):** A vector can be *any* object for which there are sensible definitions of adding two such objects and multiplying one by a number (scaling). This perspective is deferred but underscores the importance of these two operations.

- **Focus:** For linear algebra, vectors are best visualized as arrows in a coordinate system **rooted at the origin**. This elegantly combines the geometric intuition of the physics view with the numeric precision of the CS view.

* **Vectors in Coordinate Systems:**
    * **2D Coordinates:** A vector `[x, y]` represents an arrow starting at the origin `(0,0)` and ending at the point `(x,y)`. The numbers are instructions: move `x` units along the x-axis, then `y` units parallel to the y-axis.
    * **3D Coordinates:** Similarly, a vector `[x, y, z]` is an arrow from the origin `(0,0,0)` to the point `(x,y,z)`.
    * There's a one-to-one correspondence: each arrow (from the origin) has unique coordinates, and each set of coordinates defines a unique arrow (from the origin).

*   **Fundamental Vector Operations:**
    *   **1. Vector Addition (Geometric "Tip-to-Tail"):**
        *   To add vector **v** and vector **w**: place the tail of **w** at the tip of **v**.
        *   The sum (**v + w**) is the new vector drawn from the original tail of **v** to the (newly positioned) tip of **w**.
        *   Conceptually, this represents a sequence of movements or displacements.
    *   **Vector Addition (Numeric/Coordinate-wise):**
        *   This is done by adding the corresponding components of the vectors.
        *   Example: `[x1, y1] + [x2, y2] = [x1+x2, y1+y2]`.
        *   This numeric operation directly corresponds to the geometric tip-to-tail method.
    *   **2. Scalar Multiplication (Geometric "Scaling"):**
        *   Multiplying a vector **v** by a number (called a "scalar," e.g., 2, 1/3, -1.8) scales the vector.
        *   `2v` means stretching **v** to be twice its original length in the same direction.
        *   `(1/3)v` means squishing **v** to one-third its original length, same direction.
        *   `-1.8v` means flipping **v**'s direction and then stretching it by a factor of 1.8.
    *   **Scalar Multiplication (Numeric/Coordinate-wise):**
        *   Multiply each component of the vector by the scalar.
        *   Example: `c * [x, y] = [c*x, c*y]`.
        *   "Scalar" is the term for a number when it's used to scale a vector.

*   **Core Idea of Linear Algebra:**
    *   The two fundamental operations—vector addition and scalar multiplication—are central to nearly all topics in linear algebra.
    *   The power and utility of linear algebra come from the ability to seamlessly translate between the geometric interpretation (arrows, movements, space) and the numeric representation (lists of numbers, coordinates), allowing for both visual intuition and precise computation.

