# character.js
### Specification and implementation status
This specification is based on information by LF-EMPIRE. [http://lf-empire.de/en/lf2-empire/data-changing/types/167-type-0-characters](http://lf-empire.de/en/lf2-empire/data-changing/types/167-type-0-characters).

# bmp
### file
`array` of file elements
#### file element attributes
- `file(xx-yy)`
- `w`, `h`, `row`, `col`
	- `row` is actually number of columns, or, correctly, number of pictures in one row

example:
```
bmp: {
file: [
  {
    "file(0-69)": "sprite/bandit_0.png", w: 79, h: 79, row: 10, col: 7
  },
  {
    "file(70-139)": "sprite/bandit_1.png", w: 79, h: 79, row: 10, col: 7
  }
],
name: "Bandit",
head: "sprite/bandit_f.png",
small: "sprite/bandit_s.png",
walking_frame_rate: 3,
walking_speed: 4,
walking_speedz: 2,
running_frame_rate: 3,
running_speed: 8,
running_speedz: 1.3,
heavy_walking_speed: 3,
heavy_walking_speedz: 1.5,
heavy_running_speed: 5,
heavy_running_speedz: 0.8,
jump_height: -16.299999,
jump_distance: 8,
jump_distancez: 3,
dash_height: -11,
dash_distance: 15,
dash_distancez: 3.75,
rowing_height: -2,
rowing_distance: 5
}
```

# frames

```
000 - 003: standing
005 - 008: walking
009 - 011: running
012 - 015: heavy_obj_walk
016 - 018: heavy_obj_run
019      : heavy_stop_run
020 - 028: normal_weapon_atck
030 - 033: jump_weapon_atck
035 - 037: run_weapon_atck
040 - 043: dash_weapon_atck
045 - 047: light_weapon_thw
050 - 051: heavy_weapon_thw
052 - 054: sky_lgt_wp_thw
055 - 058: weapon_drink (unimplemented)
060 - 068: punch
070 - 073: super_punch
080 - 081: jump_attack
085 - 087: run_attack
090 - 091: dash_attack
095      : dash_defend
100 - 101: rowing (from falling-frames) (unimplemented)
102 - 109: rowing (rolling)
110 - 111: defend (111, if char is being hit)
112 - 114: broken_defend
115      : picking_light
116 - 117: picking_heavy
120 - 121: catching
122 - 123: catching (punch)
130 - 144: picked_caught
180 - 191: falling (180-185 foward, 186-191 backward)
200 - 202: ice (unimplemented)
203 - 206: fire (203/4 & 205/6) (unimplemented)
207      : tired (unimplemented)
210 - 212: jump
213      : dash
214      : dash (turned back)
   issue : has 1 frame of glitch when performing back dash
215      : crouch
216      : dash
217      : dash (turned back)
218      : stop_running
219      : crouch2 (out of lying)
220 - 229: injured
230 - 231: lying (0=stomach 1=back)
232 - 234: throw_lying_man
```

- issue: characters sometimes trapped in obstacle

### attributes
- `pic`
- `state`
- `wait`
- `next`
- `dvx`, `dvy`, `dvz`
- `centerx`, `centery`
- `hit_a`, `hit_d`, `hit_j`
- `hit_Fa`, `hit_Fj`, `hit_Ua`, `hit_Uj`, `hit_Da`, `hit_Dj`, `hit_ja`
- `mp`
- `sound`

# frame elements

## interactions: `itr`

### attributes
- [`kind`](#itrkind)
- [`x`, `y`, `w`, `h`, `zwidth`](#itrvolume)
- [`dvx`, `dvy`](#itreffect)
- `arest`, `vrest`
- `fall`
- `bdefend`
- `injury`
- [`effect`](#itreffect)

### itr:kind

#### itr:kind:0
normal attack
- `active`
	- trigger an event by intersecting with others
- conditional `team exclusive`
	- only attacks characters of other teams
	- `team neutral` to other object types

#### itr:kind:1
characters with this itr can catch characters that are in state:16 (Dance of Pain).
- `active`
- `team exclusive`
	- only interacts with other teams
- only interact with characters at state 16

extra attributes:
```
"catchingact": [A,B]
```
catcher transits to frame A if catcher approaches the one being caught from the front, otherwiser to frame B
```
"caughtact": [A,B]
```
the one being caught transits to frame A if catcher approaches the one being caught from the front, otherwiser to frame B

for details consult [inter-living-object-interactions.html](http://f-lf2.blogspot.hk/2013/01/inter-living-object-interactions.html).

#### itr:kind:2
characters with this itr can pick up weapons
- `active`
- only interact with pickable items

#### itr:kind:3
characters with this itr can catch characters, without state:16 requirement as kind:3
- `active`
- `team exclusive`
- only interact with characters

other properties same as kind 1

#### itr:kind:4
(unimplemented) characters with this itr can hit others as if he is a heavyweapon.
normally this itr appears in falling frames of a character
- `active`
- conditional `team neutral`
	- can hit any team
	- cannot hit the character that initially threw him

#### itr:kind:5
kind 5 is for weapons

#### itr:kind:6
when a character initiates a punch, he checks if his itr volume intersects with another itr volume of kind:6, if this is true he will transit to frame 70 instead of 60 or 65.
- `passive`
	- other characters trigger an event by intersecting with this itr
- `team exclusive`

#### itr:kind:7
characters with this itr can pick up weapons without causing a frame transition
- `active`
- `key press`
	- require holding down the attack key
- only interact with pickable items

#### itr:kind:8,9,10,11
kind 8,9,10,11 are for special attacks

#### itr:kind:14
living objects with this itr acts as obstacle to characters
- `passive`
- only interact with characters

#### itr:kind:15,16
kind 15,16 are for special attacks

### itr:volume
for details consult [volume specification](http://f-lf2.blogspot.hk/2013/01/scenejs-volume.html).

### itr:effect
with this attribute, an effect will be created when itr:kind:0 occurs.
effects can be purely visual, but most effects include behaviours that affect characters being hit.
effects apply `dvx`,`dvy` as velocity to characters being hit.

## attachment points: `xpoint`

### wpoint
#### attributes
- `kind`
- `x`, `y`
- `weaponact`
- `attacking`
- `cover`
- `dvx`, `dvy`, `dvz`

### opoint
#### attributes
- `kind`
- `x`, `y`
- `action`
- `dvx`, `dvy`
- `oid`
- `facing`

### cpoint

### bpoint
(unimplemented)

## body: `bdy`
#### attributes
- `kind`
	- bdy:kind:0
	- other kind is (unimplemented)
- `x`, `y`, `w`, `h`

for details consult [volume specification](http://f-lf2.blogspot.hk/2013/01/scenejs-volume.html).
