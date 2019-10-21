---
torlayout: post
title:  "Eigenvalue and Eigenvector"
date:   2018-10-08
author: "Huang Qiang"
tags: [linear algebra, eigenvalue, eigenvector]
comments: true
---

## Eigenvector and eigenvalues

This is a text version of 3Blue1Brown's [Eigenvectors and eigenvalues](https://www.youtube.com/watch?v=PFDu9oVAE-g)


Consider matrix

$$\begin{bmatrix} 
3 & 1 \\
0 & 2 
\end{bmatrix}$$

It moves basis vector (1,0) to (3,0) and (0,1) to (1,2). Focusing what it does on one particular vector, think about the span of that vector. A line passing through its origin and its tip. Most vectors are going to get knocked off their span during the transformation. But some special vectors do remain on their own span. Meaning the effect that the matrix has on such a vector is just to stretch it or squish it.

In the above example, the basis vector (1,0) is such special vector. $$\hat i$$ moves over to 3 times itself. It also means any other vector on the x-axis is also just stretched by a factor of 3, remains on its own span.

A slightly sneakier vector that remains on its own span during this transformation is (-1,1). It ends up getting stretched by a factor of 2. The same happens to any vectors that on this diagonal line. Under this transformation, those are all the vectors with this special property. Any other vector is going to get rotated somewhat during the transformation, knocked off the line that it spans. These special vectors are called **eighenvectors** of this transformation.

Each eigenvector has associated with it, what's called an **eigenvalue**, which is just the factor by which it stretched or squashed during this transformation. Eigenvalue doesn't have to be positive, -0.5 means the vector gets flipped and squished by a factor of 1/2.

It's a pattern shows up a lot in linear algebra, with any linear transformation described by a matrix, you could understand what it's doing by reading the columns of this matrix as the landing spots for basis vectors.

With any linear transformation described by a matrix, you could understand what it's doing by reading off the columns of this matrix as the landing spots for basis vectors. (This gives too much weight to our coordinate system)

A **better way** to get at the heart of what the linear transformation actually does, less dependent on your particular coordinate system, is to find the **eigenvectors** and **eigenvalues**.


$$
A \vec{v} = \lambda \vec{v}
$$

$A$ is a matrix representing some transformation, $\vec{v}$ is eigenvector, $\lambda$ is the eigenvalue, $\vec{v} \not=0$. <u>What this expression is saying is that the matrix-vector product gives the same result as just scaling the eigenvector $\vec{v}$ by some scalar $\lambda$.</u> Finding the $\lambda$ and $\vec{v}$ of matrix A is to find the values that make this expression true.


It's a little awkward to work with at first, because that left hand side represents matrix-vector multiplication, but the right hand side here is scalar-vector multiplication. So let's start by rewriting that right hand side as some kind of matrix-vector multiplication, using a matrix, which has the effect of scaling any vector by a factor of $\lambda$. This is $\lambda I$, where $I$ is the identity matrix.

Because $A\vec{v} - \lambda I \vec{v} = 0$, $\lambda \vec{v}$ can also be written as $\lambda I \vec{v}$, therefore we have

$$
(A - \lambda I) \vec{v} = 0
$$

Since $\vec{v} \not= 0$, the only way to satisfy the equation above is when $det(A - \lambda I) = 0$. It means we add or subtract a value from the diagonal of A such that the altered matrix A has its determinant equals to 0.

After finding $\lambda$ we could find the vector $\vec{v}$ such that after applied the linear transformation by matrix A, $\vec{v}$ still remains on its own span.

For example, for matrix 

$$\begin{bmatrix} 
3 & 1 \\
0 & 2 
\end{bmatrix}$$

What $\lambda$ we subtract from its diagonal makes the determinant of it become zero? 

$$det\bigg(\begin{bmatrix} 
3 - \lambda & 1 \\
0 & 2 - \lambda 
\end{bmatrix}) = (3-\lambda)(2-\lambda) - 1· 0 = 0$$

Solve for $\lambda$ get the possible eigenvalues are $\lambda = 2$ and $\lambda = 3$. Plug the eigenvalue say $\lambda = 2$ to the matrix, get the following equation

$$\begin{bmatrix} 
3 - 2 & 1 \\
0 & 2 - 2
\end{bmatrix} 
\begin{bmatrix} 
x \\
y
\end{bmatrix} = 
\begin{bmatrix} 
0 \\
0
\end{bmatrix}$$

You'd see that the solutions are all the vectors on the diagonal line spanned by (-1, 1).

This corresponds to the fact that the unaltered matrix [(3, 0), (1, 2)] has the effect of stretching all those vectors by a factor of 2.

Here you need to have a solid grasp of determinants and why they relate to linear systems of equations having non-zero solutions.

