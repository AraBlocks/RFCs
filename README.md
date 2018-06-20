ARA Request For Comments (RFC)
==============================

A repository of Request For Comments (RFC) documents for ARA.

## 0. Table Of Contents

1. [Introduction](#1-introduction)
2. [RFC Categories](#2-rfc-categories)
3. [RFC Considerations, Submission, & Review Process](#3-rfc-considerations-submission-review-process)

## 1. Introduction

Many changes, including bug fixes and documentation improvements can be implemented and reviewed via the normal GitHub pull request workflow.

Some changes though are "substantial", and we ask that these be put through a bit of a design process and produce a consensus amongst the ARA community and the ARA Core team.

The RFC process is designed to give contributors a structured pathway to present and integrate new features into the ARA project. The active RFC list can be found [here](https://github.com/arablocks/rfc/pulls).

## 2. RFC Categories

RFC documents are organized by single category names. They are arbitrary and can grow as new scopes are introduced into the respository. As they are created in RFC documents, they are initially created as Github issue labels. If a merged RFC introduces a new category, it should be added here.

* [API](https://github.com/arablocks/rfcs/pulls?q=label:api)
* [Identity](https://github.com/arablocks/rfcs/pulls?q=label:identity)
* [Interoperability](https://github.com/arablocks/rfcs/pulls?q=label:interoperability)
* [Security](https://github.com/arablocks/rfcs/pulls?q=label:security)

## 3. RFC Consideration, Submission, & Review Process

RFC document authors should follow the outlined process in this section to submit a RFC to this repository

### 3.1 RFC Consideration - When To Open A RFC

You must follow this process when you plan to make "substantial" changes to the ARA protocol or its documentation. The following are some examples of features that would require an RFC:

* An update to the Smart Contracts that exist within the protocol, proposing a new way to calculate and distribute rewards
* A change to the existing Filesystem Hierarchy found within AFS

On the contrary, items that do not have to be submitted as an RFC:

* Rephrasing, reorganizing, refactoring, or otherwise "changing shape does not change meaning".
* Additions that strictly improve objective, numerical quality criteria (warning removal, performance improvements, platform coverage etc)
* Additions only likely to be noticed by other developers of ARA, invisible to users of ARA.

### 3.2 Submission - How To Open A RFC

The submission of a RFC should follow the steps outlined in this section.

1. Fork the RFC repo http://github.com/arablocks/rfcs
2. Copy `0000-template.md` to `text/0000-my-feature.md` (where
'my-feature' is descriptive. Don't assign an RFC number yet). If the `text` directory does not exist, please create it prior to copying.
3. Fill in the RFC. Put care into the details: **RFCs that do not present convincing motivation, demonstrate understanding of the impact of the design, or are disingenuous about the drawbacks or alternatives tend to be poorly-received**.
4. Submit a pull request. As a pull request the RFC will receive design feedback from the larger community, and the author should be prepared to revise it in response.
5. Build consensus and integrate feedback. RFCs that have broad support are much more likely to make progress than those that don't receive any comments.

#### Final Comment Period

RFCs that are candidates for inclusion in ARA will enter a "final comment period" lasting 3 calendar days. The beginning of this period will be signaled with a comment and tag on the RFCs pull request.

An RFC can be modified based upon feedback from the team and community. Significant modifications may trigger a new final comment period.

#### Acceptance & Rejectance

An RFC may be rejected by the team after public discussion has settled and comments have been made summarizing the rationale for rejection. A member of the team should then close the RFCs associated pull request.

An RFC may be accepted at the close of its final comment period. A team member will merge the RFCs associated pull request, at which point the RFC will become 'active'.

An RFC may be accepted or rejected prior to the close of its final comment period if all reviewing members of the RFC come to consensus [1] about its acceptance or "rejectance" [2].

### 3.3 RFC Review

Once an RFC becomes "active" then authors may implement it and submit the feature as a pull request to the ARA repo that the RFC is relevant to. Being "active" is not a rubber stamp, and in particular still does not mean the feature will ultimately be merged; it does mean that in principle all the major stakeholders have agreed to the feature and are amenable to merging it.

Furthermore, the fact that a given RFC has been accepted and is 'active' implies nothing about what priority is assigned to its implementation, nor whether anybody is currently working on it.

Modifications to "active" RFCs can be done in follow-up pull requests. We strive to write each RFC in a manner that it will reflect the final design of the feature; but the nature of the process means that we cannot expect every merged RFC to actually reflect what the end result will be at the time of the next major release.

### 3.4 RFC Integration

The author of an RFC is not obligated to implement it. Of course, the RFC author (like any other developer) is welcome to post an implementation for review after the RFC has been accepted.

If you are interested in working on the implementation for an 'active' RFC, but cannot determine if someone else is already working on it, feel free to ask (e.g. by leaving a comment on the associated issue).

## Inspiration

**ARA's RFC process owes inspiration to the [React](https://github.com/reactjs/rfcs) and [Rust](https://github.com/rust-lang/rfcs) RFC processes.**

## References

* [1] - *https://github.com/arablocks/rfcs/pull/5#issuecomment-398454718*
* [2] - *https://english.stackexchange.com/questions/419955/is-rejectance-a-proper-legitimate-word/419979#419979*
