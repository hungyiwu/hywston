# Learn to make web front-end using D3.js

- repo: https://github.com/hungyiwu/md-serve
- product page: https://hungyiwu.github.io/md-serve/

## Why I made this

This is a side project to practice D3.js. Prior to this point, I have not spent time learning javascript to the productive level. Recent work experience has led me to the realization that javascript is not just about pretty, animated UI, but also about the logic. In addition, to push any service to the client/edge side, javascript is almost inevitable. Picking it up completed my skillset to make a minimal viable product, from the core machine learning, back-end API, to front-end UI, all by myself.

This project focuses at the front-end UI, where the machine learning logic was done in a previous project ([Mixed-distance](https://github.com/hungyiwu/mixed-distance)) and the back-end API was done via GitHub Pages.

## Lessons learned

Initially I wrote a small back-end using python flask in order to launch the dev server locally. A few things learned:

1. when debugging javascript code separated from html, disable browser cache

2. co-developing front-end and back-end has made me think about API design

Initially I had 3 APIs:
- `GET /wsi/list` returns a list of all available whole-slide image (WSI) IDs
- `GET /wsi/<string:wsi_id>/fraction/list` returns a list of all available fractions for a WSI ID
- `GET /wsi/<string:wsi_id>/fraction/<string:fraction>` returns a list of data points for a WSI ID at a fraction

...which requires front-end API calls to be nested due to the asynchronous nature:
```
// prepare chart at landing page using first WSI ID of the returned WSI ID list
// and the frist fraction of that WSI ID
d3.json("/wsi/list").then(function (wsiList) {
  createDropDownMenu(wsiList);
  setDropDownMenuToValue(wsiList[0]);
  d3.json(`/wsi/${wsiList[0]}/fraction/list`).then(function (fractionList) {
    createSlider(fractionList);
    setSliderToValue(fractionList[0]);
    d3.json(`/wsi/#{wsiList[0]}/fraction/${fractionList[0]}`).then(function (data) {
      drawChart(d3.select("#chart"), data, false);  // third argument is bool, true when just updating with the same WSI ID
    });
  });
});

d3.select("#dropDown").on("input", function () {
  // call API 3 to get data
});

d3.select("#slider").on("input", function () {
  // call API 3 to get data
});
```

Another drawback is that the user story includes frequent sliding of the slider; pick one, then switch to other WSIs to confirm the fraction value is generalizable. That API design means one API call per slider action.

I realized it makes the front-end flow easier if there are only two APIs:
- `GET /wsi/list` returns a list of all available WSI IDs
- `GET /wsi/<string:wsi_id>` returns data of all fractions

...then the front-end logic becomes:

```
// prepare chart at landing page using first WSI ID of the returned WSI ID list
// and the frist fraction of that WSI ID
d3.json("/wsi/list").then(function (wsiList) {
  createDropDownMenu(wsiList);
  setDropDownMenuToValue(wsiList[0]);
  d3.json(`/wsi/${wsiList[0]}`).then(function (data) {
    createSlider(data.fraction);
    setSliderToValue(data.fraction[0]);
    drawChart(d3.select("#chart"), data.fraction[0], false);  // third argument is bool, true when just updating with the same WSI ID
    d3.select("#slider").on("input", function () {
      // reuse the `data` variable
    });
  });
  
  d3.select("#dropDown").on("input", function () {
    // call API 2 to get data
    d3.select("#slider").on("input", function () {
      // reuse data
    });
  });
});
```

Now there's one less API call at landing page rendering and only one API call if switching WSI; no API call when sliding.
