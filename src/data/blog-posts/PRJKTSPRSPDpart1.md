---
title: "PRJKTSPRSPD Part 1: Building My Music Player & Audio Mixer Web App"
slug: PRJKTSPRSPDpart1
publishDate: 2026-05-30
description: blank
---

Hello again! This is my second post on Aidan Nikki. 

For this post, I wanted to talk about one of the bigger projects I have been working on for the past few months. Since this was a large project with so many different things I want to talk about, I will be breaking it down into several parts. This first episode will be kind of an introduction to the project as well as a general overview of everything. I'll dig deeper into the cool stuff in later episodes.

![PRJKT img](/public/assets/prjkt.png)

## The basic idea

I know from the image it looks like a lot but I'll break it down for you. Basically, PRJKTSPRSPD is a music player and audio mixer. The alternative to your beloved Spotify. And to all your beloved Spotify listeners your platform is a bit [sus](https://www.theguardian.com/music/2025/jul/31/spotify-musicians-david-bridie-ntwnfb). You can import songs from your device that will be saved to the browser, change the pitch and speed, add effects like reverb or bitcrushing, manage a queue, and watch the 3D vaporwave background move to the beat. I also wanted to go for a different UI from the average music player, allowing the user to control everything in one screen. A criticism I've gotten is that the screen can get very crowded due to this but I personally prefer it to the modern music players like Spotify or Evermusic (what I use) as I feel like I have to tap through so many different buttons and tabs to find what I want. On larger screens, I see my UI as totally dominant. On mobile, maybe its more preference based, though mine works perfectly on mobile (I use it everyday in the car).

So yeah that's the basic rundown I suppose. Oh I forgot to mention! There's also a pomodoro timer (25 min on 5 min off cycles) for work. At the time I was making this project I often listened to Hollow Knight pomodoro playlists so I thought having it built in to the music app would be a great idea. But yeah let me get into why I built this. 

Also, here's a TLDR that gives you what each icon does on this desktop style interface: 

- **Songs** — where you can see your imported songs
- **Mixer** — where you can change pitch, speed, and effects
- **Import** — where you can import files or folders
- **Queue** — where you can see what is coming next
- **Tutorial** — basic instructions
- **Timer** — a Pomodoro-style timer that controls the music

When you click an icon, it opens a draggable window. So the app is still a music player, but the interface feels more like opening programs on an old computer.

## So why did you build this?

As for why I built this, it's for two main reasons. 1. ALL THESE MUSIC APPS ARE HORRIBLE & 2. I like listening to sped up and pitched up versions of songs and I'm tired of looking for nightcore versions of every song (usually not sped up/pitched up enough still)

But since this is my blog I'm gonna take the time to rant about why I hate music apps. First of all, the ads. I use Evermusic, which you probably don't know what it is so let me explain that first. So a lot of the songs I like aren't on apps like spotify so what I do is download my songs from youtube using very totally legal methods. Once it's on my device I can use Evermusic to play the songs. This works on Spotify too (with no ads even on free version), but you can't put downloaded files into playlists so I switched to Evermusic. And let me take a second to rant on Spotify. Their free version SUCKS. Ads after like every song if it's not listening offline through downloaded local files. And like they don't have ANY OF THE SONGS I LIKE!! Like nobody's listening to stupid Ice Man by Drake man! Put on my Japanese artists on there! No international artists, no fun music man. And their music recommendation system sucks. Once I got the time I'm gonna make a better one. 

Sorry, we got sidetracked but I just had to get that off my chest. 
 
---

## The stack

The project uses **React** with **Vite**. Vite is the build tool that runs the dev server and bundles everything for production. React handles the UI and state.

The important libraries are:

1. **Tone.js** — for the audio engine
2. **IndexedDB / idb** — for saving songs locally in the browser
3. **Three.js** — for the animated 3D background
4. **GSAP** — for draggable windows
5. **React** — for the actual app structure and components

I kind of kept adding things as I went so I didn't plan on using all these when I started. This was actually just supposed to be a very basic music player initially and it kinda got crazyyy

## The main structure of the app

Ok ok so I'd love to go into each file and the detailed process for each one but likeee 

<img src="/public/assets/components.png" width="200" alt="Description">
<img src="/public/assets/css.png" width="200" alt="Description">
<img src="/public/assets/svgs.png" width="200" alt="Description">
 
Look at all these files man.. and this isn't even all of it!!! But the good thing is the important stuff is in just a few files! So we'll go into them now. So lock in because now we're getting into the [meat](https://www.youtube.com/watch?v=UrrHnagerGw&pp=ygUMbWVhdCAyc2xpbWV5)! Also real quick, the repo is free so go take a look at the code as you read. [ClICK HERE FOR THE RePO](https://github.com/aidanm20/PRJKTSPRSPD)

