# Assignment-1 Image Signal Processing (EE5175) done by

* Name : Surya T T
* Roll No : ee22b051

# Generics

## Coordinate Convention

The following coordinate system is used throughout:

* Origin $(0, 0)$ is at the **top-left corner** of the image
* **x-axis** increases along the **rows** (downward direction)
* **y-axis** increases along the **columns** (rightward direction)

---

## Bilinear Interpolation
The computed source coordinates $(x_s, y_s)$ are generally non-integer. Bilinear interpolation is used to estimate the pixel intensity from the four nearest integer neighbors:

* Upper-left: ($x_0$, $y_0$)
* Upper-right: ($x_0$, $y_1$)
* Lower-left: ($x_1$, $y_0$)
* Lower-right: ($x_1$, $y_1$)

where
$x_0 = \lfloor x_s \rfloor,\quad y_0 = \lfloor y_s \rfloor,\quad x_1 = \lfloor x_s \rfloor + 1,\quad y_1 = \lfloor y_s \rfloor + 1$

Interpolation weights are computed based on the fractional distances in both directions.

---

## Boundary Handling

If any of the required source pixels fall outside the valid image boundaries, the output pixel is assigned a **default value of 0 (black)**. This corresponds to zero padding at the boundaries.

---

# Q1) Image Translation using Target-to-Source Mapping

## Problem Statement

Translate the given grayscale image **`lena_translate.png`** by

* ($t_x = 3.75$) pixels along the **x-axis (rows / downward)** and 
* ($t_y = 4.3$) pixels along the **y-axis (columns / rightward)**

The transformation must be implemented using **target-to-source mapping** with **bilinear interpolation**, following the standard Python image coordinate convention.

---

## Translation Model


A translation by ($t_x$, $t_y$) maps a source point $(x_s, y_s)$ to a target point $(x_t, y_t)$ as:

$(x_t, y_t) = (x_s + t_x, y_s + t_y)$

The source coordinates are then obtained as:

$(x_s, y_s) = (x_t - t_x, y_t - t_y)$

For every target pixel $(i, j)$, the corresponding source coordinates are computed using the above equations.

---

## Implementation Summary

* The image is loaded as a tensor.
* For each target pixel, inverse translation is applied
* Bilinear interpolation is used to compute pixel intensities at non-integer locations
* Output values are clamped to $[0, 255]$ and converted from float to `uint8`
* The translated image is saved as **`lena_translated.png`**

---

## Result

The output image is a translation of the input image by $(3.75, 4.3)$ pixels, with black regions appearing where no source pixels are available.

---

## Summary

Image translation is implemented using target-to-source mapping with bilinear interpolation to ensure smooth intensity transitions and complete pixel coverage.

---

# Q2) Image Rotation using Target-to-Source Mapping

## Problem Statement

Rotate the given grayscale image **`pisa_rotate.png`** about its image centre so as to straighten the Pisa tower.  

The transformation must be implemented using **target-to-source mapping** with **bilinear interpolation**, following the standard Python image coordinate convention.

---

## Rotation Model

Let $(x_s, y_s)$ denote the source coordinates and $(x_t, y_t)$ denote the target coordinates.

Let the rotation angle be $\theta$ radians, where a **positive $\theta$ corresponds to an anticlockwise rotation** in image coordinates.

Using the image coordinate system, the **source-to-target Anti-Clock-Wise rotation** about the origin $(0,0)$ is given by:

$$
\begin{bmatrix}
x_t \\
y_t
\end{bmatrix}
=
\begin{bmatrix}
\cos\theta & \sin\theta \\
-\sin\theta & \cos\theta
\end{bmatrix}
\begin{bmatrix}
x_s \\
y_s
\end{bmatrix}
$$

---

## Rotation about Image Centre

To rotate about the image centre $(c_x, c_y)$, the following steps are applied:

1. Shift the source point so that the image centre becomes the origin  
2. Apply the rotation matrix  
3. Shift the point back to the original coordinate system  

This gives the **source-to-target mapping**:

$$
\begin{bmatrix}
x_t - c_x \\
y_t - c_y
\end{bmatrix}
=
\begin{bmatrix}
\cos\theta & \sin\theta \\
-\sin\theta & \cos\theta
\end{bmatrix}
\begin{bmatrix}
x_s - c_x \\
y_s - c_y
\end{bmatrix}
$$

---

## Target-to-Source Mapping

Since target-to-source mapping is used in implementation, the inverse rotation is applied:

$$
\begin{bmatrix}
x_s - c_x \\
y_s - c_y
\end{bmatrix}
=
\begin{bmatrix}
\cos\theta & -\sin\theta \\
\sin\theta & \cos\theta
\end{bmatrix}
\begin{bmatrix}
x_t - c_x \\
y_t - c_y
\end{bmatrix}
$$

The source coordinates are then obtained as:

$$
x_s = (\cos\theta)(x_t - c_x) - (\sin\theta)(y_t - c_y) + c_x
$$

$$
y_s = (\sin\theta)(x_t - c_x) + (\cos\theta)(y_t - c_y) + c_y
$$

For every target pixel $(i, j)$, the corresponding source coordinates are computed using the above equations.

---

## Implementation Summary

* The image is loaded as a tensor.
* The image centre $(c_x, c_y)$ is computed from the image dimensions
* For each target pixel, inverse rotation is applied about the image centre
* Bilinear interpolation is used to compute pixel intensities at non-integer locations
* Output values are clamped to $[0, 255]$ and converted from float to `uint8`
* The rotated image is saved as **`pisa_rotated.png`**
* Adjust the value of $\theta$ using hit and trial paired with human intuition until you straigten the Pisa Tower.

---

## Result

Using a rotation angle of $\theta = -0.09$ radians Anti-Clock-Wise, the Pisa tower is visually straightened, with black regions appearing where no source pixels are available.

---

## Summary

Image rotation about the image centre is implemented using target-to-source mapping with bilinear interpolation to ensure smooth intensity transitions and complete pixel coverage.

---

# Q3) Image Scaling using Target-to-Source Mapping

## Problem Statement

Scale the given grayscale image **`cells_scale.png`** by the following factors:

* $s_x = 0.8$ along the **x-axis (rows)**
* $s_y = 1.3$ along the **y-axis (columns)**

The transformation is implemented using **target-to-source mapping** with **bilinear interpolation**, following the standard Python image coordinate convention.

---

## Scaling Model

Let $(x_s, y_s)$ denote the source coordinates and $(x_t, y_t)$ denote the target coordinates.

Scaling by factors $(s_x, s_y)$ is defined by the **source-to-target mapping**:

$$
x_t = s_x \, x_s
$$

$$
y_t = s_y \, y_s
$$

---

## Target-to-Source Mapping

Since target-to-source mapping is used in implementation, the inverse scaling is applied:

$$
x_s = \frac{x_t}{s_x}
$$

$$
y_s = \frac{y_t}{s_y}
$$

For every target pixel $(i, j)$, the corresponding source coordinates are computed using the above equations.

---

## Implementation Summary

* The image is loaded as a tensor.
* For each target pixel, inverse scaling is applied to obtain source coordinates
* Bilinear interpolation is used to compute pixel intensities at non-integer locations
* Output values are clamped to $[0, 255]$ and converted from float to `uint8`
* The scaled image is saved as **`cells_scaled.png`**

---

## Result

The output image is a non-uniformly scaled version of the input image, compressed along the x-direction and stretched along the y-direction, with black regions appearing where no source pixels are available.

---

## Summary

Image scaling is implemented using target-to-source mapping with bilinear interpolation to ensure smooth intensity transitions and complete pixel coverage.

