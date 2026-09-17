+++
date = '2024-10-20T01:39:32+02:00'
draft = false
title = 'How does this work?'
backgroundImage = 'img/background.svg'
+++

I mean, this is not an issue or whatever but how can it be so hard to make the background image work?

Let's drop some Lorem Ipsum here my friend just checkin' out:

Lorem ipsum dolor sit amet, consectetur adipiscing elit sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

##  The image

![The image](img/background.svg)

Oh so here it is, the image that I was talking about. It's a simple SVG file that I wanted to use as a background image. But it's not working. I'm not sure why but I'll figure it out. I'm sure I will.

## The solution

Well... maybe I keep this test-post here just in case anyone else has the same issue.

The thing was that i was setting the `defaultBackgroundImage` param under the homepage section in the `config.toml` file, when it was supposed to be set in the root of the file.

I might swap to yaml or json for the config file, just to make it easier to read and understand as i dont really like the toml format.

### I almost forgot

I didn't like the default background layout, I found the `Profile` layout to be more appealing. The thing is that the background image doesn't work with that layout, so i made a custom layout taking the profile one as a base and adding the background image to it, feel free to check it out in the `layouts/partials/home` folder.

![alt text](image.png)
