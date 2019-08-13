# Domain Versioning
## August 11th, 2019

There are a lot of broken things about how most of us build software, but few things are as broken as how we use and abuse version numbers.  What is a version number supposed to tell you, exactly?  A version has many jobs:

* Tell you what version of the software you are running
* Tell you certain properties of your software (dev? alpha? production? hotfix? fork? terrifying release ending in '.0'?)
* Tell you how much has changed since some other version string (1.0 versus 1.1 versus 3.4?  Semantic Versioning?)

I have listed these things in approximate most important to least important order, in my objectively perfect opinion.  Unfortunately, achieving the first item is in direct conflict with achieving the second and third.

The best way for a version to tell you what version of the software you are
running would be for it to literally be a version you can check out in source
control.  In git or other sensible DVCS, that means a commit name (i.e. a
sha1).  A somewhat close second would be for it to be a tag, but tags in git
are a global namespace and not to be trifled with, so let's not go there.
Unfortunately, a sha1 cannot tell you anything from those other two bullets,
and anything which can tell you those things would have to be human generated
after the fact, meaning you match it to a commit name you would need to resort
to some sort of lookup (be it tag lookup, or something more tragic like a
release database or something).  This is all Very Bad(TM).

Another problem with the way we typically achieve bullet two and three is, we
normally check that version number into source control.  After all, how else
are we going to get it into our build and release process?  This is incredibly
dumb and responsible for a huge portion of the pain and suffering endured by
release engineers the world over.  First of all, if the source knows what
version it is, then if you have two different branches which are for two
different product lines, you can't ever merge them without making a "lying
merge" where you manually edit out one of the changes (the one that changed the
version number).  This is the biggest reason people end up using cherry-pick
workflows instead of merge workflows, which is yet **another** source of pain
all by itself and which I should address in a future blog post.

It is generally acknowledged to be a desireable trait for your software to be "releaseable at any time", in fact, for any build you might make to be releaseable. But, if that is to happen, how do you assign it a release version without a rebuild?  The answer is, you can't.  People who actually live by this rule do not check in version numbers as most people understand them.  Let's not even get into the shitshow that is maven versions and running maven commands that go insert themselves forcibly into your pom files and update all their version numbers every time you branch or release, or even worse, *people who do the same things with a terrible sed script or something*.  If you've got a variable in your java somewhere that looks like `ANYCO_PRODUCT_VERSION_6_0_X = "6.0.4"`, you have my deepest sympathies.

So we are starting to see some desireable traits surface:

* Must not be part of the source
* Must include the name of the commit
* Must be human-comparable in some way
* Must be able to communicate other properties

# The Answer: Domain Versions

Considering these issues, my colleagues and I have come up with what we feel is a pretty good solution, which I call Domain Versions.  Domain versions come in the form `domain-X-gY` where X is the total count of commits history, and Y is the name of the git commit, normally truncated to some sensible length like 12 or 16 characters.  The "domain" part can be anything you want, but is preferrably a name or version string looking thing.  You could even use semantic versions if you wanted, e.g. `1.0.5-X-gY`.  The domain part should be inserted into your build/release process somehow (checked into a *different* repo where your release code lives, for example, or made a variable of your release job you run, or communicated some other way).  Domain versions have the following benefits:

* You can actually type then directly into git commands - for example, `git checkout -b temp domain-X-gY`.  Try it!  Git sees that it is in the format of a git describe string, so it dutifully ignores everything except the 'Y' part and tries to check that out.
* You can easily generate it in a repo with no ta



====

Title: Domain Versioning
Date: 2019-08-11
Tags: untagged
