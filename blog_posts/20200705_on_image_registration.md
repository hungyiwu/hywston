# On image registration
I wish there was a blog post charting out some basic stuff on image processing back when I entered this field, so after 1 year of exploration I decided to write one for whoever needs this, starting from image registration.

## very quick rigid registration
A very quick and simple way is to shift (warp) the moving image a bit and calculate the correlation coefficient against the fixed image, something like this:  
```
all_dx = range(-moving_img.shape[0]//2:moving_img.shape[0]//2)
all_dy = range(-moving_img.shape[1]//2:moving_img.shape[1]//2)
dx_grid, dy_grid = np.meshgrid(all_dx, all_dy, indexing='ij')

response = np.zeros(dx_grid.size)
for i, (dx, dy) in enumerate(zip(dx_grid.flatten(), dy_grid.flatten())):
    at = skimage.transform.AffineTransform(translation=(dx,dy))
    test_img = skimage.transform.warp(moving_img, at.inverse)
    response[i] = np.corrcoef(fixed_img.flatten(), test_img.flatten())[0, 1]
    
best_dx, best_dy = np.unravel_index(np.argmax(response), response.shape)
at = skimage.transform.AffineTransform(translation=(best_dx,best_dy))
moved_img = skimage.transform.warp(moving_img, at.inverse)
```
This can be impractically slow as the images get larger, and there's a faster implementation in `skimage` called `skimage.feature.match_template`, using Fourier transform:
```
response = skimage.feature.match_template(image=fixed_img, template=moving_img, pad_input=True)

best_dx, best_dy = np.unravel_index(np.argmax(response), response.shape)
at = skimage.transform.AffineTransform(translation=(best_dx,best_dy))
moved_img = skimage.transform.warp(moving_img, at.inverse)
```
Now the speed issue is solved, we move on to the next one: the smallest shift here is 1 pixel. To do it at sub-pixel steps, there's an even better implementation in `skimage` called `skimage.registration.phase_cross_correlation` [[link](https://scikit-image.org/docs/stable/api/skimage.registration.html#phase-cross-correlation)]  

Combined with another handy function that does polar transformation (`skimage.transform.warp_polar` [[link](https://scikit-image.org/docs/stable/api/skimage.transform.html#skimage.transform.warp_polar)]), you can do translational+rotational (ie. rigid) image registration with just `skimage`. See their tutorial [[link](https://scikit-image.org/docs/stable/auto_examples/registration/plot_register_rotation.html#sphx-glr-auto-examples-registration-plot-register-rotation-py)].

## Going non-linear/local registration
Many cases I encounter at work requires non-linear and/or local registration. `skimage` has an implementation of optical flow [[link](https://scikit-image.org/docs/stable/api/skimage.registration.html#skimage.registration.optical_flow_tvl1)] that belongs to this category.

## Moving forward
IEEE International Symposium on Biomedical Imaging (ISBI) 2019 held a competition on automatic non-rigid histological image registration ([forum.image.sc post](https://forum.image.sc/t/automatic-non-rigid-histological-image-registration-anhir-challenge/21492), [summary paper](https://ieeexplore.ieee.org/document/9058666)). Notably, the benchmarks can be found in this [GitHub repo](https://github.com/Borda/BIRL).

Is this the ImageNet moment for automatic non-rigid histological image registration? How about multi-modal cross-registration (absorption image, fluorescence image)? How about 3D reconstructed images? With more proportion of whole-slide histological images becoming well-aligned at single-cell resolution (1-10 microns), we will get a clearer picture of the power & promise of whole-slide imaging technologies.

Check out my [repo](https://github.com/hungyiwu/tissue_integrity_dashboard) on this issue:  
![](https://raw.githubusercontent.com/hungyiwu/tissue_integrity_dashboard/master/screenshot.png)
