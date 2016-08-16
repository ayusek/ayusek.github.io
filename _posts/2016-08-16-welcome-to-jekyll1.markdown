---
layout: post
title:  "Welcome to Jekyll!"
date:   2016-08-16 11:13:04 -0700
categories: jekyll update
---

From a theoretical perspective, adding regularization makes a convex loss function strongly-convex, allowing faster optimization.  Regularization also restricts the solution space (under some constraints). Before diving into analyzing the minimizer, let us first define the problem. Hinge loss is often used as a surrogate loss for soft-margin SVM problems, and is defined as  : 
$$
	l(w,x,y) = max(0, 1 - yw^Tx)
$$ 

Note that Hinge loss is a positive convex function (but not strongly-convex). If we consider the soft margin SVM we have the objective function as follows : 

$$
	L(w) = \frac{\lambda}{2}\|w\|^2 + \frac{1}{n}\sum_{i=1}^{n}max(0,1-y_iw^Tx_i)
$$ 

We want to find $$ w^* = argmin ( L(w)) $$

 **Claim :** 


$$ \forall x_i, y_i \epsilon R, w^*^2 \leq \frac{1}{\lambda} $$ or the minimizer of the hinge loss always lies in a ball of radius $$ \frac{1}{\lambda} $$

 We have the following trivial bound directly induced by regularization. 

#### A Trivial Bound 
**It is easy to show that the solution $$w^*$$ lies in a ball of radius  $$\sqrt{\frac{2}{\lambda}}$$**
**Proof** - 
 Observer that L(0) = 1 
	Since, $$l(w,x,y)$$ is a positive function,
	and $$\frac{\lambda}{2}||w||^2 \geq 1 $$,  $$ \forall w $$  s.t.  $$ ||W||^2 >  \frac{2}{\lambda}$$
	It is easy to see that $$ ||w^*||^2 \leq \frac{2}{\lambda} $$

This is an interesting observations that for convex positive functions which have the same constant value at w = 0, l-2 regularization restricts the solution in a ball around the origin.  But we can do better than $$ \frac{2}{\lambda}$$. This fact was used in the PEGASOS algorithm by Shai Shalev-Shwartz et al. Their proof used ideas from Duality to restrict the solution space. 

#### Improving over the Trivial Bound 
Here, I give a proof using Geometry and Basic Algebra. Instead of analyzing the general problem, we make the following simplifications, all of which are consistent with the result. 
1.      Since the solution exists for all $$x_i$$ and $$y_i$$, we would be replacing $$x_iy_i$$ by $$\alpha_i$$
	 $$ L(w) = \frac{\lambda}{2}||w||^2 + \frac{1}{n}\sum_{i=1}^{n}max(0,1-w^T\alpha_i) $$
2.  We give the analysis for one dimensional problem. Analysis for multiple dimensions is similar where we go to concentric rings and quadratic surfaces instead of parabola's and intervals in our analysis. 
3. Without loss of generality, we would be assuming $$w^* \geq 0 $$
4. Under the assumption 3, we observe that the presence of terms with negative $$\alpha_k$$ pushes the optimal solution closer to the origin, when compared to the case when we replace $$\alpha_k$$ with $$- \alpha_k$$. Hence, we would be replacing all the negative $$\alpha_k$$ with $$-\alpha_k$$.
5. We assume that $$ \alpha_i's $$ are ordered i.e. $$\alpha_1 \geq \alpha_2 \geq \alpha_3 ... \geq \alpha_n$$
Thus we observe that if we can bound the solution under the above 4 assumptions, we also provide a bound in general case. Our new loss function now becomes : 

$$ L(w) =    L(w) = \frac{\lambda}{2}||w||^2 + \frac{1}{n}\sum_{i=1}^{n}max(0,1-w^T\alpha_i)  ,  \hspace{0.2in}\alpha_i \geq 0 $$

Now, if we observe our individual hinge losses on the same , we would see a bunch of wedges each growing towards the negative axis from $$ \frac{1}{\alpha_i} $$ and slope $$- \alpha_i$$.

Now let us divide the number line into the intervals [0, $$\frac{1}{\alpha_1}$$] , [$$\frac{1}{\alpha_1}$$ , $$\frac{1}{\alpha_2}$$],  ... ,  [$$\frac{1}{\alpha_{n-1}}$$ , $$\frac{1}{\alpha_{n}}$$], [$$\frac{1}{\alpha_n}$$ , $$\infty$$]. We can similarly break down the loss function in these intervals by analytically analyzing the max as : 
  $$
    L(w) = \left\{ \begin{array}{@{}lr@{}}
        \frac{\lambda}{2}w^2 + \frac{1}{n}\sum_{i=1}^{n} (1-w^T\alpha_i)   & \text{for }0\leq w\leq \frac{1}{\alpha_1}\\
          \frac{\lambda}{2}w^2 & \text{for } \frac{1}{\alpha_n} \leq w \\
        \frac{\lambda}{2}w^2 + \frac{1}{n}\sum_{i=j}^{n} (1-w^T\alpha_i)  & \text{for }\frac{1}{\alpha_{j-1}}\leq w\leq \frac{1}{\alpha_j}
        \end{array}\right \} 