Now, a 2-D transformation doesn't have to have eigenvectors. For example, consider a rotation by 90 degrees.

$$\begin{bmatrix} 
0 & -1 \\
1 & 0
\end{bmatrix}$$

This doesn't have any eigenvectors, since it rotates every vector off of its own span. If you actually try computing the eigenvalues of a rotation like this, you got imaginary numbers as the solution. The fact that there are no real number solutions indicates that there are no eigenvectors.

Another interesting example is shear transformation, meaning fixing $\hat{i}$ and moves $\hat{j}$ one over, like matrix

$$\begin{bmatrix} 
1 & 1 \\
0 & 1
\end{bmatrix}$$

All of the vectors on the x-axis are eigenvectors with eigenvalue 1, since they remain fixed in place. In fact, these are the only eigenvectors.

When you subtract off $\lambda$ from the diagonals and compute the determinant

$$det\bigg(\begin{bmatrix} 
1 - \lambda & 1 \\
0 & 1 - \lambda 
\end{bmatrix}) = (1-\lambda)(1-\lambda) - 1· 0 = 0$$

You got $\lambda=1$.

Keep in mind though, it's also possible to have just one eigenvalue, but with more than just a line full of eigenvectors.

A simple example is a matrix that scales everything by 2,

$$\begin{bmatrix} 
2 & 0 \\
0 & 2
\end{bmatrix}$$

the only eigenvalue is 2, but every vector in the plane gets to be an eigenvector with that eigenvalue.

## Eigenbasis

For example, maybe $\hat{i}$ is scaled by -1 and $\hat{j}$ is scaled by 2.

Writing their new coordinates as the columns of a matrix

$$\begin{bmatrix} 
-1 & 0 \\
0 & 2
\end{bmatrix}$$

Notice that those scalar multiples -1 and 2, which are the eigenvalues of $\hat{i}$ and  $\hat{j}$, sit on the diagonal of our matrix and every other entry is a 0. For a diagonal matrix, in fact, all the basis vectors are eigenvectors, with the diagonal entries of this matrix being their eigenvalues.

Diagonal matrices makes it easier to multiply itself a whole bunch of times.

$$\bigg(\begin{bmatrix} 
3 & 0 \\
0 & 2
\end{bmatrix}\bigg)^{100} = 
\begin{bmatrix} 
3^{100} & 0 \\
0 & 2^{100}
\end{bmatrix}$$

Of course, you will rarely be so lucky as to have your basis vectors also be eigenvectors, but if your transformation has a lot of eigenvectors, enough so that you can choose a set that spans the full space, then you could change your coordinate system so that these eigenvectors are your basis vectors. How?

You use the two eigenvectors that make those coordinates the columns of a matrix, called change of basis matrix:


$$\begin{bmatrix} 
1 & -1 \\
0 & 1
\end{bmatrix}$$

When you sandwich the original transformation by putting the change of basis matrix on it's right and the inverse of the change of basis matrix on its left,

$$\begin{bmatrix} 
1 & -1 \\
0 & 1
\end{bmatrix}^{-1}
\begin{bmatrix} 
3 & 1 \\
0 & 2
\end{bmatrix}
\begin{bmatrix} 
1 & -1 \\
0 & 1
\end{bmatrix} = \begin{bmatrix} 
3 & 0 \\
0 & 2
\end{bmatrix}$$

the result will be a matrix representing that same transformation, but from the perspective of the new basis vectors coordinate system.

The whole point of doing this with eigenvectors is that this new matrix is guaranteed to be diagonal with its corresponding eigenvalues down that diagonal.

So if you want to compute 

$$\bigg(\begin{bmatrix} 
3 & 1 \\
0 & 2
\end{bmatrix}\bigg)^{100} = 
\begin{bmatrix} 
1 & -1 \\
0 & 1
\end{bmatrix}
\bigg(\begin{bmatrix} 
3 & 1 \\
0 & 2
\end{bmatrix}\bigg)^{100}
\begin{bmatrix} 
1 & -1 \\
0 & 1
\end{bmatrix}^{-1}$$

Using number get the same result.

```python
>>> a = np.array([[3, 1],
...               [0, 2]])
>>> np.linalg.matrix_power(a, 4)
array([[81, 65],
       [ 0, 16]])
>>> e = np.linalg.eig(a)[1]
>>> e_inv = np.linalg.inv(e)
>>> d = e_inv.dot(a).dot(e)
>>> e.dot(d**4).dot(e_inv)
array([[81., 65.],
       [ 0., 16.]])
```

You can't do this with all transformations. A shear, for example, doesn't have enough eigenvectors to span the full space. But if you can find an eigenbasis, it makes matrix operations really lovely.