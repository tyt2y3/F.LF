# LF2 Background

## Specifications

In LF2 the background is `794 * 550`，if only the scene `794 * 400`

## Algorithm of background

- `avgX` - The average of all (players alive) x coordinates.

- `facing` - Start with 0, and add 1 for every player facing right, and subtract 1 for every player facing left.

- `xLimit` - Where we want the camera to move to.

- `screenW` - 792(LF2 window width)

- `halfW` - 396(792 / 2)

- `numPlayers` - the number of alive players on the current computer.

- `xLimit = (facing * screenW) / (numPlayers * 6) - (halfW + avgX)`

```
   if xLimit < 0, then x Limit = 0
   if xLimit > background's width - screenW, xLimit = background's width - screenW
```

**Notice** where the camera will scrool to assuming the characters don't move from their current posisions. But since we want to implement a smooth scroll, instead of making hte camera's x position = xLimit

`x = currentX + (xLimit - currentX position) / 8`

## Resource

- http://www.lf-empire.de/forum/thread-4597.html

- http://gjp4860sev.myweb.hinet.net/lf2/page16.htm

- http://clover.twgg.org/dc/data/bg/index.html



