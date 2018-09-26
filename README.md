# soley-text

> Text on track task desctiption

“Letters on the Track”
“Letters on the Track” is an application that allows a user to make letters of an
English alphabet run on the track with a given speed. It is also possible to change
the speed of a running letter and remove a letter from the track.
Implement the “Letters on the Track” application (“the app”) considering a
suggested UI, given user stories and requirements (see below).
Suggested UI
Suggested UI of the app:

User Stories
1. As a user I enter a letter that is not on the track to the Letter text box, set
itsspeed to N using the Speed slider and click the “GO!” button so that the
letter starts running on the track with the given speed.
2. As a user I enter a letter to the Letter text box that is already on the track
with speed N, set its speed to M and click the “GO!” button so that the
letter changes its speed from N to M and keeps running on the track.
3. As a user I enter a letter to the Letter text box that is already on the track
and click the “STOP” button so that the letter gets removed from the track.

Requirements
1. A newly added to the track letter starts running (moving) from the left
boundary of the track to the right boundary.
2. Reaching a boundary of the track (either left or right) a letter changes its
direction to the opposite direction and continues moving with the same
speed.
3. If a letter on the track meets another letter, then both letters change the
direction to the opposite direction and keep moving with the same speed.
4. Speed of a letter on the track ranges from 0 to 10.
5. It should only be possible to put only small and capital letters of the English
alphabet on the track.
6. The app should be robust, there should be a nice message when the user
makes a mistake. For example, if the user clicks “GO!” without a letter in
the letter box.
7. The app can be written in any language you feel most comfortable in. It is
allowed and encouraged use frameworks and latest technologies.



## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build
```

For detailed explanation on how things work, consult the [docs for vue-loader](http://vuejs.github.io/vue-loader).
