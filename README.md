# BT Labeler

Draw boxes around cornhole bags (and boards) in your frames, then export a training set in YOLO format. It runs entirely in your browser. Your images never leave your device, and your work autosaves in the browser.

Made for the BagTrak bag detector. It pairs with BT Frame Extractor, which makes the ZIPs this tool opens.

## Workflow

1. **Extract frames** with BT Frame Extractor and save the ZIPs (for example to Drive).
2. **Open** a ZIP here (or plain pictures, or drop files on the page). Several ZIPs can be open at once.
3. **Label.** Drag to draw a box. Pick a class with `1` to `9`. Press `C` on the next frame to copy the previous frame's boxes (bags on boards don't move), then fix any that did.
4. **Save labels** now and then. It downloads a small `.json` backup. Keep it next to your ZIPs. **Load labels** puts it back, and labels attach to images by file name.
5. **Export YOLO** to get a ZIP with `images/train`, `images/val`, `labels/train`, `labels/val`, `data.yaml` and `classes.txt`. That is what YOLO training (for example in Colab) expects.

## Tools

| Tool | What it does |
| --- | --- |
| Draw (`B`) | Drag to make a box. Click a box to select it. Drag a handle to resize. |
| Select (`V`) | Drag inside a box to move it. |
| Pencil (&#9998; in the box list) | Edit one box: drag it to move, drag a handle to resize, then **Save** (or Enter). Going to another box or frame saves it too; Esc cancels. Saving a green (moved) bag makes it resting again, and removes the bag's old spot from the later frames of that video (it stops at the first frame where the bag has already moved on). Arrows nudge 0.5 px (Shift 5 px). |
| Pan (`H`) | Move the picture. Space + drag, right-drag, or two fingers also pan. |

Zoom with the wheel, pinch, `+` / `-`, or the buttons. `0` fits the picture. At high zoom you can place edges to a fraction of a pixel, which matters for small bags far down the lane.

## Shortcuts

| Key | Action |
| --- | --- |
| `1` to `9` | Pick a class (changes the selected box's class if one is selected) |
| `A` / `D` or arrows | Previous / next image |
| Arrows (box selected) | Nudge 1 px, `Shift` for 10 px |
| (automatic) | Resting bags are pushed forward. When you leave a frame you changed, or press Save, its resting bags are brought to the following labeled frames: old copies move to the new spot, missing bags are added, doubled copies are removed. It stops at the first frame where the bag was moved (green), went in the hole, or has its own different spot, and at the first unlabeled frame. Walking through frames you did not change never touches them. |
| `C` | Copy boxes from the previous image. A resting bag already on this frame at an older spot is moved to the copy's spot instead of doubled. |
| `E` | Mark image as empty background and go to the next |
| `U` | Next unlabeled image |
| `Delete` | Delete the selected box |
| `Ctrl+Z` / `Ctrl+Y` | Undo / redo |
| `L` | Show or hide box labels |
| `?` | Help |

## Bags in the hole

- **Hole ring.** Press `3` and drag a box around the hole: it is drawn as a yellow ellipse and carries to the following frames like a board. Draw it once per video.
- **The ☰ menu.** Every bag row has a ☰ button with: *In the hole (this frame)*, *Off the board (this frame)*, *Delete from here on* (removes that bag from this frame and every later frame of the video, after asking you to confirm) and *Delete this frame only*. More actions can be added there later.
- **Declaring it.** When a resting bag is gone because it went in the hole, choose **In the hole** in its ☰ menu (or press `X` on its box, or delete its box and choose **In the hole**). That frame gets a one-frame in-the-hole marker, the ring turns green on it, and the bag's old resting boxes are removed from the later frames. Deleting a resting bag's box always asks first: *In the hole*, *Delete from here on*, *This frame only*, or *Keep*.
- **Off the board.** When a bag leaves the board or the picture, keep it as *moved* while any of it is visible. On the first frame where it is gone, choose **Off the board** in its ☰ menu (draw a small box and tag it first if no box is there). It works like the hole marker: a one-frame purple marker, old resting copies removed, "Now drawing" moves to the next bag, and the bag count shows it as *off the board*.
- **Not training data.** Both kinds of marker are kept in your labels file but left out of the YOLO export, because no bag is visible to box. The ring is exported as its own class, `hole`.

## Notes on carrying

- Stepping onto an empty frame carries the last frame's resting bags. It stops at a frame you marked empty or cleared on purpose (for example with *Delete from here on*), so a restarted video or an empty board does not pick the old bags back up.

## Notes on the export

- **Split by video.** Frames from one clip look almost identical. If some land in training and some in validation, validation scores look better than they really are. So by default whole videos go to one side. With fewer than 3 videos it uses the last part of the list for validation instead. The video is taken from the file name the extractor makes.
- **Empty images.** Images you mark empty (`E`) are exported as background examples with an empty label file. Images you haven't touched are left out.
- **Classes.** Starts with `bag` and `board`. You can add and rename classes so the numbers in your label files stay stable.
- Boxes are stored in pixels and exported as normalized `class cx cy w h`.

## Host it free

In the repo: **Settings, Pages**, deploy from branch `main`, folder `/ (root)`. The app then lives at `https://<your-username>.github.io/<repo-name>/`. Open it in Chrome and use **Add to Home screen** (or **Install app**) to install it. After the first visit it also opens offline.

Labeling works on a phone, but a desktop or tablet is much easier for small bags.

## Files

- `index.html` is the whole app (loading, drawing, zoom, export).
- `sw.js` is the offline cache.
- `manifest.webmanifest`, `icon-192.png`, `icon-512.png` are for installing it.
