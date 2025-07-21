---
title: Creating Widgets
layout: default
nav_order: 3
---

# Creating Widgets
{: .no_toc }

#### TABLE OF CONTENTS
{: .no_toc }
1. Design your widget
2. Create your widget using the base component
3. Register your widget
4. Create and add your widget's skeletons and preview
{:toc}

## Design your widget
So you want to create a widget, huh?

Your first step is to design what you want your new widget to look like and its behavior. It's a good idea to use software like Figma to do so. Make sure to design your widget to the correct dimensions.

<img src="/assets/img/sizing_guide.png" />

## Create your widget using the base component

Next, create your new component file inside of the `Widgets` folder. It's a good idea to name your widget something like `NoteWidget.svelte` or `ImageWidget.svelte`. 

Then, import and add the `Widget` component. Access your widget's `UID` from `$props` and pass it through to your widget component. Additionally, make sure to give your widget a title. This is the text that will show in the top left of your widget.

```svelte
<script lang="ts">
    import Widget from "$lib/Widgets/Widget.svelte";
    let { UID } = $props();
</script>

<Widget {UID} title="memo">
    // Your widget content will go here.
</Widget>
```

There are many more properties you can set on the Widget component. See [The Widget Component](./the-widget-component.html) for more information.

Like normal Svelte, if you want to have your widget react to changes in variable state, you need to declare them with `$state()`. However, using `$state` will not save variable state across sessions. To do that, see [Data Management](./data-management.html).

## Register your widget

Once you have your widget in a good place, it's time to register it so we can see it in our app.

To register your widget, simply navigate to `registry.svelte.ts` within the `Widgets` folder. Then, add your widget as an entry to the `registeredWidgets` object. Make sure the key of your new entry is exactly the same as what you set as the `title` for your widget component.

```js
export const registeredWidgets = {
    memo: NoteWidget,
    count: CounterWidget,
    moment: ImageWidget,
    todo: TodoWidget,
    weather: WeatherWidget,
    // Register your widget here and don't forget to import it!
};
```

Now that you have registered your widget, you should see it in the Widget picker. But there's a problem - the image preview doesn't load! That's because we now need to add our previews and skeletons.

## Create and add your widget's previews and skeletons

Good news - there's no coding for this part. Bad news - you need to be very precise in your file naming for this part. Make sure to be careful or things won't work!

### Previews

First things first, let's get your widget previews. Start by setting up your widgets how a user might use them - configure them so they're showing useful data. Repeat this for all the possible sizes the widget could be.

<img src="/assets/img/example_screenshot_setup.png">

Next, right click on the outer edge of one of your widgets and open inspect element on it. Head over to the inspector and ensure the `Card` element is selected. Then, right click it and click **Screenshot Node**. 

<img src="/assets/img/example_screenshot_inspect.png">

Your browser should take a screenshot of exactly just your widget. We'll use this to show users exactly how the widget looks in use. 

Repeat this process for all the widget's sizes.

Next, we're going to rename our screenshots. **It's very important you name your screenshots exactly as described here.**

If your screenshot is of the small widget size for your widget, name it `{title}-small.png`, where `{title}` is replaced by the title of your widget. For example, for `NoteWidget.svelte`, its small preview screenshot is titled `memo-small.png`, because its title is `memo` (and not Note). For the medium widget size, name it with `{title}-medium.png`, and for large with `{title}-large.png`. 

Finally, move your screenshots inside of the `widget-previews` folder. When you build again, your previews should appear in the widget picker!

### Skeletons

Unfortunately, there's no easy way to automatically generate skeletons based on your widget designm. Rather, it's a good idea to work off your widget previews and block out information to create your skeletons.

Start by importing your previews into a design software such as Figma. Then, draw elements over your preview, such as the card background and title. Next, block out information into rectangles with a fill of `#000000` and `10%` opacity. 

<img src="/assets/img/example_wireframe.png">

Once you have the key information blocked out, we need to export our skeleton as an svg. **We want to export only the skeleton rectangles, not the widget's background nor title.**

Next, name your wireframe following the exact same naming scheme you used for the previews. However, this time, simply put your named skeleton image into the `skeletons` folder. 

If you build and test the extension now, you will see the skeletons do function. However, there's one more step to ensure skeleton compatibility with themes.

In VSCode or another editor, open the `svg` files in the text editor. Then, perform a find-and-replace, finding all mentions of the color `black` and replacing it with `var(--color)`. This will allow our theme to set the color of the skeleton. Additionally, add the id `skeleton` to the top `svg` tag. For example, the `moment-small.svg` skeleton originally looked like this:

```
<svg width="200" height="248" viewBox="0 0 200 248" fill="none" xmlns="http://www.w3.org/2000/svg">
<rect width="200" height="248" rx="4" fill="black" fill-opacity="0.1"/>
</svg>
```

but after our modifications, looks like this:

```
<svg width="200" height="248" viewBox="0 0 200 248" fill="none" xmlns="http://www.w3.org/2000/svg" id="skeleton">
<rect width="200" height="248" rx="4" fill="var(--color)" fill-opacity="0.1"/>
</svg>
```

After building, your skeletons will now respond to our themes!