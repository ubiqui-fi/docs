---
parent: Creating Widgets
title: "Walkthrough: Creating a Widget"
layout: default
nav_order: 4
---

# Walkthrough: Creating a Widget

This article will walk you through creating an example widget, from start to finish.

Our finished widget will include:
1. Content that changes based on its two available widget sizes, small and medium.
2. An external link accessible via button in header
3. Settings configurable by the user that save across sessions
4. A custom button

We begin by creating our new component file. We're going to create the "UW" widget, so we'll name it `UWWidget.svelte`. Inside of our new file, we'll import and create our `Widget` tag.

```svelte
<script lang="ts">
    import Widget from '$lib/Widgets/Widget.svelte';
</script>

<Widget>
    
</Widget>
```

Then, we need to remember to pass in our UID and title.

```svelte
<script lang="ts">
    import Widget from '$lib/Widgets/Widget.svelte';
    let { UID } = $props();
</script>

<Widget {UID} title="uw">

</Widget>
```

Now, this widget has two sizes: small and medium. We're going to change our content depending on what size the user is showing. We'll also make sure to use the `max` property to restrict its sizes.

```svelte
<script lang="ts">
    import Widget, { WidgetSize } from '$lib/Widgets/Widget.svelte';
	import { getWidget } from './Widgets.svelte';
    let { UID } = $props();
</script>

<Widget {UID} title="uw" max={WidgetSize.LARGE}>
    {#if getWidget(UID).w == 1}
        // Small
    {:else}
        // Large
    {/if}
</Widget>
```

Next, we'll add in all our content and styling.

```svelte
<script lang="ts">
    import Widget, { WidgetSize } from '$lib/Widgets/Widget.svelte';
	import { getWidget } from './Widgets.svelte';
    let { UID } = $props();

    let date = new Date();
    let quarter = Math.floor((date.getMonth() + 3) / 3);
    let quarterText = ["Winter", "Spring", "Summer", "Fall"][quarter - 1];
    let quarterTextFull = `${quarterText} ${date.getFullYear()}`;

</script>

<style>
    .uw-container-small {
        display: flex;
        flex-direction: column;
        align-items: center;
        height: 100%;
        max-width: 100%;
        position: relative;
        overflow: hidden;
        border-radius: 6px;
    }

    .uw-container-medium {
        display: flex;
        height: 100%;
        max-width: 100%;
        border-radius: 6px;
        position: relative;
        overflow: hidden;
    }

    .text-elements {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        position: absolute;
        right: 0;
        padding: 10px;
        padding-right: 10%;
        top: 50%;
        transform: translateY(-50%);
        background: var(--widget-color);
        border-radius: 6px;
    }

    .uw-logo {
        max-width: 50px;
        margin: 10px;
    }

    .quarter-text {
        font-size: 30px;
        margin: 0;
    }

    .cherry-blossoms {
        position: absolute;
        bottom: 0;
        height: 100%;
        left: 50%;
        transform: translateX(-50%);
        mask-image: linear-gradient(to bottom, transparent, black);
    }

    .cherry-blossoms-medium {
        position: absolute;
        height: 100%;
        mask-image: linear-gradient(to left, transparent, black);
    }
</style>

<Widget {UID} title="uw" max={WidgetSize.MEDIUM}>
    {#if getWidget(UID).w == 1}
        <div class="uw-container-small">
            <img src="src" alt="University of Washington Logo" class="uw-logo" />
            <p class="quarter-text">{quarterTextFull}</p>
            <img src="src" class="cherry-blossoms" alt="University of Washington Cherry Blossoms" />
        </div>
    {:else}
        <div class="uw-container-medium">
            <img src="src" class="cherry-blossoms-medium" alt="University of Washington Cherry Blossoms" />
            <div class="text-elements">
                <img src="src" alt="University of Washington Logo" class="uw-logo" />
                <p class="quarter-text">{quarterTextFull}</p>
            </div>
        </div>
    {/if}
</Widget>
```

And with this we end up with a nice result!

<div style="display: flex;">
    <img src="../assets/img/example_widget_small.png" style="max-width: 250px;">
    <img src="../assets/img/example_widget_medium.png" style="max-width: 510px; margin-left: 15px;">
</div>

As you can see, the content is different for the two sizes. Neat!

Next, let's add an external link to the UW website. This is as simple as adding `openURL` to the widget tag.

```svelte
// Script and styling up here
<Widget {UID} title="uw" max={WidgetSize.MEDIUM} openURL="https://www.washington.edu/">
    // Content here
</Widget>
```

Now we have a button that opens the UW website!

<img src="../assets/img/example_widget_link.png" style="max-width: 250px;">

What if our user gets bored of seeing the cherry blossoms everyday? Let's make it so they can change that image!

But hold on - we don't want our users to put just any image in there. What if they put a picture of a cougar (*shivers*)? We can't have that! We'll curate a few images and let them choose between them.