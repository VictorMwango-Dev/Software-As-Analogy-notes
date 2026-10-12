# Guide: Making a Motion Canvas Explainer Animation, Start to Finish

|  |  |  |
| --- | --- | --- |
|  |  |  |
|  |  |  |

Based on the database-to-browser animation made for Software as Analogy (October 2026). Give this guide to any AI assistant and swap in a new topic. Every step says what to do and why.

## 1. What this guide produces

A short, visual-only explainer: a 1920x1080 animation of about 25 seconds in a dark navy blueprint style with cyan and orange accents, where a dot travels through a diagram while each step lights up and a caption describes it. No voice-over, so fixed animation timings are fine. It is rendered to MP4 or edited in DaVinci Resolve, then posted on Facebook.

If the video has narration, do not use this guide alone. Read the section on narrated videos near the end, because the order of work changes completely.

## 2. Rules for the AI following this guide

1. Work one step at a time and stop at each check. Do not build everything at once.
2. Label every claim VERIFIED (with command output or a screenshot) or BELIEVED. Say so when something has not been tested.
3. Never ask the user to paste multi-line code into PowerShell as if it were a command. Code goes into a file, using Notepad, an editor, or a here-string that writes a file.
4. Do not create index.html and do not hand-write vite.config.js or package.json. The scaffolder makes them.
5. Keep technical content accurate. If the diagram simplifies reality, say so on screen.
6. Do not delete or overwrite the user's existing work without a backup.

## 3. The workflow at a glance

| Step | What | Why |
| --- | --- | --- |
| 1 | Define the topic and the beat sheet | The animation is only as clear as its sequence of steps |
| 2 | Choose the tool | Reuse what already works on the machine |
| 3 | Set up or reuse the project, with a backup | A known-good base means errors come from the new code only |
| 4 | Shared theme and components | Consistent brand look, less code per video |
| 5 | Plan the layout with coordinates | Prevents overlapping and cut-off elements |
| 6 | Write the scene | The actual animation |
| 7 | Save the file and check its size | Catches saves that failed |
| 8 | Register the scene | An unregistered scene never appears |
| 9 | Preview and check | Untested code is not working code |
| 10 | Render | Produces the video file |
| 11 | Copy the output | The file must live somewhere you can find it |
| 12 | Optional: edit in DaVinci Resolve | Only when you need music, trimming or titles |
| 13 | Verify the exported file | A finished export proves nothing until inspected |
| 14 | Post on Facebook | Caption, hashtags and upload |

## 4. Environment of the original project

| Item | Value |
| --- | --- |
| Computer | Windows 11, Intel i5-6300U, 8 GB RAM, Intel HD 520, no GPU |
| Node and npm | v24.18.0 and 11.16.0 |
| Motion Canvas | core, 2d, ui and vite-plugin 3.17.2, JavaScript, scenes are .jsx files |
| Exporter | @motion-canvas/ffmpeg ^1.1.0 (Video FFmpeg and Image sequence) |
| Editor for source files | Notepad (any editor works) |
| Final editing | DaVinci Resolve 20 |
| ffmpeg and ffprobe | Not on PATH. They exist inside the project at node\_modules/@ffmpeg-installer/win32-x64/ffmpeg.exe and node\_modules/@ffprobe-installer/win32-x64/ffprobe.exe |

Why this matters: a low-end laptop means the visuals must stay light (flat colors, thin strokes, no heavy blur or shadows), and the preview and render will be slow.

## 5. Step 1: Define the topic and the beat sheet

Write the story as numbered steps before any code. The original topic was how data flows from a database to a browser, in six steps:

1. The browser sends an HTTP request (GET /users/42).
2. The server receives it and runs the app code.
3. The app sends a SQL query to the database.
4. The database finds the matching rows and returns them.
5. The app turns the rows into JSON.
6. The server sends the response, and the browser shows the page.

Then decide:

