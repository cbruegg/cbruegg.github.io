# Unbreaking the changes in Compose 1.7

Compose 1.7 brings a few improvements, most notably performance benefits. Updating seems like a no-brainer - but unfortunately it's not always that easy!

## Updating to Compose 1.7: Who moves first?

I'm working on a set of internal SDKs integrated into various apps. Updating to Compose 1.7 posed an interesting challenge: Who moves first?

In the beginning I believed it doesn't matter, so I opened our `libs.versions.toml`, bumped Compose to 1.7 and voilà... build errors?
It turns out that even though Compose 1.7 technically is only a minor version bump, in practice this is not necessarily the case.
In particular, the internal SDKs I'm working on use many APIs that are marked as experimental in Compose 1.6.
Google grants itself the liberty to change these APIs as they evolve. Unfortunately they do so without a deprecation cycle.
This includes innocuous-looking APIs like `HorizontalPager`. Its parameter `flingBehavior` used to be `SnapFlingBehavior`
and now requires a `TargetedFlingBehavior` - a breaking change.

## Idea 1: The SDKs update first

This is a problem. Let's assume both the internal SDK as well as the host app use the `HorizontalPager` component.
If the internal SDK updates to Compose 1.7 first, this causes a transitive dependency for the host app to Compose 1.7 as well.
As the app is still using the Compose 1.6-version of `HorizontalPager`, the app's call to this composable is now broken, forcing
the app's development team to immediately work on restoring Compose 1.7 compatibility. As an SDK developer, this is not a situation I want
to put an app developer in.

## Idea 2: Wait for the host apps to update

Unfortunately we also can't just wait for the host app to upgrade to Compose 1.7. As there can only be a single Compose version on the classpath,
the moment the app depends on Compose 1.7, our SDKs would cause runtime crashes as they are trying to call Compose 1.6-APIs that are no longer available
or changed in Compose 1.7.

Using a naive approach, all internal and external modules using experimental Compose APIs of an app have to move to Compose 1.7 at the same time.
In many organizations, this is not desirable.

# TODOs
- Mention more examples and show them side-by-side
- Confirm that examples I used really have been broken
- Explain compat layer
- Lint rules
- Show evidence for widespread experimental API usage: https://github.com/search?q=%40OptIn%28Experimentalfoundationapi%3A%3Aclass%29&type=code