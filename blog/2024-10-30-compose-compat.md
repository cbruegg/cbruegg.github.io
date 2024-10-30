# Unbreaking the changes in Compose 1.7

Compose 1.7 brings a few improvements, most notably performance benefits. Updating seems like a no-brainer - but unfortunately it's not always that easy!
I'm working on a set of internal SDKs integrated into various apps. Updating to Compose 1.7 posed an interesting challenge: Who moves first?

In the beginning I believed it doesn't matter, so I opened our `libs.versions.toml`, bumped Compose to 1.7 and voilà... build errors?
It turns out that even though Compose 1.7 technically only is a minor version bump, in practice this is not necessarily the case.
In particular, the internal SDKs use many APIs that are marked as experimental in Compose 1.6.
Google grants itself the liberty to change these APIs as they evolve. Unfortunately they do so without a deprecation cycle.
This includes 

This is a problem: 

# TODOs
- Mention more examples
- Explain compat layer
- Lint rules