---
layout: post
title: Capturing my handwriting in a searchable digital format
published: false
---

# Why handwriting?

I've been spending a lot of time in a classroom environment and taking notes. My preferred way to do this is in a notepad, as despite leading a pretty digitised life I find taking notes like this to be the lowest friction solution. Plus, who wants to listen to someone tip-tapping away on a keyboard, right?

# Why digitise it?

I found that a lot of my notes were either being forgotten or it was hard to find where I'd written a particular thing. By having my notes searchable, I would be able to look up key words and have indexes, etc.

# Why create something new?

Here's the big question. When I started thinking about this I assumed there would be something off the shelf - and there is. It just turns out that the options just aren't very good.

## Apple

iOS has a thing called [Live Text](https://support.apple.com/en-gb/HT212630) which lets you extract text from images, and this was my first port of call. For handwriting, the results are extremely poor and aren't formatted well, so I moved on.

I also looked at the handwriting recognition feature of an Apple Pencil but this is too far into the uncanny valley of writing for me, and the results were really mixed.

## Rocketbook

I picked one of these up - it's like a whiteboard but in notepad form - and tried out its app which comes with an optical character recognition (OCR) feature. This works really well, but where it fell down was in its sharing options. I wanted to push my notes into [Obsidian](https://obsidian.md/), which doesn't have an API, and Rocketbook only supports sending directly to email, iCloud drive, Google Drive, and a couple of notes apps like OneNote. So, a couple of problems there.

Let's break this down.

- I want good quality OCR
- I want to import my notes into Obsidian

# The solution, and yes, it involves AI

[Claude](https://claude.ai/) will convert text in any image into Markdown with really great accuracy, so I knew I wanted to involve that. For Obsidian, given that it doesn't have an API since it's local-first, I needed to make a plugin to pull down that Markdown from somewhere and insert it into my Obsidian vault.

So, I created a Rust webservice that you can POST an image to, and then that sends it to Claude's API for OCR. The result is pushed into a database. The Obsidian plugin will check a second endpoint on the API and create a new file with that content. When it pulls the content, the API marks it as downloaded so it doesn't get pulled down again. The plugin also calls the endpoint on a schedule so it can stay up to date.

And yes, this was an experiment with [Claude Code](https://www.anthropic.com/claude-code). This is clunky but remarkably effective, with it churning out the Obsidian plugin without requiring much bullying. The Rust API required a little more back-and-forth. I have no plans to publish this for other people to use, but I have [published the code](here).
