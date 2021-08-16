## Honoring Reduced Motion Preferences in React

Thanks again to Josh W. Comeau and [his awesome blog](https://www.joshwcomeau.com/react/prefers-reduced-motion/), I have drafted some changes to the  [Health Equity Tracker](https://healthequitytracker.org/)  website which will honor a user's accessibility settings for "user prefers reduced motion". 


![Animated GIFs stopping as user enables preferred reduced motion](https://cdn.hashnode.com/res/hashnode/image/upload/v1629130657408/_4HJtglDS.gif)

On the site are some existing animations which are pretty, but purely decorative.  Due to their motion effects, they could potentially trigger vestibular disorders in some users, resulting in nausea, dizziness, etc. Thankfully, we have an [easy way to account for these animation preferences in pure CSS](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion), however in React and in particular while rendering various images, it requires a different approach.

The step-by-step is outlined clearly on [Josh's post](https://www.joshwcomeau.com/react/prefers-reduced-motion/), but in short I created a new hook called `usePrefersReducedMotion` in our /utils folder, and then instantiated that hook on the two components where animated gifs are rendered. Then, I use a ternary to render either the fully animated gif or a single frame still image, based on the Boolean result of the function call:

``` jsx
            <AimToGoItem
              src={
                prefersReducedMotion
                  ? "img/HET-fields-no-motion.gif"
                  : "img/HET_Fields_1_v2_1000px.gif"
              }
              alt=""
              title="Empower policy makers"
              text="We plan to develop policy templates for local, state, and
            federal policy makers, and help create actionable policies
            with diverse communities."
            />
```

Also, note I have included blank alt text to the image using `alt=""`. This is best practice for situations like this where the images are decorative, and a screen reader user would have no use for knowing what the images contains. However, you do need to specify the blank string as the attribute, otherwise leaving off the `alt` entirely will result in the screen reader trying to parse the element by reading the file name or similar. 

I am still quite new to the world of accessibility (commonly referred to as A11y), so if anything you see here is not in fact best practice I would love to know; leave a comment or direct message me here or on  [Twitter](http://www.twitter.com/benhammondmusic).  