---
layout: post
title: "Sparse Features and L2 Regularization"
date: 2012-05-10 17:21
comments: true
categories: 
---

  Suppose you're doing some typical supervised learning on a gigantic dataset where the total loss over all samples for parameter $$w$$ is simply the sum of the losses of each sample $$i$$, i.e.,

$$
  L(w) = \sum_{i} l(x_i, y_i, w)
$$

  Basically any loss function you can think of in the i.i.d sample regime can be composed this way.  Since we assumed that your dataset was huge, there's no way you're going to be able to load it all into memory for BFGS, so you choose to use [Stochastic Gradient Descent](http://en.wikipedia.org/wiki/Stochastic_gradient_descent).  The update for sample $$i$$ with step size $$\eta_t$$ would then be,

$$
  w_{t+1} = w_t - \eta_t \nabla_w l(x_i, y_i, w_t)
$$

  So far, so good.  If $$\nabla_w l(x_i, y_i, w)$$ is sparse, then you only need to change a handful of $$w$$'s components.  Of course, being the astute Machine Learning expert that you are, you know that you're going to need some regularization.  Let's redefine the total loss and take a look at our new update equation,

$$
\begin{align}
  L(w) & = \sum_{i} l(x_i, y_i, w) + \frac{\lambda}{2}||w||_2^2  \\
  w_{t+1} & = w_t - \eta_t \left( \nabla_w l(x_i, y_i, w_t) + \lambda w_t \right)
\end{align}
$$

  Uh oh.  Now that $$w$$ appears in our Stochastic Gradient Descent update equation, you're going to have change *every* non-zero element of $$w$$ at each iteration, even if $$\nabla_w l(x_i, y_i, w)$$ is sparse!  Whatever shall you do?

  The answer isn't as scary as you might think.  Let's do some algebraic manipulation from $$t=0$$,

$$
\begin{align}
  w_{1} 
  & = w_0 - \eta_0 \left( \nabla_w l(x_i, y_i, w_0) + \lambda w_0 \right) \\
  & = w_0 - \eta_0 \nabla_w l(x_i, y_i, w_0) - \eta_0 \lambda w_0 \\
  & = (1 - \eta_0 \lambda ) w_0 - \eta_0 \nabla_w l(x_i, y_i, w_0) \\
  & = (1 - \eta_0 \lambda ) \left(
      w_0 - \frac{\eta_0}{1-\eta_0 \lambda } \nabla_w l(x_i, y_i, w_0)
    \right) \\
\end{align}
$$

  Do you see it now?  $$L_2$$ regularization is really just a _rescaling_ of $$w_t$$ at _every_ iteration.  Thus instead of keeping $$w_t$$, let's keep track of,

$$
\begin{align}
  c & = \prod_{\tau=0}^t (1-\eta_{\tau} \lambda )  \\
  \bar{w}_t & = \frac{w_t}{c}
\end{align}
$$

  where you update $$\bar{w}_t$$ and $$c_t$$ by,

$$
\begin{align}
  \bar{w}_{t+1} 
  & = \bar{w}_t - \frac{\eta_t}{(1 - \eta_t) c_t} \nabla_w l(x_i, w_i, c_t \bar{w}_t) \\
  c_{t+1} 
  & = (1 - \eta_t \lambda) c_t
\end{align}
$$

  And that's it!  As a final note, depending what value you choose for $$\lambda$$, $$c_t$$ is going to get really big or really small pretty fast.  The usual "take the log" tricks aren't going to fly, either, as $$c_t$$ need not be positive.  The only way around it I've found is to check every iteration if $$c_t$$ is getting out of hand, then transform $$\bar{w}_{t} \leftarrow \bar{w}_t c_t$$ and $$c_t \leftarrow 1$$ if it is.

  Finally, credit should be given where credit is due.  This is a slightly more detailed explanation of [Alex Smola's Blog Post](http://blog.smola.org/post/940672544/fast-quadratic-regularization-for-online-learnin) from about a year ago, which in turn is accredited to Leon Bottou.
