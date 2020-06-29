---
layout: post
title:  "Notes on Computer Vision and OpenCV: Harris Corner Detection"
---
This post is the notes I take when learning __Harris Corner and Edge Detection__ in [Datawhale](https://datawhale.club/) (an open-source organization holding AI-related learning sessions). The learning sessions focus on computer vision algorithms implemented in OpenCV (most of them do not use deep learning). I participate in the sessions seeking for better intuition and faster inference compared to the deep learning algorithms.

Harris algorithm, along with Moravec detector, Forstner detector, SUSAN (Smallest Univalue Segment Assimilarting Nucleus) etc., is among the most commonly used grayscale-based corner detectors. The theory of Harris algorithm is pretty simple. As shown in the picture below, 
- around a flat point (left), the pixel value does not change much no matter in which direction we move the object;
- around an edge point (middle), the pixel value changes significantly if we move the object in some direction (in this case, for example, horizontally, not vertically);
- around a corner point (right), the pixel changes significantly in multiple directions.
![](/data/harris_corner.png)

The observation above inspires us to compute the gradient (a measurement of rates of change in different directions) of the grayscale (or pixel values). Common methods to compute gradients include Sobel method, Robinson method, Laplacian, etc.

Based on the theory, we briefly go through the math (referring to the [paper](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.434.4816&rep=rep1&type=pdf)). Denote $I(x,y)$ as the grayscale at the position $(x,y)$. So the magnitute of the gradient will be 
$$ E(u,v)=\sum_{x,y} w(x,y)[I(x+u, y+v)-I(x,y)]^2 $$

Using 2nd-order Taylor expension and real-symmetric matrix diagonalization, we can write $E(u,v)=(u,v)M(u,v)^T$, where the matrix $M$ is 
$$ M=\sum_{x,y}w(x,y) \begin{bmatrix} I_x^2 & I_xI_y \\ I_xI_y & I_y^2 \end{bmatrix}= Q^{-1}\begin{bmatrix} \lambda_1 & 0 \\ 0 & \lambda_2 \end{bmatrix} Q $$

with $\lambda_1$, $\lambda_2$ the two eigenvalues of $M$.

Now we define a response function $R=\det(M)-k(\text{trace}(M))^2=\lambda_1\lambda_2-k(\lambda_1+\lambda_2)^2$, where $k$ is a hyperparameter, usually taken between (0.04, 0.06). Note that 
- if $(x,y)$ is a corner, both $\lambda_1$, $\lambda_2$ are large, and then $R$ is large;
- if $(x,y)$ is on the edge, one of $\lambda_1$, $\lambda_2$ is large and the other is small, then $R$ is negative;
- if $(x,y)$ is in a flat domain, both $\lambda_1$, $\lambda_2$ are small and so is $R$ (but still positive).

So we can set a threshold to detect the corner points. An implementation leveraging OpenCV can be found [here](https://github.com/huijunzhao-ds/opencv_tutorial/blob/master/harris.py).
