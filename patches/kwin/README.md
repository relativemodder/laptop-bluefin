# KWin Overview Gesture Patch

This directory contains the reproducible KWin overlay used by the image.

## Base Source

The patch is pinned to KWin commit:

```text
392e8c077763493db089e5b865ddcae7c1370d00
```

That commit is the KWin `6.8.80` source used to verify the build.

## Behavior

- With one desktop row, touchpad overview/grid gestures use three fingers.
- With multiple desktop rows, three-finger vertical swipes remain desktop navigation and overview/grid fall back to four fingers.
- Horizontal three- and four-finger desktop gestures keep their axis-specific offset and do not get reset by the cancelled gesture on the other axis.

## Apply

```bash
git clone https://invent.kde.org/plasma/kwin.git
cd kwin
git checkout --detach 392e8c077763493db089e5b865ddcae7c1370d00
git apply --check /path/to/laptop-bluefin/patches/kwin/overview-3-finger.patch
git apply /path/to/laptop-bluefin/patches/kwin/overview-3-finger.patch
```

The existing BlueBuild recipe does not build KWin from source by itself. A downstream KWin RPM/container build must apply this patch before compiling and replacing the base KWin packages.
