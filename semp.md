# SE Management Plan (SEMP)

### Metadata

| Item | Description |
| --- | --- |
| Maintainer(s) | A.M. Smith |

## Terms

Terms | Descriptions
--- | ---
Core Team | Members of the [GitHub Team](https://github.com/orgs/Arrow-air/teams) relevant to product
Release/Version | "Release" and "Version" are used interchangeably in this document

## Overview

The Systems Engineering Management Plan (SEMP) is a guide to managing technical
processes and documentation.

This document outlines:
- Technical processes
- Creation, modification, and review of technical
documentation
- Review processes

## :busts_in_silhouette: Code Collaboration

### :muscle: Contributing

The process of contributing to a GitHub repository is well documented online, and can be distilled down to these steps:

1. Make a copy of the repository on the local machine
2. Initialize a "branch" using git
    - A branch is a set of changes with a label, e.g. `readme-updates`
3. Edit the relevant document(s)
4. "Commit" the changes
	- A commit is a record of a change labeled with a unique number (hash)
    - A branch may have several commits
5. "Push" (send) commits to GitHub

At this stage, GitHub now has at least three branches, or sets of code:
- `main`: The stable source code of the latest release
- `develop`: Code that will eventually become the next release
- The contributor's branch (e.g. `readme-updates`)

A contributor needs to merge their code into the `develop` branch.

To merge the new changes into `develop`, the contributor creates a "Pull
Request" (PR). A PR is an official request to merge code into the `develop`
branch. Other collaborators must review the code, make comments, and give their
approvals prior to merging the branch.

The `main` branch and `develop` branch will deny attempts to update their code outside of a
Pull Request. Pull Requests require a specific number of reviewers.

The official [Arrow Developer Guide](https://www.arrowair.com/docs/contributing/devguide) describes this process in specifics.

### :bookmark: Creating a Release
Creating a new release is a trivial action in the GitHub interface and is already [well documented](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository).

After end-of-release reviews are completed and the `develop` branch is merged into `main`, the Core Team will create a new release from the `main` branch.

These releases are publically accessible. A release consists of:
- A compressed file containing a snapshot all source code
- The unique identifier of the final commit in the release
- A description of major and minor changes since the last release
- A timestamp
- :exclamation: Other file attachments relevant to the release
	- Exported spreadsheets or PDFs from Google Drive whose versions are compatible with this release of the source code.
	- e.g. Requirements Matrix v.1.0.1 (spreadsheet) is implemented by this release of source code

See [this example](https://github.com/rust-lang/rust/releases) of published releases.

## :seedling: Document Life Cycle

A lifetime document flow should account for the following:
- Concept Phase
- Collaboration
- Edit Permissions
- Release Cycle
	- Draft
	- Review
	- Creating a version/release
	
### :bulb: Concept Phase

The conceptual phase starts with a template, outline, or blank document created in [Google Drive](https://www.google.com/drive/). This browser-based software enables concurrent edits to a document by numerous editors.

Prior to a "first" release, there is considerable churn in document contents; adding and removing paragraphs, tweaking sentences, editing for tone. These changes don't need to be tracked until after the first release.

Google Drive allows collaborators to non-invasively post comments and suggestions to a document with the [Comment](https://support.google.com/docs/answer/65129?hl=en&co=GENIE.Platform%3DDesktop) feature.

When the language of the document (version one) is nearly finalized, the primary
author will decide the final format and home of the document.
- Markdown format, tracked by a [version control system](https://en.wikipedia.org/wiki/Version_control)
- Google Doc/Spreadsheet in Google Drive

### :pencil: Markdown Documents (with Git)

#### Overview

In this approach, a document is written in [Markdown](https://www.markdownguide.org/getting-started/) and is stored in a remote (cloud) repository.

Changes to the document are tracked by [git](https://git-scm.com/), a **version control system** (VCS) software. A VCS software enables collaborators to modify code, view the change history, undo changes, and merge different branches of development into a single change set. Each change (or "commit") is identified by a unique integer value.

The repository is hosted by [GitHub](https://github.com/), a service which enables developers to:
- Manage access to source code
- Hold public code reviews
- Deploy release (versioned) packages of code

Advantages of writing in Markdown:
- Plaintext file is easy to review
- Renders to a uniform, readable format
- Renders UML diagrams, code snippets

#### Which Documents?

Typically any internal document that is tightly coupled with product development already being tracked by a git repository.

In our case, these include:
- Software Design Documents (SDD)
- Interface Control Documents (ICD)
- Human System Interface Documents (HSID)

#### Collaboration

Collaboration on the document prior to release one has been covered by the prior Concept Phase section.

Collaboration on a version controlled document is the same as collaboration on source code, which is outlined [here](./#code-collaboration).

#### Edit Permissions

Anyone can make a Pull Request (see [Collaboration](#collaboration)). The code remains in an isolated branch and will not be merged into the `develop` branch without approvals from the Core Team.

#### Release Cycle

##### Version Draft

Changes to the document (or "commits", as they are called in the git workflow) are pushed to the `develop` branch. The `develop` branch is the bleeding edge of development - code and documentation that will eventually be codified as a new release.

##### Version Review

Multiple Pull Requests may be merged into `develop` throughout the course of a release.

At the end of a release cycle, a Pull Request is created to merge the `develop` branch into `main`. 

This is the final opportunity for collaborators to review the documents and make comments. This is also where a [pre-release checklist](checklists/prerelease.md) is addressed.

At this stage, supplementary presentations and video may accompany source code and documentation.

When the review completes and the pre-release checklist is complete, the `develop` branch is merged into `main`.

##### Creating a Release

Markdown documents in a GitHub repository will be packaged with source code when a release is created. Each document does not need its own document version.

Creating a release in GitHub is discussed in [Code Collaboration](#code-collaboration).

### :bar_chart: Google Docs/Sheets (in Google Drive)

#### Overview

In this approach, a document is stored and version-controlled in Google Drive.

Advantages of writing documents in the Google ecosystem:
- Increased control over document formatting
	- margins, colors, fonts, text sizes, alignment, and so on
- "Version" feature to create timestamped snapshots of the document
- "Comment" feature
	- Adds reviewer comments without editing the document itself
- "Approvals" feature for reviews
- Exportable to varying formats

#### Which Documents?

Documents hosted in the Google Drive include:
- Those relying extensively on visual media (graphics, charts, video embeds)
- Spreadsheets
- Documents targeted toward partners, investors, and the general public
    - These documents require greater control over presentation than Markdown allows

Specific documents that these include:
- User Story Matrices
- Requirement Matrices
- Concept of Operations (CONOPS) Documents
- Roadmaps

#### Collaboration

Collaboration on a document in Google Drive is discussed in the [Concept Phase](#concept-phase).

#### Edit Permissions

Anyone with a link can view the document.

Only members of a Core Team have edit permissions.

#### Release Cycle

##### Version Draft

Edits may be made to the Google Drive document until the end of a release.

##### Version Review

Review should occur at the end of a release if changes to that document have been made since the prior release.

Review of a Google Drive document is possible through the [Approvals](https://support.google.com/drive/answer/9387535) feature.

The document should be locked at this time. The primary author shall make a public request for reviewers to leave comments through the "Approvals" feature.

The time window for reviewing the document shall also be public.

This is the final opportunity for collaborators to review the document and make comments. This is also where a [pre-release checklist](FIXME) is addressed, including adherence to the [Writing Style Guide](https://www.arrowair.com/docs/contributing/styleguides/writing).

At this stage, supplementary presentations and video may accompany the documentation.

When the all approval comments are complete, it is time to 

##### Creating a Release

It is possible to [name a new version](https://support.google.com/a/users/answer/9331169?hl=en#6.4) of a document in Google Drive.

Version naming should follow [semver guidelines](https://semver.org/).

:exclamation: Version for documents outside of a GitHub repository may not match the version of the GitHub release.


## :bookmark_tabs: Document Types

For each:
- Brief Description
- Where is this document located and why
- Template

Each of these document types will be located either in the Google Drive or a
GitHub repository. For more information, see [Document Life Cycle](#-document-life-cycle).

### :thought_balloon: Concept of Operations

### :running: User Stories and Requirements Matrices

These documents are separate spreadsheets:
- Requirements
- User Stories

#### Requirements

A Requirements Matrix has the following columns:

UID | Label | Primary Text | Rationale | Parent | Children
--- | --- | --- | --- | --- | ---
0x0234abcd | REQ-0005 | The module shall... | Why? | REQ-0001 | REQ-0006<br/>REQ-0020

We follow NASA's guidelines on requirement definition:
[NASA How to Write a Good
Requirement](https://www.nasa.gov/seh/appendix-c-how-to-write-a-good-requirement/).

#### User Stories

A User Story Matrix has the following columns:

UID | Label | Primary Text | Rationale
--- | --- | --- | ---
0x0234abcd | US-CARGO-0001 | As a passenger, I want... | etc.

### :triangular_ruler: Software Design Document (SDD)

Software implementation

[SDD Template](./templates/sdd.md)

### :heavy_check_mark: Verification & Validation (V&V) Document

[V&V Template](./templates/vnv.md)

https://en.wikipedia.org/wiki/Software_verification_and_validation

### :mailbox_with_mail: Interface Control Document (ICD)

Documents inputs and outputs of a system
diagrams
tables
drawings

[ICD Template](./templates/icd.md)

### Coming Soon

- :diamond_shape_with_a_dot_inside: Configuration Management (CM) Document
- :video_game: Human Systems Interface (HSI) Dcoument