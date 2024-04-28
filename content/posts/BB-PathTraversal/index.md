---
title: "Bug Bounty: Path Traversal in Snap Creek Duplicator plugin before 1.3.28 for WordPress"
date: 2022-06-15
description: "Bug Bounty: Path Traversal in Snap Creek Duplicator plugin before 1.3.28 for WordPress"
menu:
  sidebar:
    name: "Bug Bounty: Path Traversal in Snap Creek Duplicator plugin before 1.3.28 for WordPress"
    identifier: "Bug Bounty: Path Traversal in Snap Creek Duplicator plugin before 1.3.28 for WordPress"
    weight: 13
---

## The Vulnerability

The Snap Creek Duplicator plugin before 1.3.28 for WordPress (and Duplicator Pro before 3.8.7.1) allows Directory Traversal via ../ in the file parameter to duplicator_download or duplicator_init.

## Intro

Early in my bug bounty journey, I picked a few programs from different platforms to learn how they work and respond to reports and to ultimately decide which platform I would choose to stick to.

Knowing that WordPress plugins were often forgotten to update and a good place to find vulnerabilities I focused on finding WordPress vulnerabilities in the wild. My first real finding ended up being an Exceptional severity vulnerability on Intigriti. Unfortunately, it was already a duplicate. I guess everyone watches for outdated WordPress plugins!

## The Finding

The public program Kinepolis was running WordPress with the outdated Duplicator plugin. I was able to visit the path:

**`/wp-admin/admin-ajax.php?action=duplicator_download&file=%2F..%2Fwp-config.php`**

Which downloaded the wp-config.php file and contained the MySQL settings.

## Lessons Learned

After hearing so much about the vulnerabilities of WordPress I had set out to find something in the wild myself. It took longer than I thought it would but it proved to me that with some determination I would be able to pick a CMS or even a specific vulnerability and hunt for it in the wild just fine, it might take some time but they are there! This has shifted my interest in learning API and GraphQL vulnerabilities since I have seen those in use in most of the programs I follow.
