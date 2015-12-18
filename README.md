---
layout: page
title: Project Pages Read Me
permalink: /README/
---
These are the Romana Project Pages hosted on GitHub and available at [romana.io](romana.io) (and [romana.github.io](romana.github.io))

The site is based on a Jekyll Material Lite blog [template]( https://github.com/mudpuddle/jekyll-mdl), with some modifications to accommodate our static content requirements. We have changed their blog format to a text heavy site with direct navigation.

Each page is a Jekyll markdown file using the 'page' layout template. Navigation is organized into folders. The top nav bar is built by Jekyll. Each folder gets it's own nav button with a drop down menu with links to individual pages. Placement of the nav and the entries in the menus is derived from the values of 'firstnav' and 'secondnav' tags on the page. Firstnav sets the position of the button across the top nav from left to right. Secondnav sets the position in the menu. 

There are also separate fields for the nav text and menu item text so they can be different from the page Title.  

The string for nav_text is set from the value from the page of the first menu item (i.e. secondnav=1) values on other pages is ignored. When there are no menu items, set secondnav=9 and the nav_text string will be set without adding a menu item below.

firstnav = 0; is the homepage (i.e. no nav or menu item)
secondnav = 1; takes nav_text as text for the nav button, menu item added
secondnav = 9; takes nav_text as text for the nav button, no menu item added

The sidenav buttons are generated from these values in a similar way, but there are no menu items on the sidenav.

There are no checks for duplicates, etc. so check to be sure what actually gets rendered is what you want.

Permalinks are used for each page so we have clean urls. Links have the form of /folder/page/, where 'page' is 'romana_' for SEO visability.


To comment on these pages, make a branch of this repository and make your comments directly in the page markup file. Generate a pull request so everyone to see your comments.

If you want to render the site locally, you will have to install Jekyll on your laptop. See details [here](help.github.com/articles/using-jekyll-with-pages/).

See the [issues pages](https://github.com/romana/romana.github.io/issues) for the list of known issues.
