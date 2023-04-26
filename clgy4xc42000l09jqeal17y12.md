---
title: "Ignore GitHub Whitespace Diffs"
datePublished: Wed Apr 26 2023 20:14:47 GMT+0000 (Coordinated Universal Time)
cuid: clgy4xc42000l09jqeal17y12
slug: ignore-github-whitespace-diffs
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1682539990227/ebf039fb-61aa-4e02-8982-e0e52960fcc5.jpeg
tags: github, bookmarklet

---

> In Chrome:  
> 1\. **⌘+D** to make a new bookmarklet in your bookmarks toolbar  
> 2\. Name it whatever, like "Hide PR Whitespace"  
> 3\. Paste `javascript:document.location+="?w=1"` as the URL  
> 4\. Click it whenever GitHub is showing you whitespace diff

Ideally, everyone in your repo will end up pushing pre-linted code, which should align styling and white-space issues. In reality this won't always happen, and seeing white space in the diff can be annoying and make the PR looks more massive than it is.

GitHub recently introduced a Pull Request level setting that gets remembered for the life of the PR that allows whitespace to be hidden. This is a pretty useless feature for most of us since it requires 3 clicks to enable and must be done on every new PR.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1682539042831/ed73886c-0c64-428a-8bd3-9b7542a2b1e2.gif align="center")

The other option is to manually append `?w=1` to the pull request URL, which triggers the same white space suppression. Luckily, this can be done automatically via JavaScript plopped into a bookmarklet, allowing us a 1-click option for easier PR reviewing. Follow the steps at the top of this post to make your very own bookmarklet. Enjoy!

Caveat: this won’t work if there are existing params on the URL, since subsequent params require the `&` rather than the initial param’s `?`