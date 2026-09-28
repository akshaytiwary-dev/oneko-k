# oneko-K 🐾

> A modernized, true-transparency port of the classic **oneko** desktop pet for **Wayland / Hyprland**, featuring native Hyprland IPC cursor tracking and distance-based proximity waking!

<p align="center">
  <img src="assets/demo.gif" alt="oneko-hyprland Demo: Cat sleeping and waking up to chase the cursor" width="520" />
</p>

<p align="center">
  <a href="#features"><img src="https://img.shields.io/badge/Hyprland-Native%20IPC-blue?logo=archlinux" alt="Hyprland IPC" /></a>
  <a href="#proximity-detection"><img src="https://img.shields.io/badge/Proximity-500px%20Radius-brightgreen" alt="Proximity" /></a>
  <a href="#transparency"><img src="https://img.shields.io/badge/Alpha-32--bit%20ARGB-orange" alt="ARGB Transparency" /></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-BSD%202--Clause-lightgrey" alt="License" /></a>
</p>

---

## 📖 Origin & Attribution

This project is directly derived from the classic X11 desktop pet **`oneko`**, originally created by **Masayuki Koba** (1990) and subsequently extended by **Tatsuya Kato**, **John Lerchey**, **Eric Anderson**, **Toshihiro Kanda**, and **Kiichiroh Mukose**.

On modern Wayland compositors (specifically **Hyprland**), legacy `oneko` suffered from three fatal issues:
1. **Frozen Cursor Tracking**: X11's `XQueryPointer` cannot track the mouse cursor across native Wayland applications (kitty, Firefox, Waybar, etc.), freezing the cat on the screen.
2. **Black Box Background Artifact**: Due to XWayland handling of 24-bit visuals, non-transparent bounding boxes and black shadow rectangles appeared around the cat sprite.
3. **Disappearing / Unpinned Windows**: The cat window would frequently drop underneath tiled windows or disappear when switching workspaces.

**`oneko-hyprland`** completely resolves these issues while introducing intelligent proximity awareness!

---

## ✨ Features & Enhancements

### 🚀 Hyprland UNIX Domain Socket IPC
- Instead of relying on restricted X11 pointer queries, `oneko-hyprland` connects directly to Hyprland's active UNIX socket (`/run/user/<uid>/hypr/$HYPRLAND_INSTANCE_SIGNATURE/.socket.sock`).
- Queries real-time global cursor coordinates (`cursorpos`) with sub-millisecond (~0.02ms) overhead.
- Flawlessly chases the mouse pointer anywhere across multi-monitor setups, native Wayland apps, and XWayland clients.

### 💎 True 32-bit ARGB Alpha Transparency
- Automatically selects a 32-bit `TrueColor` ARGB visual (`depth == 32`).
- Renders sprite frames with pure 32-bit alpha transparency (`0x00000000` transparent background, `0xFFxxxxxx` sprite fill).
- **100% elimination** of the notorious black box and shadow square artifact under XWayland.

### 💤 Proximity-Based Chasing (`-proximity <pixels>`)
- Euclidean distance calculation between the pet and your cursor:
  $$\text{distance} = \sqrt{(x_{\text{mouse}} - x_{\text{pet}})^2 + (y_{\text{mouse}} - y_{\text{pet}})^2}$$
- **Default radius**: `500` pixels.
- When the cursor is far away (> 500px), the cat comfortably sleeps (`Zzz...`) or grooms itself without cluttering your screen.
- When you move your cursor within the proximity boundary, the cat immediately perks up with an alert animation (`!`) and begins playfully chasing your mouse!
- Configurable to any distance (or set to `0` / high values to customize).

### 📌 Pinned & Non-Intrusive
- Window class explicitly set to `oneko` (`XClassHint`), allowing Hyprland window rules to pin the pet across all workspaces, float it above other windows, and disable focus-stealing or tiling interference.

---

## 🐾 Characters & Skins

`oneko-hyprland` includes all classic character skins and custom chasing cursors:

| Animal / Character | Exact Launch Command | Description | Chased Object |
| :--- | :--- | :--- | :--- |
| **Classic Cat (Neko)** | `oneko &`<br>*(or `oneko -neko &`)* | The original playful Japanese white cat | Mouse pointer |
| **Tiger Cat (Tora)** | `oneko -tora &` | Playful tiger-striped cat | Mouse pointer |
| **Dog (Inu)** | `oneko -dog &` | Energetic puppy | Bone cursor 🦴 |
| **BSD Daemon (Beastie)** | `oneko -bsd &`<br>*(or `oneko -bsd_daemon &`)* | Marshall Kirk McKusick's BSD Daemon | Trident cursor 🔱 |
| **Sakura Kinomoto** | `oneko -sakura &` | Sakura Kinomoto (*Cardcaptor Sakura*) | Clow Card 🃏 |
| **Tomoyo Daidouji** | `oneko -tomoyo &` | Tomoyo Daidouji (*Cardcaptor Sakura*) | Cherry Blossom Petal 🌸 |

### ⚡ Exact Commands to Launch Each Animal

Run any of the following commands directly in your terminal to start your desktop companion in the background (`&`):

```bash
# 1. Classic Cat (Neko)
oneko &

# 2. Tiger-Striped Cat (Tora)
oneko -tora &

# 3. Dog / Puppy (Inu) — chases a bone
oneko -dog &

# 4. BSD Daemon (Beastie) — chases a trident
oneko -bsd &
# (or the long form)
oneko -bsd_daemon &

# 5. Sakura Kinomoto — chases Clow Cards
oneko -sakura &

# 6. Tomoyo Daidouji — chases cherry blossom petals
oneko -tomoyo &
```

