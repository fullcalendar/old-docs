
# Build System Revamp

Mostly to benefit `fullcalendar-scheduler`, but also to benefit `fullcalendar`.

The most immediate goal is to get a unified set of testing utilities to be uses across both repos. I've found writing tests for fullcalendar to be very cumbersome, many times taking longer than writing the actual feature.

I've needed to do dirty things like this
https://github.com/fullcalendar/fullcalendar/blob/master/tests/automated/businessHours.js#L189-L278
which derives from
https://github.com/fullcalendar/fullcalendar-scheduler/blob/master/tests/util/geom.coffee
and
https://github.com/fullcalendar/fullcalendar-scheduler/blob/master/tests/util/time-grid.coffee

and these util directories are a mess:
https://github.com/fullcalendar/fullcalendar/tree/master/tests/lib
https://github.com/fullcalendar/fullcalendar-scheduler/tree/master/tests/util

**I can be the one to unify these test utils**, or more precisely, have the tools in scheduler extend from those in the core, but I don't have the initial setup to do so.

Working on the build system has always been very fun for me, but I also feel very guilty doing it, considering my time is often better spent writing features, so I'm hoping to get some community involvement on this one.

There are a number of fun build-system mini-projects that can be worked on order to achieve this goal and improve both projects in general.


## 1. Move from Bower to NPM

Both projects will continue to be distributed as both Bower and NPM packages, but internally, I want to start using NPM only.

There are a number of appealing reasons to do this, but the main reason is that the previously mentioned test utilities could be accessed as CommonJS modules when coupled with Webpack...


## 2. Use Webpack with Karma

Once NPM is set up, `fullcalendar-scheduler` would depend on the `fullcalendar` project, which would include the `tests/lib/*.js` files into the NPM-published package, which could be accessed as CommonJS modules from the scheduler's tests.

Would need a preprocessor for karma:

https://github.com/webpack/karma-webpack#alternative-usage
(I like the "Alternative Usage" better)

We would want to introduce Webpack into the Karma setup for both fullcalendar and fullcalendar-scheduler. With fullcalendar, we could eventually move the `src/` to ES6 (but not yet).


## 3. Move from Grunt to Gulp

For the fullcalendar-scheduler project.

Also, break down the build tasks into individual files that live in a `tasks/` directory.

*Lower priority.*


## 4. Update directory structure

In both projects:

- convert `buid/` directory to `bin/` and `tasks/`
- move archive-related temp files into `tmp/`

For fullcalendar:

- move manual tests that live in `tests/*.html` to `tests/manual/`
- move automated tests hat live in `tests/automated/` to `tests/`

*Do as final step*. To avoid merge conflicts throughout the refactor.
