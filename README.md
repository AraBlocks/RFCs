# Ara RFCs

Many changes, including bug fixes and documentation improvements can be implemented and reviewed via the normal GitHub pull request workflow.

Some changes though are "substantial", and we ask that these be put through a bit of a design process and produce a consensus amongst the Ara community and the Ara Core team.

The "RFC" (request for comments) process is designed to give contributors a structured pathway to present and integrate new features into the Ara project. The active RFC list can be found [here](https://github.com/AraBlocks/rfc/pulls).

## When to open an RFC

You must follow this process when you plan to make "substantial" changes to the Ara protocol or its documentation. The following are some examples of features that would require an RFC:

* An update to the Smart Contracts that exist within the protocol, proposing a new way to calculate and distribute rewards
* A change to the existing Filesystem Hierarchy found within AFS

On the contrary, items that do not have to be submitted as an RFC:

* Rephrasing, reorganizing, refactoring, or otherwise "changing shape does not change meaning".
* Additions that strictly improve objective, numerical quality criteria (warning removal, performance improvements, platform coverage etc)
* Additions only likely to be noticed by other developers-of-ara, invisible to users-of-ara.

## Opening a RFC

In short, to get a major feature added to Ara, one usually first gets
the RFC merged into the RFC repo as a markdown file. At which point the RFC is considered active for the potential inclusion in future versions of the platform.

* Fork the RFC repo http://github.com/AraBlocks/rfcs
* Copy `0000-template.md` to `text/0000-my-feature.md` (where
'my-feature' is descriptive. Don't assign an RFC number yet). If the `text` directory does not exist, please create it prior to copying.
* Fill in the RFC. Put care into the details: **RFCs that do not
present convincing motivation, demonstrate understanding of the
impact of the design, or are disingenuous about the drawbacks or
alternatives tend to be poorly-received**.
* Submit a pull request. As a pull request the RFC will receive design feedback from the larger community, and the author should be prepared to revise it in response.
* Build consensus and integrate feedback. RFCs that have broad support are much more likely to make progress than those that don't receive any comments.
* Eventually, the team will decide whether the RFC is a candidate for inclusion within Ara.
* RFCs that are candidates for inclusion in Ara will enter a "final comment period" lasting 3 calendar days. The beginning of this period will be signaled with a comment and tag on the RFCs pull request.
* An RFC can be modified based upon feedback from the team and community. Significant modifications may trigger a new final comment period.
* An RFC may be rejected by the team after public discussion has settled and comments have been made summarizing the rationale for rejection. A member of the team should then close the RFCs associated pull request.
* An RFC may be accepted at the close of its final comment period. A team member will merge the RFCs associated pull request, at which point the RFC will become 'active'.
* An RFC may be accepted or rejected prior to the close of its final comment period if all reviewing members of the RFC come to consensus about its acceptance or "rejectance".

## RFC Review

Once an RFC becomes "active" then authors may implement it and submit the feature as a pull request to the Ara repo that the RFC is relevant to. Being "active" is not a rubber stamp, and in particular still does not mean the feature will ultimately be merged; it does mean that in principle all the major stakeholders have agreed to the feature and are amenable to merging it.

Furthermore, the fact that a given RFC has been accepted and is 'active' implies nothing about what priority is assigned to its implementation, nor whether anybody is currently working on it.

Modifications to "active" RFCs can be done in follow-up pull requests. We strive to write each RFC in a manner that it will reflect the final design of the feature; but the nature of the process means that we cannot expect every merged RFC to actually reflect what the end result will be at the time of the next major release.

## RFC Integration

The author of an RFC is not obligated to implement it. Of course, the RFC author (like any other developer) is welcome to post an implementation for review after the RFC has been accepted.

If you are interested in working on the implementation for an 'active' RFC, but cannot determine if someone else is already working on it, feel free to ask (e.g. by leaving a comment on the associated issue).

**Ara's RFC process owes inspiration to the [React](https://github.com/reactjs/rfcs) and [Rust](https://github.com/rust-lang/rfcs) RFC processes.**
