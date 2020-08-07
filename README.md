> minimal reproduction of an error I'm having with Onsen's Vue binding

# Run it
  1. clone the repo
  1. install deps: `yarn`
  1. start the HTTP server: `yarn start`
  1. open the page in your browser
  1. look at the devtools console

The page is self contained, so you can run it any way you like.

# What is the error?

I'm experiencing an error with Onsen's Vue binding when I'm using the navigator
and I update the page stack during or after `mounted` (in Vue terminology). I'm
not actually try to do it like this example shows, it's just this is the
simplest example I could build to reproduce it. I'm using `vue-router` and
I set the page stack during route navigation, which is where it affects me.

I'm open to the suggestion that I'm doing something wrong, but if I'm not, then
this may be a bug.

It's worth noting that the page still renders, so it's not a complete
catastrophe.

The error shows up in the browser JS console when you load the page for this
demo:

```
vue.js:634 [Vue warn]: Error in callback for watcher "pageStack": "TypeError: Cannot read property '$el' of undefined"

found in

---> <VOnsNavigator>
       <Root>

TypeError: Cannot read property '$el' of undefined
    at VueComponent.pageStack (vue-onsenui.js:1163)
    at Watcher.run (vue.js:4567)
    at flushSchedulerQueue (vue.js:4311)
    at Array.<anonymous> (vue.js:1989)
    at flushCallbacks (vue.js:1915)
```

The line `1163` refered to in that stack trace is in
[VOnsNavigator.vue](https://github.com/OnsenUI/OnsenUI/blob/2.10.10/bindings/vue/src/components/VOnsNavigator.vue#L127).
The problem is caused by the fact that `this.$children.length` is `0` so it
finds nothing in the array but then tries to access the `$el` property on
`undefined`. Perhaps the fix is to check if we have children first?

If you look at the code, I've commented with various approaches that all cause
the error: basically any modification to the page stack during or after
`mounted`. If you get in during `beforeMount` then it works as expected.
