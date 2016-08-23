---
layout: post
title:  "EncodeURIComponent VS EncodeURI"
date:   2016-08-23 11:25:49 +0800
categories: programming javascript
---
    var world = "A string with symbols / characters that have special meaning?";
    var uri = 'http://example.com/foo?hello=' + encodeURIComponent(world);