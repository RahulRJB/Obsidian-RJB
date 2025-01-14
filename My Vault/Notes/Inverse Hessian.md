
# Inverse Hessian


DATE:  14-01-25


Tags:

# References:




# Content:


The Hessian matrix is a square matrix of second-order partial derivatives of a function. For optimization problems in machine learning, this function is typically the loss/cost function. If you have a function f(x,y), the Hessian would look like:

`H = [     [∂²f/∂x², ∂²f/∂x∂y],    [∂²f/∂y∂x, ∂²f/∂y²] ]`

The inverse Hessian (H⁻¹) is important because:

1. It captures the curvature of the loss landscape
2. In optimization, it helps determine optimal step sizes in different directions
3. It's used in Newton's method for optimization: x_new = x_old - H⁻¹∇f


