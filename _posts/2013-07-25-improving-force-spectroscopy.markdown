---
layout: post
title: Improving single molecule force spectroscopy
date: 2013-07-25
comments: false
archive: false
research: true
---
<img src="{{ "/assets/images/fe_detection.jpg" | prepend: site.url }}" alt="Force-spectroscopy resolves the origin of polydA stretching transitions" class="img-rounded img-responsive">
The benefits of single molecule force spectroscopy (SMFS) clearly outweigh the challenges which include small sample sizes, tedious data collection and introduction of human bias during the subjective data selection. These difficulties can be partially eliminated through automation of the experimental data collection process for atomic force microscopy (AFM). 

Automation can be accomplished using an algorithm that triages usable force–extension recordings quickly with positive and negative selection. We implemented an algorithm based on the windowed fast Fourier transform of force–extension traces that identifies peaks using force–extension regimes to correctly identify usable recordings from proteins composed of repeated domains. This algorithm excels as a real-time diagnostic because it involves <30 ms computational time, has high sensitivity and specificity, and efficiently detects weak unfolding events. We used the statistics provided by the automated procedure to clearly demonstrate the properties of molecular adhesion and how these properties change with differences in the cantilever tip and protein functional groups and protein age.
