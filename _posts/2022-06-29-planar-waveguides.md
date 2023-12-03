---
title: "Multilayered planar waveguide solver with the transfer-matrix method"
#tags: TeXt
key: planar-waveguides
mathjax: true
cover: /assets/images/waveguides/screenshot_waveguides.png
permalink: /planar-waveguides/

---

With this applet, you will be able to calculate the guided modes on planar multilayer structures. It calculates the effective index $n_{eff}$ for all TE and TM modes, and also the electric field mode profile of each mode.

## Calculation method

One method of calculating the mode spectrum is to solve the differential equation considering the index profile of the structure. This method is most appropriate for the single-layer case, as the solution only requires the boundary conditions at both interfaces. However, for an arbitrary number of layers, it is not that simple, but we can take advantage of the multilayer algorithm we developed in the previous applet. That applet calculates the reflection spectrum of a multilayer structure. How can we obtain the guided modes from that routine?

## Guided modes with the transfer-matrix method

In [the previous app](/multilayer/), we calculated a matrix that links the electric fields propagating towards positive and negative $z$ values, represented as $E^{+}$ and $E^{-}$ respectively.

$$
\begin{pmatrix}
E_{0}^{+}\\
E_{0}^{-}
\end{pmatrix}
=
\begin{pmatrix}
t_{11}&t_{12}\\
t_{21}&t_{22}
\end{pmatrix}
\begin{pmatrix}
E_{N}^{+}\\
0
\end{pmatrix}
$$

This equation allows us to calculate the transmission and reflection coefficients when there is incident light coming from negative *z*. However, what happens with guided modes? A guided mode does not require light coming from the left or right, as light is already guided inside. So for guided modes, the previous equation becomes:

$$
\begin{pmatrix}
0\\
E_{0}^{-}
\end{pmatrix}
=
\begin{pmatrix}
t_{11}&t_{12}\\
t_{21}&t_{22}
\end{pmatrix}
\begin{pmatrix}
E_{N}^{+}\\
0
\end{pmatrix}
$$

This means that $t_{11}E_{N}^{+}=0$, which has the trivial solution of $E_{N}^{+}=0$, but can have non-zero solutions if $t_{11}=0$. Actually, this is the condition for guided modes to exist. However this condition only applies to specific effective index values. So in order to find the modes, we just need to sweep $t_{11}$ through all possible propagation angles and find the zeros of this function. For doing this, we can define a useful magnitude called *effective index*, which is defined as:

$$
n_{eff}=n_0 \sin (\theta_0) = n_i \sin (\theta_i)
$$

where $n_0$ and $\theta_0$ are the refractive index and incidence angle at the incident layer, and $n_i$ and $\theta_i$ are the same parameters for every other layer, as this magnitude is conserved as we know from Snell's law. However, as we said before, guided modes do not have incident light, so how can we assign a value to it? The answer relies in the fact that for effective indices higher than the incident medium, $\sin(\theta_0) > 1 $ which makes the angle imaginary. This is precisely what happens when we have total internal reflection from inside, light cannot refract out of the layer because its propagation angle becomes imaginary. This is indeed a necessary guiding condition, so it makes sense. This means that in order to find the guided modes in our structure, instead of sweeping the incident angle, we should sweep the effective index between the highest index in our structure (higher than that we would have imaginary propagation angle in all layers, which is not possible) and the refrative index of the incident or substrate medium, whichever is higher. For example, a multilayer which contains air at the top, some layers of silicon ($ n \approx 3.5$) and silicon nitride ($ n \approx 2.0$) and a silica substrate ($ n \approx 1.45 $), then we should sweep $n_{eff}$ betweem 1.45 and 3.5 and find the zeros of $t_{11}(n_{eff})$ to find all guided modes.

This is precisely what this applet does, it sends to our multilayer routine a sweep vector of effective indices covering the whole range, and then looks for sign changes in $t_{11}$, which allows us to identify were the zeros are between those pairs of points, which are quickly found using the Newton's method.

Once the effective indices are calculated, then we can also calculate the electric field profiles of our modes by again applying the transfer matrix method. We just need to calculate the electric fields $E_{i}^{+}$ and $E_{i}^{-}$ at each interface, and then for the regions between interface, just propagate the fields with the plane wave equation accordingly, and sum them. This is what the software does. For the TE modes, the electric fields are always parallel to the surfaces, so no more calculations are needed, but for the TM modes, the electric fields obtained are tilted, so before summing them we first have to calculate the $z$ component by multiplying by $\sin(\theta_i))$ and *voila*:

$$
E_z^{(TM)}=(E^{+}+E^{-})\sin(\theta)
$$

It is worth noting that the profile of the electric fields in this kind of structures can only have two possible shapes in each layer. If the refractive index of that layer is *higher* than the effective index, then the propagation angle is real and the electric field is a combination of a sine and a cosine, therefore it has a sinusoidal shape. On the other hand, if the refractive index of that layer is *lower* than the effective index, then the propagation angle is imaginary therefore the function is an evanescent field, which follows an exponential shape. Finally, the software also lists all the effective indices as a table, separating TE and TM modes.

At the interfaces, the boundary conditions of electromagnetic fields apply, which say that for the TE modes, the electric fields are continuous and derivable, while TM modes show a discontinuity proportional to the square of the index contrast.

## Using the software

With this software, the user can tweak the parameters of the multilayered structure, and see the mode spectrum and shape in real time. We encourage the user to see the effect of changing thickness and refractive indices of the different layers and see the consequence in terms of number of modes and effective index. It is obvious that increasing the refractive index or the thickness makes light *fit* better thus the number of modes increases, and the $n_{eff}$ spectrum tends to "accumulate" at the refractive index of that layer. It is also interesting to see how the effective indices of the different modes are related, so that trend is plotted at the right side.

Note that the software only calculates true guided modes. If the substrate has a high index, the software will not find the modes as they would not be truly guided, even though in practice there are still modes. In that case, where the substrate is so far that the light cannot reach it, then the way to find the modes is just to remove the substrate and treat the bottom cladding as substrate.

Some interesting cases to try:

- A single-mode silicon-on-insulator waveguide, which consists of a silicon layer of thickness 220nm surrounded by silica on top and bottom, which is quite a typical thickness value in silicon photonics. This layer should generate just one TE mode and one TM mode. What SOI thickness is necessary for a second TE mode to appear?

- Another interesting case is a so-called *slot waveguide*  which is a waveguide made of two high-index layers sandwiching a thin low-index layer. For TM polarization, the boundary conditions forces a strong field enhancement in the low-index layer, which has some applications for sensing, nonlinear enhancement, or dispersion properties. Try for examples two silicon layers of 100nm thickness sandwiching a 50nm silica layer.

- This software can also be used to calculate laterally-guided 2D modes in cases where the effective index approximation can be applied. This approximation consists of first solving the multilayer problem vertically in the core region and in the cladding region, calculate the effective indices in each case, and then use those effective indices are refractive indices of a new structure in the horizontal direction. Under certain conditions of index-contrast of etch depth, this provides a good approximation of the 2D mode.

The applet should appear below (if it doesn't please contact me).


<!--<a class="button button--primary button--pill" href="https://andtherewaslight-pl-waveguides-b60ae427d299.herokuapp.com/" target="_blank">OPEN APPLET IN NEW TAB</a>-->

<iframe src="https://andtherewaslight-pl-waveguides-b60ae427d299.herokuapp.com/" height="1000px" width="100%" frameBorder="0"></iframe>


---