- The actors (here: Browser, Server, Database).
- The artifacts that travel or appear (an HTTP request, a SQL query, a JSON response). Each becomes a small card with realistic example text.
- What to leave out, and say so on screen. Here the footer says DNS, caches and load balancers are left out, and that the browser typically does not query the database directly.

Why: the beat sheet becomes the caption text, the highlight order and the animation order. It is also where factual errors are caught cheaply, before any code exists.

## 6. Step 2: Choose the tool

The original answer from another AI suggested Remotion (React video) or HTML with CSS and OBS recording. Motion Canvas was kept because:

- It already ran on the machine with the brand theme and components.
- A new tool means a new project, a headless Chromium render that is heavy on 8 GB of RAM, and license terms that must be checked (not checked here).
- Staying in one tool keeps every video visually consistent.

If you are on a different machine or already know another tool, this step can change. The rest of the guide still applies in principle.

## 7. Step 3: Set up or reuse the project

For a new project, use the official scaffolder from a folder that does not contain the target:

```powershell
cd C:/developer
npm init @motion-canvas@latest
```

Choose JavaScript and both exporters. When the Project path prompt appears, it is pre-filled. Delete the default text before typing, otherwise your text is appended to it and you get a merged folder name. Then run npm install and npm start, open http://localhost:9000/, and confirm the example circle animates. Why: it proves the foundation works before any of your code is involved. Ignore the vulnerability list and the esbuild warning. Do not run npm audit fix --force.

In the original work the existing Docker project was reused as a sandbox. Before changing anything, back up its project file:

```powershell
cd C:/developer/docker-in-five-minutes-mc
Test-Path src/project.js
Copy-Item src/project.js src/project.js.full.bak -Force
```

Why: project.js lists which scenes the editor loads. The backup lets you restore the full project afterwards.

## 8. Step 4: Shared theme and components

Copy these from an earlier project rather than rewriting them: src/theme.js and src/components/Blueprint.jsx (a cached grid background, drawn once to stay light on integrated graphics), TerminalWindow.jsx and Laptop.jsx.

| Token | Value |
| --- | --- |
| bg | #0a1628 |
| grid / gridMajor | #12294a / #1c3d6e |
| panel / termBg | #102542 / #07101f |
| cyan / orange | #22d3ee / #ff9f43 |
| text / dim | #e6f1ff / #7f9bc4 |
| red / yellow / green | #ff5f56 / #ffbd2e / #27c93f |
| fonts | ui: Segoe UI, Arial, sans-serif. mono: Consolas, Courier New, monospace |

Why: every video then shares one visual identity, and scenes stay short because the look lives in one place.

## 9. Step 5: Plan the layout with coordinates

Motion Canvas places (0, 0) at the center of the 1920x1080 canvas. Positive x goes right and positive y goes down. Plan positions on paper first:

| Element | Position |
| --- | --- |
| Title | y = -440 |
| Subtitle | y = -370 |
| Three actor boxes (340 by 220) | x = -600, 0, 600 at y = -130 |
| Request lane (orange, pointing right) | y = -160, between the boxes |
| Response lane (cyan, pointing left) | y = -80, between the boxes |
| Caption | y = 70 |
| Three cards (560 by 250) | x = -600, 0, 600 at y = 300 |
| Footer | y = 490 |

Why: placing things by arithmetic is how overlaps happen. Writing the grid down first, and then checking it in the preview, catches them. Those positions were chosen by arithmetic and still had to be confirmed by eye.

## 10. Step 6: Write the scene

The pattern, in order:

1. Define small helpers: swap (fade a text out, change it, fade it in), pulse (scale up and back), and component functions for a stage box and a card.
2. Create references with createRef for everything you will animate.
3. Add every element to the view with opacity 0, so nothing flashes on screen.
4. Run the sequence with yield\*: intro fade-ins, then one block per step.
5. Use all() to run animations in parallel. Move the dot with position tweens along the lane coordinates.
6. Highlight the active actor by changing its lineWidth, and the matching card by changing its stroke color. Call these setters without a duration so they apply instantly.
7. End by fading everything out.