> [!TIP]
> **To close or stop oneko at any time**, simply run:
> ```bash
> killall oneko
> ```

---

## 🛠️ Installation

### Dependencies

On **Arch Linux / EndeavourOS / Manjaro**:
```bash
sudo pacman -S base-devel libx11 libxext
```

### Build from Source

```bash
git clone https://github.com/akshaytiwary-dev/oneko-k.git
cd oneko-k
make
sudo make install
```

This installs the binary to `/usr/local/bin/oneko` and the man page to `/usr/local/share/man/man1/oneko.1`.

To install to your local user directory without `sudo`:
```bash
make PREFIX=$HOME/.local install
```

---

## ⚙️ Hyprland Configuration

To ensure `oneko` floats cleanly without borders, shadows, blur, or focus stealing, add the following rules to your Hyprland configuration (e.g. `~/.config/hypr/hyprland.conf` or `~/.config/hypr/configs/window_rules.conf`):

```ini
# oneko-hyprland window rules
windowrule = float true, match:class ^(oneko)$
windowrule = pin true, match:class ^(oneko)$
windowrule = no_shadow true, match:class ^(oneko)$
windowrule = no_blur true, match:class ^(oneko)$
windowrule = border_size 0, match:class ^(oneko)$
windowrule = decorate false, match:class ^(oneko)$
windowrule = rounding 0, match:class ^(oneko)$
windowrule = no_focus true, match:class ^(oneko)$
```

*A ready-to-use snippet is provided in [`hyprland/oneko.conf`](hyprland/oneko.conf).*

---

## 🚀 Usage & Command-Line Options

### Quick Start Examples

```bash
# Classic Cat
oneko &                                   # Default 500px proximity
oneko -proximity 200 &                    # Wake up only when cursor gets closer (200px)

# Tiger Cat
oneko -tora &                             # Default 500px proximity
oneko -tora -proximity 300 &              # 300px proximity radius

# Dog (chases a bone cursor)
oneko -dog &                              # Default dog
oneko -dog -speed 20 &                    # Faster dog
oneko -dog -proximity 400 -speed 18 &     # 400px proximity + faster running speed

# BSD Daemon (chases a trident cursor)
oneko -bsd &                              # Default Beastie
oneko -bsd -fg red &                      # Red Beastie
oneko -bsd -proximity 600 -fg "#ff5555" & # Custom color + 600px proximity

# Sakura Kinomoto (chases Clow Cards)
oneko -sakura &                           # Default Sakura
oneko -sakura -proximity 250 &            # 250px proximity radius

# Tomoyo Daidouji (chases cherry blossom petals)
oneko -tomoyo &                           # Default Tomoyo
oneko -tomoyo -proximity 350 &            # 350px proximity radius

# Kill or stop any running pet
killall oneko
```

### Full Command-Line Options Reference

| Option | Argument | Description | Default |
| :--- | :--- | :--- | :--- |
| `-proximity` | `<dots>` | Distance threshold in pixels to wake up and chase | `500` |
| `-speed` | `<dots>` | Distance pet moves per step | `16` |
| `-time` | `<microseconds>` | Animation update interval (smaller = faster) | `125000` |
| `-idle` | `<dots>` | Mouse speed required to trigger awake animation | `6` |
| `-fg` / `-foreground` | `<color>` | Color of pet outline/body | `black` |
| `-bg` / `-background` | `<color>` | Background tint (transparent by default in 32-bit mode) | `white` |
| `-rv` | *(none)* | Reverse video (inverts foreground/background) | Off |
| `-position` | `+X+Y` | Offset relative to mouse pointer | `+0+0` |
| `-name` | `<name>` | Set custom window name | `oneko` |
| `-help` / `-h` | *(none)* | Display help message and exit | — |

---

## 📂 Project Structure

```
oneko-hyprland/
├── assets/
│   └── demo.gif            # Preview animation of proximity chasing
├── bitmaps/                # 32x32 character sprite frames (XBM)
│   ├── bsd/                # BSD Daemon sprites
│   ├── dog/                # Dog sprites
│   ├── sakura/             # Sakura Kinomoto sprites
│   ├── tomoyo/             # Tomoyo Daidouji sprites
│   └── tora/               # Tiger-striped cat sprites
├── bitmasks/               # Transparency mask bitmaps
├── cursors/                # Custom cursors (mouse, bone, trident, card, petal)
├── hyprland/
│   └── oneko.conf          # Standalone Hyprland window rules snippet
├── src/
│   ├── oneko.c             # Main source code (Hyprland IPC + 32-bit ARGB + Proximity)
│   ├── oneko.h             # Headers and definitions
│   └── patchlevel.h        # Patch version header
├── .gitignore              # Ignored build artifacts
├── LICENSE                 # BSD 2-Clause / Public Domain license
├── Makefile                # Build and installation Makefile
├── oneko.1                 # Standard UNIX man page
└── README.md               # Documentation and usage guide
```

---

## 📜 License & Credits

- Original `xneko` by **Masayuki Koba** (1990).
- BSD Daemon Copyright © 1988 by **Marshall Kirk McKusick**.
- Sakura Kinomoto & Tomoyo Daidouji characters from *"CARDCAPTOR SAKURA"* (CLAMP, Kodansha).
- Hyprland IPC cursor tracking, 32-bit ARGB visual transparency, and proximity wake engine developed for modern Linux Wayland environments.
- Licensed under the **BSD 2-Clause License**. See [`LICENSE`](LICENSE) for details.
