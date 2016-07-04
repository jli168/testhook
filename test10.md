Notes for this blog project: 4
===========================

##### about paginator
Laravel comes with powerful paginator. Use it.
check out my commit "add paginator for posts page" to see the code.

##### bootstrap css: how to set width of container?
it seems that the `.container` class always set a fix width, but in situations that in my side bar, i want to use the container class, it will strech to far right, because it still uses the whole width of a page. how to fix it?
my solution is, wrap the container, set the width of wrapper, then in container div, add `style="width: 100%"` to it