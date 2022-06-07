---
layout: post
title:  "Why does Clipping Happen in *the Clipping Space*"
date:   2022-04-01 21:54:11 +0800
categories: jekyll update
---
Why does clipping happen in the clipping space, i.e. the space after applying projection matrix while before projection dividing (divided by w, and note the w is essentially -z_view), in which the viewing frustum is transformed into a (-w, w) cube. See [this site](http://www.songho.ca/opengl/gl_projectionmatrix.html "SongHo") if you need a recapping of what happened during applying a projection matrix and projection dividing.

~~The main reason is after dividing by w, the original signedness of z_viewspace is no longer preserved (z_clip / w is essentially something like (z_view+a)/(-z_view)), so what originally behind the origin of viewspace (i.e. camera) will now be in front instead. This is definitely not what you want, so clipping has to happen before projection division is necessary as one more dimension (w) is used to preserve the signedness of z_view.~~

The map for z_view -> z_ndc is actually an inverse-propotinal function with some bias on y-axis. So when a point P of a triangle falls in the region z > 0, even only sightly, the z_ndc will become positive infinite, which makes the interpolation of new vertices generated after clipping quite impossible -- unless you transform them back before perspective division, do the interpolation, and then transform back -- which is quite unnecessary.