The first important file is `App.jsx`. This is kind of the brain of the app. It keeps track of the main state, like whether music is playing, what song is currently selected, the list of imported songs, the queue, and whether loop or shuffle is active.

It also handles loading saved songs when the app starts. Since the songs are saved in the browser, the app needs to get them from IndexedDB, turn them back into playable audio sources, and then put them into React state.

Then there is a custom hook called `useAudio`. This is where the actual audio logic lives. It handles the Tone.js player, play and pause, seeking, current time, total time, pitch, speed, and effects like reverb, filtering, and bitcrushing.

Then there is `songDatabase.js`, which handles saving, loading, and deleting songs from IndexedDB. I separated this into its own file because I did not want the database logic mixed directly into the UI code.

Then there is `Desktop.jsx`, which handles most of what the user actually sees. This includes the desktop icons, draggable windows, taskbar, mixer window, song window, queue window, import window, timer window, and the Three.js background.

So at a high level, the app is kind of structured like this:

```txt
App.jsx
  ├── useAudio.js        // audio engine
  ├── songDatabase.js    // browser storage
  └── Desktop.jsx        // main interface
        ├── Window.jsx
        ├── DisplaySongs.jsx
        ├── FolderInput.jsx
        ├── Mixer controls
        ├── Queue controls
        └── TimerContent.jsx
```

---

## Some big takeaways from this project

So while I write out the more detailed versions of specific features in the site, just look at the repo. But I want to go over the big lessons I took away from this project. Here they are.

### The importance of UseRef

Going into this project, I wasn't too good with `useRef`, as I think I didn't really understand it too well.

I understood `useState` because it was pretty straightforward. You use it when you have a value that affects what appears on the screen. When the value changes, React re-renders the component and the UI updates. 

For example, if I have a `playing` state, the play button can change depending on whether `playing` is true or false.

So in my head:

`useState` = store a value and update the UI when it changes

`useRef` is different. A ref also stores a value, but changing that value does not cause the component to re-render.

That part confused me at first because I was like, why would I want to change a value if React is not going to update the UI?

But this project made me realize that not every value is meant for the UI.

Some values are just for logic.

For example, in the audio engine, I needed to keep track of things like:

- where the song currently is in queue
- when playback started
- whether the audio is actually playing
- the latest queue
- whether loop or shuffle is active

Those values matter a lot, but I do not necessarily want the whole component to re-render every time one of them changes.

This became especially important with timers, callbacks, and audio events.

One issue I ran into was that functions can sometimes hold onto old state values. So if a song ends and the audio engine calls a function, I need that function to know what the queue is right now, not what the queue was earlier when the function was created.

That is where `useRef` became really useful.

For the queue, I still use state because the UI needs to show the queue:

`const [queue, setQueue] = useState([])`

But I also keep a ref version of it:

`const queueRef = useRef([])`

Then whenever the queue changes, I update the ref:

`queueRef.current = queue`

This way, React state handles the UI, and the ref gives callbacks access to the latest value.

The simplest way I started thinking about it is:

`useState` is for rendering.

`useRef` is for remembering.

That is probably a little oversimplified, but it helped me understand the difference.

In PRJKTSPRSPD, this mattered a lot because the project has things happening outside of normal React rendering. Audio playback is happening through Tone.js. The background is animating through Three.js. The timer is running with intervals. The windows are being dragged with GSAP.

All of those systems need values to stay updated, but they do not always need React to re-render.

Before this project, I mostly thought `useRef` was just for grabbing DOM elements, like a canvas or a div. And it is useful for that too. But now I understand that refs are also really useful for storing mutable values that need to survive between renders.

So yeah, I definitely understand `useRef` a lot better now.

I still do not think refs should replace state everywhere. If changing the value should change what the user sees, I probably want `useState`.

But if I just need to remember a value for logic, callbacks, intervals, or external libraries, I probably want `useRef`.

### Simple features are usually not that simple

Another lesson I learned is that simple features can be really complicated, especially when I am the one making the assets! Let's think of the shuffle button for example. A shuffle button seems really simple. It's one of the basic features of any music app. But let's think of what actually goes into making a shuffle button, and you quickly realize its not some task that you can knock out in 5-10 minutes.

First, the app needs to know what songs are available. Then it needs to make a shuffled version of those songs without messing up the original song list. This matters because the song list still needs to be used for search, sorting, deleting, and displaying songs normally.

Then there is the current song.

If a song is already playing, I do not want shuffle to suddenly interrupt it and start a random song. That would feel weird. So the current song should stay first, and the rest of the songs should be shuffled after it.
 

Then there is the case where no song is currently playing. In that situation, there is no current song to keep first, so the app can just shuffle the full song list. 