Why fixed timings are fine here: with no narration, the animation has no outside clock to match. Each step takes a caption swap, a dot travel (about 1.2 seconds) and a short wait, which gave about 26 seconds in total.

Generic template (3 actors, one step shown; repeat the step block for each beat):

```jsx
import {makeScene2D, Txt, Rect, Node, Line, Circle} from '@motion-canvas/2d';
import {createRef, waitFor, all} from '@motion-canvas/core';
import {Blueprint} from '../components/Blueprint.jsx';
import {colors, fonts} from '../theme.js';

function* swap(node, text, d = 0.3) {
  yield* node.opacity(0, d);
  node.text(text);
  yield* node.opacity(1, d);
}

function* pulse(node) {
  yield* node.scale(1.06, 0.25);
  yield* node.scale(1, 0.25);
}

function Stage({boxRef, x, title, sub, color}) {
  return (
    <Rect ref={boxRef} x={x} y={-130} width={340} height={220} radius={16}
      fill={colors.panel} stroke={color} lineWidth={4} opacity={0}
      layout direction='column' alignItems='center'
      justifyContent='center' gap={12}>
      <Txt text={title} fontFamily={fonts.ui} fontSize={52}
        fontWeight={700} fill={colors.text} />
      <Txt text={sub} fontFamily={fonts.mono} fontSize={28} fill={color} />
    </Rect>
  );
}

export default makeScene2D(function* (view) {
  const title = createRef();
  const caption = createRef();
  const lanes = createRef();
  const dot = createRef();
  const a = createRef();
  const b = createRef();
  const c = createRef();
  const stages = [a, b, c];
  const markStage = active =>
    stages.forEach((s, i) => s().lineWidth(i === active ? 10 : 4));

  view.add(<Blueprint />);
  view.add(<Txt ref={title} text='YOUR TITLE' fontFamily={fonts.ui}
    fontSize={76} fontWeight={700} fill={colors.text} y={-440} opacity={0} />);
  view.add(<Stage boxRef={a} x={-600} title='Actor A' sub='what it is' color={colors.cyan} />);
  view.add(<Stage boxRef={b} x={0} title='Actor B' sub='what it is' color={colors.orange} />);
  view.add(<Stage boxRef={c} x={600} title='Actor C' sub='what it is' color={colors.cyan} />);
  view.add(
    <Node ref={lanes} opacity={0}>
      <Line points={[[-420, -160], [-180, -160]]} stroke={colors.orange}
        lineWidth={5} endArrow arrowSize={16} />
    </Node>,
  );
  view.add(<Circle ref={dot} size={40} fill={colors.orange}
    x={-420} y={-160} opacity={0} />);
  view.add(<Txt ref={caption} text='' fontFamily={fonts.ui} fontSize={42}
    fontWeight={700} fill={colors.text} y={70} opacity={0} />);

  yield* title().opacity(1, 0.6);
  yield* all(...stages.map(s => s().opacity(1, 0.7)), lanes().opacity(1, 0.7));

  // Step 1
  markStage(0);
  caption().text('Step 1. Describe what happens');
  yield* caption().opacity(1, 0.4);
  yield* dot().opacity(1, 0.2);
  yield* dot().position([-180, -160], 1.2);
  yield* waitFor(1.2);

  // Step 2
  markStage(1);
  yield* swap(caption(), 'Step 2. Describe the next thing');
  yield* pulse(b());
  yield* waitFor(2.0);
});
```

Verified quirks from the original work: createRef comes from @motion-canvas/core, not from 2d. A scene file needs no JSX pragma. Layout props (layout, alignItems, justifyContent) center text inside boxes. Multi-line text needs textWrap set to pre.

## 11. Step 7: Save the file and check it

Create the file, for example src/scenes/dataflow.jsx. With Notepad, run notepad src/scenes/dataflow.jsx in PowerShell, click Yes to create it, paste the code, and save with Ctrl+S. Then check it:

```powershell
Get-ChildItem src/scenes/dataflow.jsx | Select-Object Name, Length
```

Why: three times in the original work a scene appeared to be missing because its file was empty, saved with a .txt ending, or never created. A byte count (the original was 7,341 bytes) catches that. Keep the code plain ASCII, so encoding is never a problem.

## 12. Step 8: Register the scene

A scene file does nothing until src/project.js imports it, with the ?scene suffix. For a single-scene test project:

```powershell
$p = @'
import {makeProject} from '@motion-canvas/core';

import dataflow from './scenes/dataflow?scene';

export default makeProject({
  scenes: [dataflow],
});
'@
[System.IO.File]::WriteAllText((Join-Path $PWD 'src/project.js'), $p, (New-Object System.Text.UTF8Encoding($false)))
```

Why: forgetting this step caused scenes not to appear in the editor several times. Writing the file with this method avoids a byte-order mark and avoids pasting code into the shell as commands. Add a size guard around it if you want it to refuse when the scene file is missing.

## 13. Step 9: Preview and check

Keep npm start running in its own window. Press Ctrl+F5 on http://localhost:9000/, check that the timeline lists the scene, and press the spacebar. Check:

1. Nothing overlaps or is cut off: title, boxes, lanes, caption, cards and footer.
2. The dot follows the lanes, orange going out and cyan coming back.
3. The active box gets a thick border, and its matching card is highlighted.
4. Captions swap cleanly and fit on one line.
5. Code snippets fit inside their cards.
6. Playback is smooth and the total length is as planned.

Why: code that has not run in the preview is unverified. In the original work the visual results were never reported back to the AI, so layout was never confirmed here. Do the checks. If the browser shows red errors, open the console with F12 and read the first one.

## 14. Step 10: Render

In the editor, open Video Settings (the camera icon) and set:

| Setting | Value | Why |
| --- | --- | --- |
| Resolution | 1920 x 1080 | Standard landscape HD |
| Rendering frame rate | 30 | The default shown was 60, which doubles render time and file size |
| Exporter | Video (FFmpeg), fast start on | One MP4 file instead of thousands of PNG frames |
| Range | 0 to end | Use a short range such as 0 to 300 first for a test |

Close other heavy apps and keep the editor tab visible during the render. The output goes to output/project.mp4 inside the project folder. The original render was a small file of about 0.9 MB.

## 15. Step 11: Copy the output

```powershell
New-Item -ItemType Directory -Force (Join-Path $env:USERPROFILE 'Downloads/video') | Out-Null
Copy-Item output/project.mp4 (Join-Path $env:USERPROFILE 'Downloads/video/database-to-browser.mp4') -Force
```

Why: the output folder is overwritten on every render, so copy and rename each finished file right away.

## 16. Step 12: Optional editing in DaVinci Resolve

You do not need Resolve for a silent animation, because the rendered MP4 is already finished. Use it when you want to trim, add music or sound effects, add titles, or build a vertical version. If you do, create a 1920x1080 timeline at 30 fps, import the MP4, edit, then open the Deliver page. The labels below are from memory and may differ slightly:

- Format MP4 and codec H.264 are the safest choice for social media. Format QuickTime produces a .mov file.
- Resolution 1920 x 1080 and frame rate 30.
- Add to the render queue, then Render All.

In the original work the Resolve export was a .mov (Timeline 1.mov). It was H.264, 1920x1080, 25.73 seconds, 13.9 MB, with a 24-bit uncompressed audio track that Resolve adds by default. The animation itself has no sound.

## 17. Step 13: Verify the exported file

Use ffprobe, which was already inside the project at node\_modules:

```powershell
& 'C:/developer/docker-in-five-minutes-mc/node_modules/@ffprobe-installer/win32-x64/ffprobe.exe' -v error -show_entries stream=codec_name,codec_type,width,height,duration -of default=nw=1 'C:/Users/victo/Downloads/Timeline 1.mov'
```

