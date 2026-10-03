# KWin Patches

This directory contains the reproducible KWin overlay used by the image.

## Base Source

The patches are pinned to KWin commit:

```text
ab7df7ccb7c6af20f4b279cd6220f7cd3d2267d7
```

That commit is the KWin `6.8.80` source used to verify the build. It is the same
commit that `files/scripts/build-kwin.sh` fetches.

## Patches

The patches are applied in order. The second one touches the same functions as
the first, so it only applies once `overview-3-finger.patch` is in place.

### overview-3-finger.patch

- With one desktop row, touchpad overview/grid gestures use three fingers.
- With multiple desktop rows, three-finger vertical swipes remain desktop navigation and overview/grid fall back to four fingers.
- Horizontal three- and four-finger desktop gestures keep their axis-specific offset and do not get reset by the cancelled gesture on the other axis.

### gesture-fling-commit.patch

- Desktop switches are decided by where the gesture would coast to a halt if the
  fingers kept decelerating the way they already are, instead of by how far they
  travelled. The projection uses only the last 150 ms of movement and niri's
  deceleration model (speed drops by 0.997 every millisecond).
- The projected offset is rounded to the nearest desktop, so the switching
  threshold becomes half a desktop instead of a fixed quarter. A fast flick
  switches desktop even when the fingers barely moved; a drag that comes to rest
  short of the threshold does not.
- The velocity at the moment the fingers are lifted is handed to the slide
  animation, which runs on a critically damped spring (`stiffness 1000`,
  `damping ratio 1.0`) instead of the old overdamped one, so the transition
  continues with the speed of the fingers instead of restarting from rest.

## Apply

```bash
git clone https://invent.kde.org/plasma/kwin.git
cd kwin
git checkout --detach ab7df7ccb7c6af20f4b279cd6220f7cd3d2267d7

git apply --check /path/to/laptop-bluefin/patches/kwin/overview-3-finger.patch
git apply /path/to/laptop-bluefin/patches/kwin/overview-3-finger.patch

git apply --check /path/to/laptop-bluefin/patches/kwin/gesture-fling-commit.patch
git apply /path/to/laptop-bluefin/patches/kwin/gesture-fling-commit.patch
```

The BlueBuild recipe builds KWin from source in a `containerfile` module, applies these patches, and installs the resulting files into the image.