So the logic becomes something like this:

`if a song is playing, keep it first and shuffle the rest`

`if no song is playing, shuffle everything` 

Turning shuffle off also needs logic. If the user turns shuffle off, the app should go back to the normal song order starting from the current song. Otherwise, the button would visually turn off, but the old shuffled queue would still be controlling playback.

Loop makes it even more complicated.

Shuffle by itself is not too bad. Loop by itself is not too bad. But shuffle and loop together create more questions. What happens when the shuffled queue ends? Should the same shuffled order repeat? Should the app generate a new random order? Should songs be allowed to repeat? 

There are also edge cases:

- What if there are no songs?
- What if there is only one song?
- What if the current song gets deleted?
- What if the user adds a song to the queue while shuffle is on?
- What if the user clicks next right after turning shuffle on? 

Finally, I had to make the shuffle pixel art in Libresprite.

A lot of frontend development is not just putting a button on the page. It is deciding what that button means, what state it controls, how it interacts with the rest of the app, and what should happen in all the weird situations.

So shuffle is just one small feature, but it includes randomization, queue management, current song handling, loop behavior, visual feedback, and a bunch of edge cases! And with the amount of features on this site you can imagine the amount of time it took to build this whole thing.

### Creative coding and building UI is really difficult, but can be simplified!

So I'm the type of person that gets really ambitious with design. When starting a project, I imagine it having some crazy award winning designs. I underestimate the workload and tell myself I'll figure it out when I get there. This mindset has failed a few of my personal projects in the past. I try to make something design heavy and end up with a barely functional mess. 

That's why this project I wanted to keep the designs more "simple". I know that looking at the site it can look pretty complex (and it was), but it wasn't as bad as some of the other projects I've attempted. Anyhow, the lesson I'm trying to share is that *you can simplify your ideal vision for the design and still have it look good*. 

Also having experience with my stack helped a lot. I have made various projects with ThreeJS, GSAP, and CSS. The only new tool I used was Libresprite for the pixel art, but I have some experience using Aseprite and the Pixelart app on my phone. I also had a clear idea of how I wanted the UI to look when I started the project. I wanted a desktop-like interface with a taskbar at the bottom.

I'll definitely be making a post detailing how I did all the designs but let me give you the basic rundown because there's not too much? maybe?

So for the 3D background, visually there's only the sun, scattered stars, the sky, and the vaporwave ground with mountains thing. The icons I made are simple pixel art. The windows have a lot of shared CSS and the desktop is pretty simple CSS. Moving the song order in the queue with GSAP and the pop up menus when hitting the three dots was a bit complicated but not too terrible. And really that's all there is. 

I am the type of person who gets really ambitious with design. Whenever I start a project, I imagine it having some crazy award-winning interface with animations everywhere, perfect visuals, custom assets, and a super unique layout. Then I tell myself, "I'll figure it out when I get there."

That mindset has killed a few of my personal projects in the past.

I would start with this huge design idea, underestimate how much work it would take, and then end up with something that looked half finished and barely worked. The idea was cool, but the scope was too big for where I was at.

So for this project, I tried to do something different.

I still wanted PRJKTSPRSPD to look interesting, but I wanted the design to be more manageable. 

And I think that was one of the biggest lessons from this project:

*You can simplify your ideal vision without completely losing what made it cool in the first place.*

Looking at the app, it might still seem pretty complex. There is a 3D background, pixel art icons, draggable windows, a taskbar, pop up menus, and a retro desktop interface. It definitely was not easy and took me a lot of time.

But the important thing is that the design is built from a few reusable ideas.

The 3D background is visually pretty simple. It is basically a sun, stars, sky, and a vaporwave style ground. The icons are simple pixel art. The windows all share the same general styling. The desktop layout is mostly icons, windows, and a taskbar.

So instead of designing a hundred unique things, I designed a few reusable pieces and used them throughout the app.

That made the project way more realistic.

Having experience with the stack helped too. I had already worked with Three.js, GSAP, and CSS in past projects, so I was not learning every design tool from scratch at the same time. The only new tool I really used was Libresprite for the pixel art, but I had some experience with Aseprite and the Pixelart app on my phone, so it did not feel completely unfamiliar.

I also had a clear visual direction from the beginning. I knew I wanted the app to feel like a desktop, with icons you could click and a taskbar at the bottom. That helped a lot because I was not making every design decision from zero.

To summarize,

I did not build my biggest possible design idea. I built a smaller version of the idea that still captured the feeling I wanted. That is probably the lesson I want to remember for future projects.

Ambitious design is fun, but if the scope is too big, the project can collapse under its own weight. A simpler design that actually gets finished is usually better than a perfect design that only exists in my head.

---

Well I'll keep it at that for now! I'll see you in the next one hopefully.