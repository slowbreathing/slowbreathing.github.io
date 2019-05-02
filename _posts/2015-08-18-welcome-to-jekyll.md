---
layout: post
title:  "Welcome to Jekyll!"
date:   2015-08-18 15:07:19
categories: [tutorial]
comments: true
---
You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

<!--more-->

Jekyll also offers powerful support for **code snippets**:

{% highlight python %}
def _softmax_grad(s):
    # Take the derivative of softmax element w.r.t the each logit which is usually Wi * X
    # input s is softmax value of the original input x.
    # s.shape = (1, n)
    # i.e. s = np.array([0.3, 0.7]), x = np.array([0, 1])
    # initialize the 2-D jacobian matrix.
    jacobian_m = np.diag(s)
    #print("jacobian_m:",jacobian_m)
    for i in range(len(jacobian_m)):
        for j in range(len(jacobian_m)):
            if i == j:
                #print("equal:",i,j)
                jacobian_m[i][j] = s[i] * (1-s[i])
            else:
                #print("not equal:",i,j)
                jacobian_m[i][j] = -s[i]*s[j]
    #print("jacobian_m:",jacobian_m)
    return jacobian_m
{% endhighlight %}

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll’s dedicated Help repository][jekyll-help].

formulaes::

\begin{equation} \begin{bmatrix} x & \dot{x} & \theta & \dot{\theta} & L & m & M \end{bmatrix} \end{equation}

![Smithsonian Image]({{ site.url }}/img/self.png)
That being me.



[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
