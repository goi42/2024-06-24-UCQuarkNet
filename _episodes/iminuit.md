---
title: "Fitting with iminuit"
teaching: 15
exercises: 0
questions:
- "TBD"
objectives:
- "TBD"
keypoints:
- "TBD"
permalink: /_episodes/iminuit
---

This episode will introduce the `iminuit` package for fitting data.
More information about it, as well as a host of tutorials, can be found in its [documentation](https://scikit-hep.org/iminuit);
this episode draws heavily from these [tutorials](https://scikit-hep.org/iminuit/tutorials.html).
For information on fitting in the context of particle physics, see chapter 4 of *Statistics for nuclear and particle physicists* by Louis Lyons, from which this episode also draws.

## What is a fit?

A fit is a procedure to determine some parameters of a model we want to use to describe data.
Suppose we have some dataset that looks like this:

<figure>
<img src="{{site.baseurl}}/fig/linear_data.png" title="Some arbitrary data."/>
</figure>

For each value of $x$, we have measured a corresponding value of $y$. We want to come up with an equation, or "model", that describes the relationship between $x$ and $y$.

From looking at the data, it looks like a straight line, $y = f(x) \equiv mx + b$, might be a good model. In this case, $f(x,m,b)$ is our model, and it has two parameters $m$ and $b$ that we need to determine. We can do this by fitting the model to the data.

First, we need to guess some values for our parameters, say, $m=1$ and $b=0$. If we plot our model alongside our data, we see that it doesn't fit very well:

<figure>
<img src="{{site.baseurl}}/fig/linear_data_initial_fit.png" title="Data and a bad fit."/>
</figure>

It looks like the slope of our line, $m$, is too gradual, though our y-intercept, $b$, is about right. We then adjust our parameters to get better agreement between our data and our model, setting $m=2$ and $b=0$:

<figure>
<img src="{{site.baseurl}}/fig/linear_data_better_fit.png" title="Data and a better fit."/>
</figure>

This now looks about right. We have fit our model to our data.

## How good is our fit?

We performed the fit above using only qualitative evaluations of how well our model agreed with the data. We can be a bit more quantitative by defining a "goodness of fit" metric, which tells us how well our model describes the data. One such metric is the $\chi^2$ statistic, defined as
$$
\chi^2(\vec{\theta})=\sum_{i=1}^N\frac{(y_i-f(x_i,\vec{\theta}))^2}{\sigma_i^2},
$$
where $N$ is the number of measurements, $i$ is an index indicating each measurement in turn, $x_i$ indicates the $x$ value of the $i$th measurement, $y_i$ is the value measured at $x_i$, $\sigma_i$ is the uncertainty on the $i$th measurement, and $f(x_i,\vec{\theta})$ is the value of the model at $x_i$ with parameters $\vec{\theta}$.
The $\chi^2$ statistic is a measure of how well our model describes the data, with smaller values indicating a better fit.

We can see this by calculating the $\chi^2$ statistic for our model above. Our measurements can be organized in a table like this:

| $i$ | $x_i$ | $y_i$ | $\sigma_i$ |
|-----|-------|-------|------------|
| 1   | 0.00  | 1.03  | 0.1        |
| 2   | 0.11  | 1.30  | 0.1        |
| 3   | 0.22  | 1.48  | 0.1        |
| 4   | 0.33  | 1.54  | 0.1        |
| 5   | 0.44  | 1.98  | 0.1        |
| 6   | 0.56  | 2.16  | 0.1        |
| 7   | 0.67  | 2.28  | 0.1        |
| 8   | 0.78  | 2.61  | 0.1        |
| 9   | 0.89  | 2.81  | 0.1        |
| 10  | 1.00  | 3.03  | 0.1        |

Our initial model was $f(x,m=1,b=1) = x + 1$. We can add that to the table above:

| $i$ | $x_i$ | $y_i$ | $\sigma_i$ | $f(x_i,m=1,b=1)$ |
|-----|-------|-------|------------|------------------|
| 1   | 0.00  | 1.03  | 0.1        | 1.00             |
| 2   | 0.11  | 1.30  | 0.1        | 1.11             |
| 3   | 0.22  | 1.48  | 0.1        | 1.22             |
| 4   | 0.33  | 1.54  | 0.1        | 1.33             |
| 5   | 0.44  | 1.98  | 0.1        | 1.44             |
| 6   | 0.56  | 2.16  | 0.1        | 1.56             |
| 7   | 0.67  | 2.28  | 0.1        | 1.67             |
| 8   | 0.78  | 2.61  | 0.1        | 1.78             |
| 9   | 0.89  | 2.81  | 0.1        | 1.89             |
| 10  | 1.00  | 3.03  | 0.1        | 2.00             |

Now we calculate the $\chi^2$ statistic:
$$
\chi^2(m=1,b=1) = \frac{(1.03-1.00)^2}{0.1^2} + \frac{(1.30-1.11)^2}{0.1^2} + \cdots + \frac{(3.03-2.00)^2}{0.1^2} = 378.
$$
We can compare this with our improved model, $f(x,m=2,b=1) = 2 \times x + 1$:

| $i$ | $x_i$ | $y_i$ | $\sigma_i$ | $f(x_i,m=1,b=1)$ | $f(x_i,m=2,b=1)$ |
|-----|-------|-------|------------|------------------|------------------|
| 1   | 0.00  | 1.03  | 0.1        | 1.00             | 1.00             |
| 2   | 0.11  | 1.30  | 0.1        | 1.11             | 1.22             |
| 3   | 0.22  | 1.48  | 0.1        | 1.22             | 1.44             |
| 4   | 0.33  | 1.54  | 0.1        | 1.33             | 1.67             |
| 5   | 0.44  | 1.98  | 0.1        | 1.44             | 1.89             |
| 6   | 0.56  | 2.16  | 0.1        | 1.56             | 2.11             |
| 7   | 0.67  | 2.28  | 0.1        | 1.67             | 2.33             |
| 8   | 0.78  | 2.61  | 0.1        | 1.78             | 2.56             |
| 9   | 0.89  | 2.81  | 0.1        | 1.89             | 2.78             |
| 10  | 1.00  | 3.03  | 0.1        | 2.00             | 3.00             |

and calculate the $\chi^2$ statistic:
$$
\chi^2(m=2,b=1) = \frac{(1.03-1.00)^2}{0.1^2} + \frac{(1.30-1.22)^2}{0.1^2} + \cdots + \frac{(3.03-3.00)^2}{0.1^2} = 4.47,
$$
which is much smaller than for our initial model. This confirms quantitatively what we saw qualitatively before, that our second model is a better description of the data---it "fits" better.

The method above is also called the "least squares" method of parameter determination. Technically, we can only identify it with the $\chi^2$ statistic in certain cases (including the above); since these are the cases we are most interested in, we will treat the two ("least squares" and "$\chi^2$") as synonymous.

We typically assess the quality of the fit by looking not at the $\chi^2$ value itself, but at the "reduced $\chi^2$" value, defined as
$$
\chi^2_{\text{red}} \equiv \frac{\chi^2}{N_{\text{dof}}},
$$
where $N_{\text{dof}}$ is the number of degrees of freedom, defined as the number of measurements minus the number of parameters in the model. For the above example, $N_{\text{dof}} = 10 - 2 = 8$. The reduced $\chi^2$ value is a measure of how well our model describes the data, normalized by the number of measurements and the number of parameters in the model. A reduced $\chi^2$ value of 1 indicates a good fit, while values significantly larger than 1 indicate a poor fit. Using the reduced $\chi^2$ allows us to easily compare fit quality across models (which may have different numbers of parameters) and across datasets (which may have different numbers of measurements).

The reduced $\chi^2$ value for our first model is $\chi^2_{\text{red}}(m=1,b=1) = 47.3$, a poor fit, and for our second model is $\chi^2_{\text{red}}(m=2,b=1) = 0.56$, a great fit. If this were a real experiment, the second reduced $\chi^2$ is so good that we would be suspicious, as values less than 1 are unlikely and are generally a sign of over fitting; here, it is just a consequence of how we generated the data.

## How can we find the best parameter values?

Above, we guessed some parameter values and picked the ones that gave us the lowest $\chi^2$, but how can we be sure that this is the lowest *possible* $\chi^2$? Put another way, we would like to find the parameter values that *minimize* the $\chi^2$ value.

(For such a simple minimization, we could find the answer analytically [by solving matrices], but for more complicated models, this is not possible, and we have to use numerical methods that involve guessing. For the purposes of illustration, we will use numerical methods below.)

To ensure we are at a minimum, we will need at least three guesses for the $\chi^2$. To keep things simple, we will only try to minimize the $\chi^2$ with respect to the $m$ parameter---we will keep $b$ fixed at 1. We then make a third guess for $m$, say $m=3$:

| $i$ | $x_i$ | $y_i$ | $\sigma_i$ | $f(x_i,m=1,b=1)$ | $f(x_i,m=2,b=1)$ | $f(x_i,m=3,b=1)$ |
|-----|-------|-------|------------|------------------|------------------| -----------------|
| 1   | 0.00  | 1.03  | 0.1        | 1.00             | 1.00             | 1.00            |
| 2   | 0.11  | 1.30  | 0.1        | 1.11             | 1.22             | 1.33            |
| 3   | 0.22  | 1.48  | 0.1        | 1.22             | 1.44             | 1.67            |
| 4   | 0.33  | 1.54  | 0.1        | 1.33             | 1.67             | 2.00            |
| 5   | 0.44  | 1.98  | 0.1        | 1.44             | 1.89             | 2.33            |
| 6   | 0.56  | 2.16  | 0.1        | 1.56             | 2.11             | 2.67            |
| 7   | 0.67  | 2.28  | 0.1        | 1.67             | 2.33             | 3.00            |
| 8   | 0.78  | 2.61  | 0.1        | 1.78             | 2.56             | 3.33            |
| 9   | 0.89  | 2.81  | 0.1        | 1.89             | 2.78             | 3.67            |
| 10  | 1.00  | 3.03  | 0.1        | 2.00             | 3.00             | 4.00            |

and calculate the $\chi^2$ statistic, $\chi^2 = 334$. We can now plot the $\chi^2$ values for our three guesses:

<figure>
<img src="{{site.baseurl}}/fig/linear_data_chi2.png" title="The $\chi^2$ values for our three guesses of $m$."/>
</figure>

It looks like the $\chi^2_{\text{red}}$ value does have a minimum between $m=1$ and $m=3$, possibly at $m=2$. To find it exactly, we can draw a parabola that crosses the three points:

<figure>
<img src="{{site.baseurl}}/fig/linear_data_chi2_parabola.png" title="The $\chi^2$ values for our three guesses of $m$ with a parabola drawn through them."/>
</figure>

We then calculate the minimum of the parabola to be at $m=m_0=2.03$. If we calculate the reduced $\chi^2$ value for this guess, we find $\chi^2_{\text{red}}(m=2.03,b=1) = 0.52$, which is slightly better than our previous best guess, $\chi^2_{\text{red}}(m=2,b=1) = 0.56$. We have now minimized our $\chi^2$ as a function of $m$:

<figure>
<img src="{{site.baseurl}}/fig/linear_data_fit_comparison.png" title="Data and the best fit (along with the previous best)."/>
</figure>

How confident are we that this is the best value of $m$? We can calculate an error for our minimized value based on the width of the parabola, which we determine by finding the value of $m$ at $\text{min}(\chi^2) + 1$ and taking the absolute difference between this value and $m_0$:

<figure>
<img src="{{site.baseurl}}/fig/linear_data_chi2_parabola_width.png" title="The $\chi^2$ values for our three guesses of $m$ with a parabola drawn through them and the minimized value of $m$, along with its error."/>
</figure>

In our case, we find $m=2.03 \pm 0.05$:

<figure>
<img src="{{site.baseurl}}/fig/linear_data_fit_comparison_with_error.png" title="Data and the best fit (along with the previous best), shown with error bands."/>
</figure>

## Choosing trial parameters

In the above example, we chose $m=1$, $m=2$, and $m=3$ as our trial values based on how our model appeared to align with the data in the plot. This was fine for our simple example here, but for more complicated models, we would want to define an algorithmic way of determining which parameter values to guess.

One such algorithm is called "gradient descent", which involves taking the derivative of the $\chi^2$ function with respect to the parameters to be minimized and adjusting them in the direction of smaller $\chi^2$ values. Another algorithm is called "Newton's method", which involves adjusting parameter values based on second-derivatives. A discussion of these algorithms is beyond the scope of this episode, but they will be used behind the scenes when we start with the `iminuit` package below.

## Is this really the minimum?

We have certainly found *a* minimum for $\chi^2(m)$, but is it *the* minimum? How can we be sure there is not another, lower value of the $\chi^2$ somewhere else that we did not check? Well, for simple minimization problems with analytical solutions, like the one above, we could have solved for the minimum explicitly, but for more complicated models, this may not be possible. In these cases, we *won't* be sure that we have really found the absolute "global" minimum, only that we have found a "local" one (or something close to it)---the only way to be really sure would be to check every possible value for the parameters, which we can't do with finite computing resources. It is therefore important to start our minimization from a reasonable location by choosing appropriate guesses for our parameters; this helps give us confidence that we have arrived at an appropriate solution.

## What is `iminuit`?

`iminuit` is a Jupyter-friendly python interface to `Minuit2`, a piece of software written in C++ that combines a minimizer (MIGRAD) and error calculators (HESSE and MINOS). It takes an analytical function, which has several parameters, and a guess for the initial parameter values; it then finds a local minimum of this function. It can also compute uncertainty estimates for the parameters.

Let's use `iminuit` to repeat the fit process above. First, let us generate the same dataset as before:

~~~
# our line model
import numpy as np

def line(x, b, m):
    return b + m * x

# generate random toy data with random offsets in y
rng = np.random.default_rng(1)
data_x = np.linspace(0, 1, 10)
data_yerr = 0.1  # could also be an array
data_y = rng.normal(line(data_x, 1, 2), data_yerr)
~~~
{: .language-python}

Now, we could define our own $\chi^2$ function and minimize it using `iminuit`, but `iminuit` includes a number of cost functions, including `LeastSquares`, which, as we said above, is effectively another name for the $\chi^2$ function in our case. We import it from `iminuit.cost` and then tell it to use our dataset and model:

~~~
from iminuit.cost import LeastSquares

least_squares = LeastSquares(data_x, data_y, data_yerr, line)
~~~
{: .language-python}

The `iminuit` docs include a [tutorial on cost functions](https://scikit-hep.org/iminuit/notebooks/cost_functions.html) that goes into more detail on how to use them.

Now we can perform the minimization:

~~~
# minimization in iminuit is handled through the Minuit class, so we import it
from iminuit import Minuit

# we then initialize the minimizer
# this does not actually perform any minimization yet, just makes sure it is set up properly
# notice that we can pass it starting values for our parameters by name
m = Minuit(least_squares, m=1, b=1)

# now we perform the minimization
m.migrad()  # minimizes least_squares
m.hesse()  # computes the parameter uncertainties
~~~
{: .language-python}

This should produce output like the following:

<table>
    <tr>
        <th colspan="2" style="text-align:center" title="Minimizer"> Migrad </th>
    </tr>
    <tr>
        <td style="text-align:left" title="Minimum value of function"> FCN = 3.959 (χ²/ndof = 0.5) </td>
        <td style="text-align:center" title="Total number of function and (optional) gradient evaluations"> Nfcn = 42 </td>
    </tr>
    <tr>
        <td style="text-align:left" title="Estimated distance to minimum and goal"> EDM = 5.56e-21 (Goal: 0.0002) </td>
        <td style="text-align:center" title="Total run time of algorithms">  </td>
    </tr>
    <tr>
        <td style="text-align:center;background-color:#92CCA6;color:black"> Valid Minimum </td>
        <td style="text-align:center;background-color:#92CCA6;color:black"> Below EDM threshold (goal x 10) </td>
    </tr>
    <tr>
        <td style="text-align:center;background-color:#92CCA6;color:black"> No parameters at limit </td>
        <td style="text-align:center;background-color:#92CCA6;color:black"> Below call limit </td>
    </tr>
    <tr>
        <td style="text-align:center;background-color:#92CCA6;color:black"> Hesse ok </td>
        <td style="text-align:center;background-color:#92CCA6;color:black"> Covariance accurate </td>
    </tr>
</table><table>
    <tr>
        <td></td>
        <th title="Variable name"> Name </th>
        <th title="Value of parameter"> Value </th>
        <th title="Hesse error"> Hesse Error </th>
        <th title="Minos lower error"> Minos Error- </th>
        <th title="Minos upper error"> Minos Error+ </th>
        <th title="Lower limit of the parameter"> Limit- </th>
        <th title="Upper limit of the parameter"> Limit+ </th>
        <th title="Is the parameter fixed in the fit"> Fixed </th>
    </tr>
    <tr>
        <th> 0 </th>
        <td> b </td>
        <td> 1.02 </td>
        <td> 0.06 </td>
        <td>  </td>
        <td>  </td>
        <td>  </td>
        <td>  </td>
        <td>  </td>
    </tr>
    <tr>
        <th> 1 </th>
        <td> m </td>
        <td> 2.0 </td>
        <td> 0.1 </td>
        <td>  </td>
        <td>  </td>
        <td>  </td>
        <td>  </td>
        <td>  </td>
    </tr>
</table><table>
    <tr>
        <td></td>
        <th> b </th>
        <th> m </th>
    </tr>
    <tr>
        <th> b </th>
        <td> 0.00345 </td>
        <td style="background-color:rgb(140,140,250);color:black"> -0.0049 <strong>(-0.843)</strong> </td>
    </tr>
    <tr>
        <th> m </th>
        <td style="background-color:rgb(140,140,250);color:black"> -0.0049 <strong>(-0.843)</strong> </td>
        <td> 0.00982 </td>
    </tr>
</table><?xml version="1.0" encoding="utf-8" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN"
  "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg xmlns:xlink="http://www.w3.org/1999/xlink" width="360pt" height="288pt" viewBox="0 0 360 288" xmlns="http://www.w3.org/2000/svg" version="1.1">
 <metadata>
  <rdf:RDF xmlns:dc="http://purl.org/dc/elements/1.1/" xmlns:cc="http://creativecommons.org/ns#" xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#">
   <cc:Work>
    <dc:type rdf:resource="http://purl.org/dc/dcmitype/StillImage"/>
    <dc:date>2024-07-01T08:46:51.005280</dc:date>
    <dc:format>image/svg+xml</dc:format>
    <dc:creator>
     <cc:Agent>
      <dc:title>Matplotlib v3.7.3, https://matplotlib.org/</dc:title>
     </cc:Agent>
    </dc:creator>
   </cc:Work>
  </rdf:RDF>
 </metadata>
 <defs>
  <style type="text/css">*{stroke-linejoin: round; stroke-linecap: butt}</style>
 </defs>
 <g id="figure_1">
  <g id="patch_1">
   <path d="M 0 288 
L 360 288 
L 360 0 
L 0 0 
z
" style="fill: #ffffff"/>
  </g>
  <g id="axes_1">
   <g id="patch_2">
    <path d="M 25.903365 268.321635 
L 356.99976 268.321635 
L 356.99976 3.00024 
L 25.903365 3.00024 
z
" style="fill: #ffffff"/>
   </g>
   <g id="matplotlib.axis_1">
    <g id="xtick_1">
     <g id="line2d_1">
      <defs>
       <path id="m552b1f90de" d="M 0 0 
L 0 3.5 
" style="stroke: #000000; stroke-width: 0.8"/>
      </defs>
      <g>
       <use xlink:href="#m552b1f90de" x="40.953201" y="268.321635" style="stroke: #000000; stroke-width: 0.8"/>
      </g>
     </g>
     <g id="text_1">
      <!-- 0.0 -->
      <g transform="translate(33.001639 282.920073) scale(0.1 -0.1)">
       <defs>
        <path id="DejaVuSans-30" d="M 2034 4250 
Q 1547 4250 1301 3770 
Q 1056 3291 1056 2328 
Q 1056 1369 1301 889 
Q 1547 409 2034 409 
Q 2525 409 2770 889 
Q 3016 1369 3016 2328 
Q 3016 3291 2770 3770 
Q 2525 4250 2034 4250 
z
M 2034 4750 
Q 2819 4750 3233 4129 
Q 3647 3509 3647 2328 
Q 3647 1150 3233 529 
Q 2819 -91 2034 -91 
Q 1250 -91 836 529 
Q 422 1150 422 2328 
Q 422 3509 836 4129 
Q 1250 4750 2034 4750 
z
" transform="scale(0.015625)"/>
        <path id="DejaVuSans-2e" d="M 684 794 
L 1344 794 
L 1344 0 
L 684 0 
L 684 794 
z
" transform="scale(0.015625)"/>
       </defs>
       <use xlink:href="#DejaVuSans-30"/>
       <use xlink:href="#DejaVuSans-2e" x="63.623047"/>
       <use xlink:href="#DejaVuSans-30" x="95.410156"/>
      </g>
     </g>
    </g>
    <g id="xtick_2">
     <g id="line2d_2">
      <g>
       <use xlink:href="#m552b1f90de" x="101.152546" y="268.321635" style="stroke: #000000; stroke-width: 0.8"/>
      </g>
     </g>
     <g id="text_2">
      <!-- 0.2 -->
      <g transform="translate(93.200983 282.920073) scale(0.1 -0.1)">
       <defs>
        <path id="DejaVuSans-32" d="M 1228 531 
L 3431 531 
L 3431 0 
L 469 0 
L 469 531 
Q 828 903 1448 1529 
Q 2069 2156 2228 2338 
Q 2531 2678 2651 2914 
Q 2772 3150 2772 3378 
Q 2772 3750 2511 3984 
Q 2250 4219 1831 4219 
Q 1534 4219 1204 4116 
Q 875 4013 500 3803 
L 500 4441 
Q 881 4594 1212 4672 
Q 1544 4750 1819 4750 
Q 2544 4750 2975 4387 
Q 3406 4025 3406 3419 
Q 3406 3131 3298 2873 
Q 3191 2616 2906 2266 
Q 2828 2175 2409 1742 
Q 1991 1309 1228 531 
z
" transform="scale(0.015625)"/>
       </defs>
       <use xlink:href="#DejaVuSans-30"/>
       <use xlink:href="#DejaVuSans-2e" x="63.623047"/>
       <use xlink:href="#DejaVuSans-32" x="95.410156"/>
      </g>
     </g>
    </g>
    <g id="xtick_3">
     <g id="line2d_3">
      <g>
       <use xlink:href="#m552b1f90de" x="161.35189" y="268.321635" style="stroke: #000000; stroke-width: 0.8"/>
      </g>
     </g>
     <g id="text_3">
      <!-- 0.4 -->
      <g transform="translate(153.400328 282.920073) scale(0.1 -0.1)">
       <defs>
        <path id="DejaVuSans-34" d="M 2419 4116 
L 825 1625 
L 2419 1625 
L 2419 4116 
z
M 2253 4666 
L 3047 4666 
L 3047 1625 
L 3713 1625 
L 3713 1100 
L 3047 1100 
L 3047 0 
L 2419 0 
L 2419 1100 
L 313 1100 
L 313 1709 
L 2253 4666 
z
" transform="scale(0.015625)"/>
       </defs>
       <use xlink:href="#DejaVuSans-30"/>
       <use xlink:href="#DejaVuSans-2e" x="63.623047"/>
       <use xlink:href="#DejaVuSans-34" x="95.410156"/>
      </g>
     </g>
    </g>
    <g id="xtick_4">
     <g id="line2d_4">
      <g>
       <use xlink:href="#m552b1f90de" x="221.551235" y="268.321635" style="stroke: #000000; stroke-width: 0.8"/>
      </g>
     </g>
     <g id="text_4">
      <!-- 0.6 -->
      <g transform="translate(213.599672 282.920073) scale(0.1 -0.1)">
       <defs>
        <path id="DejaVuSans-36" d="M 2113 2584 
Q 1688 2584 1439 2293 
Q 1191 2003 1191 1497 
Q 1191 994 1439 701 
Q 1688 409 2113 409 
Q 2538 409 2786 701 
Q 3034 994 3034 1497 
Q 3034 2003 2786 2293 
Q 2538 2584 2113 2584 
z
M 3366 4563 
L 3366 3988 
Q 3128 4100 2886 4159 
Q 2644 4219 2406 4219 
Q 1781 4219 1451 3797 
Q 1122 3375 1075 2522 
Q 1259 2794 1537 2939 
Q 1816 3084 2150 3084 
Q 2853 3084 3261 2657 
Q 3669 2231 3669 1497 
Q 3669 778 3244 343 
Q 2819 -91 2113 -91 
Q 1303 -91 875 529 
Q 447 1150 447 2328 
Q 447 3434 972 4092 
Q 1497 4750 2381 4750 
Q 2619 4750 2861 4703 
Q 3103 4656 3366 4563 
z
" transform="scale(0.015625)"/>
       </defs>
       <use xlink:href="#DejaVuSans-30"/>
       <use xlink:href="#DejaVuSans-2e" x="63.623047"/>
       <use xlink:href="#DejaVuSans-36" x="95.410156"/>
      </g>
     </g>
    </g>
    <g id="xtick_5">
     <g id="line2d_5">
      <g>
       <use xlink:href="#m552b1f90de" x="281.750579" y="268.321635" style="stroke: #000000; stroke-width: 0.8"/>
      </g>
     </g>
     <g id="text_5">
      <!-- 0.8 -->
      <g transform="translate(273.799017 282.920073) scale(0.1 -0.1)">
       <defs>
        <path id="DejaVuSans-38" d="M 2034 2216 
Q 1584 2216 1326 1975 
Q 1069 1734 1069 1313 
Q 1069 891 1326 650 
Q 1584 409 2034 409 
Q 2484 409 2743 651 
Q 3003 894 3003 1313 
Q 3003 1734 2745 1975 
Q 2488 2216 2034 2216 
z
M 1403 2484 
Q 997 2584 770 2862 
Q 544 3141 544 3541 
Q 544 4100 942 4425 
Q 1341 4750 2034 4750 
Q 2731 4750 3128 4425 
Q 3525 4100 3525 3541 
Q 3525 3141 3298 2862 
Q 3072 2584 2669 2484 
Q 3125 2378 3379 2068 
Q 3634 1759 3634 1313 
Q 3634 634 3220 271 
Q 2806 -91 2034 -91 
Q 1263 -91 848 271 
Q 434 634 434 1313 
Q 434 1759 690 2068 
Q 947 2378 1403 2484 
z
M 1172 3481 
Q 1172 3119 1398 2916 
Q 1625 2713 2034 2713 
Q 2441 2713 2670 2916 
Q 2900 3119 2900 3481 
Q 2900 3844 2670 4047 
Q 2441 4250 2034 4250 
Q 1625 4250 1398 4047 
Q 1172 3844 1172 3481 
z
" transform="scale(0.015625)"/>
       </defs>
       <use xlink:href="#DejaVuSans-30"/>
       <use xlink:href="#DejaVuSans-2e" x="63.623047"/>
       <use xlink:href="#DejaVuSans-38" x="95.410156"/>
      </g>
     </g>
    </g>
    <g id="xtick_6">
     <g id="line2d_6">
      <g>
       <use xlink:href="#m552b1f90de" x="341.949924" y="268.321635" style="stroke: #000000; stroke-width: 0.8"/>
      </g>
     </g>
     <g id="text_6">
      <!-- 1.0 -->
      <g transform="translate(333.998361 282.920073) scale(0.1 -0.1)">
       <defs>
        <path id="DejaVuSans-31" d="M 794 531 
L 1825 531 
L 1825 4091 
L 703 3866 
L 703 4441 
L 1819 4666 
L 2450 4666 
L 2450 531 
L 3481 531 
L 3481 0 
L 794 0 
L 794 531 
z
" transform="scale(0.015625)"/>
       </defs>
       <use xlink:href="#DejaVuSans-31"/>
       <use xlink:href="#DejaVuSans-2e" x="63.623047"/>
       <use xlink:href="#DejaVuSans-30" x="95.410156"/>
      </g>
     </g>
    </g>
   </g>
   <g id="matplotlib.axis_2">
    <g id="ytick_1">
     <g id="line2d_7">
      <defs>
       <path id="m6342fc0d8e" d="M 0 0 
L -3.5 0 
" style="stroke: #000000; stroke-width: 0.8"/>
      </defs>
      <g>
       <use xlink:href="#m6342fc0d8e" x="25.903365" y="249.069938" style="stroke: #000000; stroke-width: 0.8"/>
      </g>
     </g>
     <g id="text_7">
      <!-- 1.0 -->
      <g transform="translate(3.00024 252.869156) scale(0.1 -0.1)">
       <use xlink:href="#DejaVuSans-31"/>
       <use xlink:href="#DejaVuSans-2e" x="63.623047"/>
       <use xlink:href="#DejaVuSans-30" x="95.410156"/>
      </g>
     </g>
    </g>
    <g id="ytick_2">
     <g id="line2d_8">
      <g>
       <use xlink:href="#m6342fc0d8e" x="25.903365" y="194.122962" style="stroke: #000000; stroke-width: 0.8"/>
      </g>
     </g>
     <g id="text_8">
      <!-- 1.5 -->
      <g transform="translate(3.00024 197.922181) scale(0.1 -0.1)">
       <defs>
        <path id="DejaVuSans-35" d="M 691 4666 
L 3169 4666 
L 3169 4134 
L 1269 4134 
L 1269 2991 
Q 1406 3038 1543 3061 
Q 1681 3084 1819 3084 
Q 2600 3084 3056 2656 
Q 3513 2228 3513 1497 
Q 3513 744 3044 326 
Q 2575 -91 1722 -91 
Q 1428 -91 1123 -41 
Q 819 9 494 109 
L 494 744 
Q 775 591 1075 516 
Q 1375 441 1709 441 
Q 2250 441 2565 725 
Q 2881 1009 2881 1497 
Q 2881 1984 2565 2268 
Q 2250 2553 1709 2553 
Q 1456 2553 1204 2497 
Q 953 2441 691 2322 
L 691 4666 
z
" transform="scale(0.015625)"/>
       </defs>
       <use xlink:href="#DejaVuSans-31"/>
       <use xlink:href="#DejaVuSans-2e" x="63.623047"/>
       <use xlink:href="#DejaVuSans-35" x="95.410156"/>
      </g>
     </g>
    </g>
    <g id="ytick_3">
     <g id="line2d_9">
      <g>
       <use xlink:href="#m6342fc0d8e" x="25.903365" y="139.175987" style="stroke: #000000; stroke-width: 0.8"/>
      </g>
     </g>
     <g id="text_9">
      <!-- 2.0 -->
      <g transform="translate(3.00024 142.975206) scale(0.1 -0.1)">
       <use xlink:href="#DejaVuSans-32"/>
       <use xlink:href="#DejaVuSans-2e" x="63.623047"/>
       <use xlink:href="#DejaVuSans-30" x="95.410156"/>
      </g>
     </g>
    </g>
    <g id="ytick_4">
     <g id="line2d_10">
      <g>
       <use xlink:href="#m6342fc0d8e" x="25.903365" y="84.229012" style="stroke: #000000; stroke-width: 0.8"/>
      </g>
     </g>
     <g id="text_10">
      <!-- 2.5 -->
      <g transform="translate(3.00024 88.028231) scale(0.1 -0.1)">
       <use xlink:href="#DejaVuSans-32"/>
       <use xlink:href="#DejaVuSans-2e" x="63.623047"/>
       <use xlink:href="#DejaVuSans-35" x="95.410156"/>
      </g>
     </g>
    </g>
    <g id="ytick_5">
     <g id="line2d_11">
      <g>
       <use xlink:href="#m6342fc0d8e" x="25.903365" y="29.282037" style="stroke: #000000; stroke-width: 0.8"/>
      </g>
     </g>
     <g id="text_11">
      <!-- 3.0 -->
      <g transform="translate(3.00024 33.081255) scale(0.1 -0.1)">
       <defs>
        <path id="DejaVuSans-33" d="M 2597 2516 
Q 3050 2419 3304 2112 
Q 3559 1806 3559 1356 
Q 3559 666 3084 287 
Q 2609 -91 1734 -91 
Q 1441 -91 1130 -33 
Q 819 25 488 141 
L 488 750 
Q 750 597 1062 519 
Q 1375 441 1716 441 
Q 2309 441 2620 675 
Q 2931 909 2931 1356 
Q 2931 1769 2642 2001 
Q 2353 2234 1838 2234 
L 1294 2234 
L 1294 2753 
L 1863 2753 
Q 2328 2753 2575 2939 
Q 2822 3125 2822 3475 
Q 2822 3834 2567 4026 
Q 2313 4219 1838 4219 
Q 1578 4219 1281 4162 
Q 984 4106 628 3988 
L 628 4550 
Q 988 4650 1302 4700 
Q 1616 4750 1894 4750 
Q 2613 4750 3031 4423 
Q 3450 4097 3450 3541 
Q 3450 3153 3228 2886 
Q 3006 2619 2597 2516 
z
" transform="scale(0.015625)"/>
       </defs>
       <use xlink:href="#DejaVuSans-33"/>
       <use xlink:href="#DejaVuSans-2e" x="63.623047"/>
       <use xlink:href="#DejaVuSans-30" x="95.410156"/>
      </g>
     </g>
    </g>
   </g>
   <g id="LineCollection_1">
    <path d="M 40.953201 256.261572 
L 40.953201 234.282781 
" clip-path="url(#pc82e3728a3)" style="fill: none; stroke: #000000; stroke-width: 1.5"/>
    <path d="M 74.397281 226.609369 
L 74.397281 204.630578 
" clip-path="url(#pc82e3728a3)" style="fill: none; stroke: #000000; stroke-width: 1.5"/>
    <path d="M 107.841362 207.586273 
L 107.841362 185.607483 
" clip-path="url(#pc82e3728a3)" style="fill: none; stroke: #000000; stroke-width: 1.5"/>
    <path d="M 141.285442 201.117609 
L 141.285442 179.138819 
" clip-path="url(#pc82e3728a3)" style="fill: none; stroke: #000000; stroke-width: 1.5"/>
    <path d="M 174.729522 152.426508 
L 174.729522 130.447718 
" clip-path="url(#pc82e3728a3)" style="fill: none; stroke: #000000; stroke-width: 1.5"/>
    <path d="M 208.173603 133.049557 
L 208.173603 111.070767 
" clip-path="url(#pc82e3728a3)" style="fill: none; stroke: #000000; stroke-width: 1.5"/>
    <path d="M 241.617683 119.434857 
L 241.617683 97.456067 
" clip-path="url(#pc82e3728a3)" style="fill: none; stroke: #000000; stroke-width: 1.5"/>
    <path d="M 275.061763 82.727051 
L 275.061763 60.748261 
" clip-path="url(#pc82e3728a3)" style="fill: none; stroke: #000000; stroke-width: 1.5"/>
    <path d="M 308.505844 60.68588 
L 308.505844 38.707089 
" clip-path="url(#pc82e3728a3)" style="fill: none; stroke: #000000; stroke-width: 1.5"/>
    <path d="M 341.949924 37.039094 
L 341.949924 15.060303 
" clip-path="url(#pc82e3728a3)" style="fill: none; stroke: #000000; stroke-width: 1.5"/>
   </g>
   <g id="line2d_12">
    <path d="M 40.953201 246.427692 
L 78.577791 218.996864 
L 116.202382 191.566037 
L 153.826972 164.135209 
L 191.451562 136.704382 
L 229.076153 109.273554 
L 266.700743 81.842727 
L 304.325334 54.411899 
L 341.949924 26.981071 
" clip-path="url(#pc82e3728a3)" style="fill: none; stroke: #1f77b4; stroke-width: 1.5; stroke-linecap: square"/>
   </g>
   <g id="line2d_13">
    <defs>
     <path id="maf82cec444" d="M 0 3 
C 0.795609 3 1.55874 2.683901 2.12132 2.12132 
C 2.683901 1.55874 3 0.795609 3 0 
C 3 -0.795609 2.683901 -1.55874 2.12132 -2.12132 
C 1.55874 -2.683901 0.795609 -3 0 -3 
C -0.795609 -3 -1.55874 -2.683901 -2.12132 -2.12132 
C -2.683901 -1.55874 -3 -0.795609 -3 0 
C -3 0.795609 -2.683901 1.55874 -2.12132 2.12132 
C -1.55874 2.683901 -0.795609 3 0 3 
z
" style="stroke: #000000"/>
    </defs>
    <g clip-path="url(#pc82e3728a3)">
     <use xlink:href="#maf82cec444" x="40.953201" y="245.272177" style="stroke: #000000"/>
     <use xlink:href="#maf82cec444" x="74.397281" y="215.619973" style="stroke: #000000"/>
     <use xlink:href="#maf82cec444" x="107.841362" y="196.596878" style="stroke: #000000"/>
     <use xlink:href="#maf82cec444" x="141.285442" y="190.128214" style="stroke: #000000"/>
     <use xlink:href="#maf82cec444" x="174.729522" y="141.437113" style="stroke: #000000"/>
     <use xlink:href="#maf82cec444" x="208.173603" y="122.060162" style="stroke: #000000"/>
     <use xlink:href="#maf82cec444" x="241.617683" y="108.445462" style="stroke: #000000"/>
     <use xlink:href="#maf82cec444" x="275.061763" y="71.737656" style="stroke: #000000"/>
     <use xlink:href="#maf82cec444" x="308.505844" y="49.696484" style="stroke: #000000"/>
     <use xlink:href="#maf82cec444" x="341.949924" y="26.049698" style="stroke: #000000"/>
    </g>
   </g>
   <g id="patch_3">
    <path d="M 25.903365 268.321635 
L 25.903365 3.00024 
" style="fill: none; stroke: #000000; stroke-width: 0.8; stroke-linejoin: miter; stroke-linecap: square"/>
   </g>
   <g id="patch_4">
    <path d="M 356.99976 268.321635 
L 356.99976 3.00024 
" style="fill: none; stroke: #000000; stroke-width: 0.8; stroke-linejoin: miter; stroke-linecap: square"/>
   </g>
   <g id="patch_5">
    <path d="M 25.903365 268.321635 
L 356.99976 268.321635 
" style="fill: none; stroke: #000000; stroke-width: 0.8; stroke-linejoin: miter; stroke-linecap: square"/>
   </g>
   <g id="patch_6">
    <path d="M 25.903365 3.00024 
L 356.99976 3.00024 
" style="fill: none; stroke: #000000; stroke-width: 0.8; stroke-linejoin: miter; stroke-linecap: square"/>
   </g>
  </g>
 </g>
 <defs>
  <clipPath id="pc82e3728a3">
   <rect x="25.903365" y="3.00024" width="331.096395" height="265.321395"/>
  </clipPath>
 </defs>
</svg>

{% include links.md %}