$$

Guess what, all of these are quadratics. Let us look at the optimal solution of each of these quadratics (without worrying about if it lies in the domain of the quadratic for now). These are the points where the derivatives become 0 for the quadratic. 

  $$
    w^* = \left\{ \begin{array}{@{}lr@{}}
         \frac{1}{n\lambda}\sum_{i=1}^{n} (\alpha_i)  & \text{for }0\leq w\leq \frac{1}{\alpha_1}\\
         0  & \text{for } \frac{1}{\alpha_n} \leq w \\
        \frac{1}{n\lambda}\sum_{i=j}^{n} (\alpha_i)  & \text{for }\frac{1}{\alpha_{j-1}}\leq w\leq \frac{1}{\alpha_j}
        \end{array}\right \} 
$$

Now, let us force these solutions to exists in their domains, i.e. w* is such that gradient vanishes at w* i.e. it must be a solution to one such quadratic. 

Case 1 :   $$\frac{1}{\alpha_n} \leq w* $$ 
$$w^*$$ = 0 $$\implies {w^*}^2 \leq \frac{1}{\lambda}$$   (if this is the solution)

Case 2 :  $$w^* \leq \frac{1}{\alpha_1}$$
$$\implies  \frac{1}{n\lambda}\sum_{i=1}^{n} (\alpha_i) \leq \frac{1}{\alpha_n}  $$  (solution satisfies the constraints)
$$ \implies n\lambda \geq \alpha_n \sum_{i=1}^{n} (\alpha_i) \geq \frac{\sum_{i=1}^{n} (\alpha_i)}{n} \sum_{i=1}^{n} (\alpha_i)  $$
$$ \implies \lambda \geq \alpha_n \sum_{i=1}^{n} (\alpha_i) \geq (\frac{\sum_{i=1}^{n} (\alpha_i)}{n}) ^2 $$ 
$$ \implies \frac{1}{\lambda} \geq (\frac{\sum_{i=1}^{n} (\alpha_i)}{n\lambda}) ^2 $$ 
$$\implies w* \leq \sqrt{\frac{1}{\lambda}} $$

Case 3:  $$\frac{1}{\alpha_{j-1}}\leq w\leq \frac{1}{\alpha_j}$$
$$\implies \frac{1}{\alpha_{j-1}}\leq   \frac{1}{n\lambda}\sum_{i=j}^{n} (\alpha_i)  \leq \frac{1}{\alpha_j}$$
$$\implies n\lambda \geq  \alpha_j\sum_{i=j}^{n} (\alpha_i) $$
$$\implies n\lambda \geq  \frac{\sum_{i=j}^{n} \alpha_i}{n - j  +1} \sum_{i=j}^{n} (\alpha_i) $$
$$\implies n\lambda \geq  \frac{\sum_{i=j}^{n} \alpha_i}{n} \sum_{i=j}^{n} (\alpha_i) $$
$$\implies \frac{1}{\lambda} \geq  (\frac{\sum_{i=j}^{n} \alpha_i}{n\lambda})^2$$
$$\implies \frac{1}{\lambda} \geq  (w*)^2$$
$$\implies w* \leq \sqrt{\frac{1}{\lambda}} $$

Well, we analyzed the case when the gradient at the solution is 0. 

What if the solution occurs at the intersection of the two parabola's i.e. on one of the interval switches i.e. at some $$\frac{1}{\alpha_i}$$, we would have 

$$ \frac{\sum_{i=j}^{n} \alpha_i }{n\lambda} \geq \frac{1}{\alpha_i} \geq \frac{\sum_{i=j + 1}^{n} \alpha_i }{n\lambda}$$

$$ \implies  \frac{(n-i + 1) \alpha_i}{n \lambda} \geq \frac{1}{\alpha_i}$$
$$ \implies \frac{1}{\alpha_i^2} \leq \frac{(n-i + 1)}{n \lambda} \leq \frac{1}{\lambda}$$
$$\implies w* = \frac{1}{\alpha_i} \leq \sqrt{\frac{1}{\lambda}} $$







This completes our simple proof. This is not the most elegant proof of this result and I would be interested in knowing more elegant proofs. 

#### Learning Experience 
1. Experiment your theoretical beliefs. At first, I could not believe that this constrain on the solution space exists for all the $$\alpha_i's$$ and then I wrote a code to experiment it and it was indeed true. 
