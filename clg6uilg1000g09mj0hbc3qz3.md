---
title: "Expose Vite's local address"
datePublished: Fri Apr 07 2023 17:53:36 GMT+0000 (Coordinated Universal Time)
cuid: clg6uilg1000g09mj0hbc3qz3
slug: expose-vites-local-address
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1680889637729/03cd9244-9ded-45d2-a194-4c0ba80d3263.jpeg
tags: npm, reactjs, localhost, responsive-web-design, vite

---

> Add `-- --host` to the end of your package manager's start command. Note the extra hyphens!

As a follow-up to my blog on [viewing localhost on mobile](https://blog.benhammond.tech/how-to-view-localhost-on-mobile), I thought I would share how to do the same with Vite specifically. A recent task I completed at work was migrating [healthequitytracker.org](https://healthequitytracker.org) from create-react-app to Vite. In another article, I'll share the details of how this cut our CI build time by 75%, saving the team over 25 hours per month 🚀, but for now, I'll discuss the not-so-obvious way you can have Vite expose the local server's actual IP address (allowing you to view on another device as explained in [my other blog post](https://blog.benhammond.tech/how-to-view-localhost-on-mobile)).

## Updating your package.json scripts

```json
"scripts": {
...
 "start": "vite -- --host"
...
}
```

Vite politely gives you the message `Network: use --host to expose` when you run the dev server, however it doesn't explain HOW to use that flag. Most likely you have a `package.json` folder inside your project, and in that JSON is a property called `"scripts"`. These are the scripts that run when you start your local server with `npm run start` or `npm run dev`, or whatever your team has set up. If you were running the Vite command directly (and not via npm scripts), you would use the flag as they write: `--host`, however, to pass the flag along via npm to Vite, you must include the additional double-hyphen, making the needed flag `-- --host`. By adding that flagged-flag to your script command, you'll tell the script to pass along the flag to Vite, which will then automatically expose the local host address when the server starts up.

## Directly from the command line

You can also use this flagged-flag directly on the command line if you only want to expose the IP a single time and not by default, so instead of using `npm run dev` you would use `npm run dev -- --host`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1680888592800/350734b2-f968-4c2a-ba1d-0013152eb155.png align="center")

Now that you have the address, you can easily [view the development server directly on your mobile device](https://blog.benhammond.tech/how-to-view-localhost-on-mobile).

And yes, if you look closely, I am pleased to finally be working on a feature that will kill a UI carousel that has been in place for far too long. If you're wondering why this is exciting to me, check out [Should I Use A Carousel?](https://shouldiuseacarousel.com/)