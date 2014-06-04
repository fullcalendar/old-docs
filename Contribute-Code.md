## Contributing Code for a *Feature*

Say you have an idea for a new feature or maybe you've already coded it. How do you incorporate it into the project's main codebase for all to use? Should you submit a [pull request] through Github?

Firstly, there is a lot of demand for a lot of features already. Just look at the [issue tracker][issue tracker features], there are almost 100. Priority goes to features that demonstrate high demand (mostly through number of stars) weighed by ease of implementation. But let's say you've already written the code for a new feature. Even the most perfectly crafted pull request takes work to review and merge. Please realize that it might be a better use of a core contributor's time implementing a different high-priority feature from scratch rather than merging an already-implemented low-priority feature.

Secondly, there might be important groundwork required before implementation on a new feature begins. Often times a better API needs to be fleshed out and the feature made more robust before being deemed appropriate for wider use. Sometimes the feature would be better implemented if certain parts of the codebase were refactored, which requires discussion and planning first.

Github is a great tool for code collaboration and we are very thankful for every code contribution, but we ask that you start the discussion on the [issue tracker] first. Please follow this procedure:

1. **Find an associated ticket on the issue tracker**. This is absolutely required and allows you to prove that there is demand for the feature before anyone else invests further work. If a ticket does not yet exist, go ahead and create it, following the [Feature Request Guidelines], but please realize this probably means it is low priority.

2. **Think of the best API** (option names, public methods) making it as flexible and useful to other developers as possible. Read the existing comments on the ticket for inspiration. Propose your ideas in comments.

3. If you've been given confirmation from others that you are onto something and want to begin coding (or already have), **post a link to your [fork] in the comments of the issue**. Doing this will allow others who care about the feature to play around with it and offer suggestions. Do not submit a pull request yet.

4. Core contributors of the project will monitor the issue tracker, hopefully partake in discussion about the optimal implementation, and will recognize when the feature is ready to be moved over to Github in the form of a pull request. This is when the code collaboration begins.

Please note that all of this might take a long time. We are really sorry. It's not that we don't appreciate your contribution, it's that core contributors don't have enough bandwidth and/or the project might not be ready for your cool new feature just yet.

[pull request]: https://help.github.com/articles/using-pull-requests
[fork]: https://help.github.com/articles/fork-a-repo
[issue tracker]: https://code.google.com/p/fullcalendar/issues/list
[issue tracker features]: https://code.google.com/p/fullcalendar/issues/list?can=2&q=type=Feature
[Feature Request Guidelines]: http://arshaw.com/fullcalendar/wiki/Request-a-Feature/