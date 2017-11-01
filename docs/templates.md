# Using the Templates and Handlebars Helper Provided by This Package.

This package provides a handful of [Handlebars "partials"](http://handlebarsjs.com/partials.html), which can be used
within your templates.  To use these, you will need to add this package's template directory to your renderer
configuration, as shown in the following example:

```javascript
var fluid = require("infusion");
var my = fluid.registerNamespace("my");

fluid.require("%gpii-diff");

fluid.defaults("my.renderer", {
    type: "gpii.handlebars.standaloneRenderer",
    templateDirs: ["%gpii-diff/tests/templates", "%gpii-diff/src/templates"],
    components: {
        isDiffArray: {
            type: "gpii.diff.helper.isDiffArray"
        }
    }
});

var renderer = my.renderer();
var text = renderer.render("diff-text", gpii.diff.compare("old", "new")); // Returns "-old-+new+\n"
```

To use many of the templates included in this package, your handlebars instance will need to be made aware of the
`isArrayDiff` handlebars helper provided by this package (see below).  The preceding example also adds this to our
renderer.

If you only wish to render a single comparison, you can use the `diff` or `diff-text` page to generate HTML
or text respectively. These will call the appropriate partials to render a single variable as either HTML or text. See
[the tutorial](tutorial.md) for more detailed examples, including examples of how to call the relevant partials
directly.

## The `isArrayDiff` helper

The `isArrayDiff` block helper renders a block if a variable is confirmed to be a diff of array values, and another
if the block is not.  This is intended to distinguish "simple" values (integers, strings, etc.) from Array values.  The
block helper takes a single argument, which is the variable to be evaluated.  You don't necessarily need to use this
yourself, as the `diff` partial (see above) uses the helper to distinguish array from non-array values.

## This Package's HTML Output

The HTML output generated by this package is intended to be visually distinct, but also to convey changes to users who
rely on screen readers.  To do either, you will need to include the CSS file included in this package under
`src/css/gpii-diff.css`.

The strategy we use is to included non-hidden text clipped to zero pixels, that will be announced before and after a
given change.  To give the simplest example, here is an expanded and indented view of a single change from "old" to
"new":

```html
<del>
    <span class="gpii-diff-announcement">Content deleted:</span>
    old
    <span class="gpii-diff-announcement">End deleted content.</span>
</del>
<ins>
    <span class="gpii-diff-announcement">Content added:</span>
    new
    <span class="gpii-diff-announcement">End added content.</span>
</ins>
```

The CSS file included in this package will "clip" all the blocks with the `gpii-diff-announcement` class to zero pixels,
ensuring that they are not visible on screen, but that they will still be read by a screen reader.  This combination has
been tested with Firefox, Chrome, Safari, Opera, Edge, and Internet Explorer, and with both the NVDA and VoiceOver
screen readers.