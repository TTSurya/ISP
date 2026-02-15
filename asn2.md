# Assignment-2 Image Signal Processing (EE5175) done by

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

# In-Plane Rotation and Translation between Two Images - Occlusion Detection

## Problem Statement

Two aerial grayscale images **`IMG1.png`** and **`IMG2.png`** capture the same airport parking bay from different camera positions and at different times.

The images are related by an **in-plane rotation and translation**.

Given two point correspondences:

| Correspondence | IMG1 $(x,y)$ | IMG2 $(x,y)$ |
| -------------- | ------------ | ------------ |
| 1              | $(29, 124)$  | $(93, 248)$  |
| 2              | $(157, 372)$ | $(328, 399)$ |

Determine the changes in `IMG2` with respect to `IMG1`.

---

## Transformation Model

An in-plane transformation is modeled as:

$$
\mathbf{q} = R \mathbf{p} + \mathbf{t}
$$

where

* $\mathbf{p}$ is a point in IMG2
* $\mathbf{q}$ is the corresponding point in IMG1
* $R$ is the rotation matrix
* $\mathbf{t}$ is the translation vector

---

## Rotation Matrix

Define direction vectors: 
$$ \quad \mathbf{v}_1 = p_2 - p_1,\quad \mathbf{v}_2 = q_2 - q_1$$

Compute orientations:
$$
\theta_1 = \tan^{-1}\left(\frac{v_{1y}}{v_{1x}}\right), \quad
\theta_2 = \tan^{-1}\left(\frac{v_{2y}}{v_{2x}}\right)
$$

Rotation angle:
$\quad \theta = \theta_2 - \theta_1$

Rotation matrix:
$$\quad
R =
\begin{bmatrix}
\cos\theta & -\sin\theta \\
\sin\theta & \cos\theta
\end{bmatrix}
$$


Since $R$ is orthogonal:$\quad R^{-1} = R^T$

---

## Translation Matrix

Using correspondence 1:
$\quad \mathbf{t} = q_1 - R p_1$

---

## Bounding Box Computation

The four corners of IMG2 are transformed:
$\quad
p' = R p + t
$

Minimum and maximum transformed coordinates define the output image size.

---

## Target-to-Source Mapping

For each target pixel $(x_t, y_t)$:
$\quad
\mathbf{p}_s = R^{-1}(\mathbf{p}_t - \mathbf{t})$

---

## Implementation Summary

* Image is loaded as tensor
* Rotation is then computed using direction vectors.
* Translation is computed from correspondence since rotation is known.
* Transformation of corners give output image dimensions.
* For each target pixel, inverse mapping is applied
* Bilinear interpolation is used to compute pixel intensities at non-integer locations.
* Output values are clamped to $[0, 255]$ and converted from float to `uint8`
* Result saved as **`IMG2_transformed.png`**

---

## Result

`IMG2` is transformed into the coordinate frame of `IMG1` using the transformation relation obtained from 2-point correspondences, with black regions appearing where no source pixels are available.

After alignment, two aircraft seen on rooftops in `IMG2_transformed` are not present in `IMG1`.

---

## Summary

Since images are captured at different times, the following may take place:

* Some structures appear or disappear
* Objects may shift position

Aligning the images helps highlight these differences which is the key principle behind occlusion detection.

An in-plane transformation (rotation + translation) was identified using 2-point correspondences and applied using target-to-source mapping with bilinear interpolation, producing geometrically aligned images suitable for analyzing scene differences.

---