Expect: video codec h264, width 1920, height 1080, and a duration close to the planned length. Any extra data stream is just a timecode track from Resolve. Then play the file once and check the last frames and the footer text.

Why: a successful export does not prove the content is right. In the original work this check confirmed that the length matched the animation, so nothing extra was sitting at the end.

If you need an MP4 from a .mov, ffmpeg can convert it (this re-encodes, a small quality cost):

```powershell
& 'C:/developer/docker-in-five-minutes-mc/node_modules/@ffmpeg-installer/win32-x64/ffmpeg.exe' -i 'C:/Users/victo/Downloads/INPUT.mov' -c:v libx264 -crf 18 -preset medium -pix_fmt yuv420p -movflags +faststart -c:a aac -b:a 192k 'C:/Users/victo/Downloads/OUTPUT.mp4'
```

Whether that ffmpeg build includes the H.264 encoder was not checked.

## 18. Step 14: Post on Facebook

Caption structure that was used: a hook question, the numbered steps in short form, the simplification note, a call to follow the page, and about five or six hashtags at the end. Example hashtags: #SoftwareAsAnalogy #WebDevelopment #HowItWorks #Database #LearnToCode #SoftwareEngineering. Change the topic hashtags for each new video and keep the brand hashtag the same.

Steps (menu labels are from memory and change often):

1. Open the Page, switching into it first if you manage it from a personal profile.
2. Click Photo/video in the post box and choose the file. A 1920x1080 landscape file was prepared. Facebook is believed to accept .mov and .mp4, but its current rules were not checked. MP4 with H.264 is the safest choice.
3. Paste the caption, add a thumbnail if offered, preview, then Post or Schedule.
4. For Reels or Stories you need a vertical 1080x1920 version. That is a separate layout, not a resize of this one.

Why a silent video works: every step is captioned on screen, so viewers who scroll with the sound off still understand it.

## 19. Narrated videos work in a different order

When the video has a voice-over, do not animate first. The Docker video was built that way and the narration drifted away from the visuals. The correct order is script, audio, measured timing manifest, then animation that waits on the manifest timestamps, then verification. The audio file is the source of truth. Details are in the separate playbook titled Audio-First Motion Canvas Playbook. Two parts of that approach were never tested: whether Motion Canvas can carry audio into the render, and the ffprobe sync checks.

## 20. Troubleshooting

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| Scene not visible in the editor | Not registered in src/project.js, or missing ?scene suffix | Rewrite project.js as in Step 8 and refresh with Ctrl+F5 |
| Blank page at localhost:9000 | A hand-made index.html or config | Remove index.html. Use the scaffolded vite.config.js |
| Code run in PowerShell fails with errors like yield not recognized | The code was pasted into the shell | Save it to a file, then register it |
| Scaffolder folder name is merged | Default path text not cleared | Clear the prompt, then type the path |
| Notepad saved a .txt file | Wrong Save as type | Choose All files, or use a code editor |
| Text clipped or overlapping in preview | Positions chosen by arithmetic | Adjust coordinates, then re-preview |
| Render takes very long | 60 fps default and a weak CPU | Set 30 fps, close other apps, test a short range first |
| Upload rejected or stuck processing | Unusual codec or container | Convert to MP4, H.264 with the ffmpeg command in Step 13 |
| Output file overwritten | Every render writes to the same name | Copy and rename after each render |

## 21. Starter prompt for another AI

Paste this first, then attach this guide. Replace the bracketed parts:

```text
Follow the attached guide exactly. Topic: [TOPIC]. Format: a silent 1920x1080 Motion Canvas
explainer of about [LENGTH] seconds in the Software as Analogy blueprint style.
Step 1 only: write the numbered beat sheet, the actors, the cards with realistic example text,
and what is simplified. Stop there for my approval.
Do not write code until I approve. Do not create index.html. Do not hand-write package.json
or vite.config.js. Label every claim VERIFIED or BELIEVED.
Then proceed one step at a time and stop at each check.
```
