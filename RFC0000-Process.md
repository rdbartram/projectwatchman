---
RFC: RFC0000
Author: Ryan Bartram
Status: Draft
Area: Process
Version: 1.0
---

# PowerShell based LCM RFC Process and Guidelines

A RFC (Request for Comments) is a publication to propose design changes and improvements to projectwatchman.
This provides the community an opportunity to provide feedback before code is written where it becomes harder
to change at the risk of compatibility.
The complete list of RFCs are available at https://github.com/rdbartram/projectwatchman

This process was adapted from the PowerShell RFC process.

## Roles

* **Author**: All members of the community are allowed to author new RFCs and can provide feedback to any RFC.
* **Committee**: The design committee that votes to accept or reject an RFC. (the member for which have not yet been determined)
* **Committee Member**: An individual member of the Committee.

## RFC Submission Convention

* When submitted, RFC documents shall follow the naming convention of `RFCNNNN-<Title>.md`.
* Authors of RFCs shall not assign the RFC number (leave the `NNNN` in the filename).
* When the Pull Request is submitted, the author shall check `Allow edits from maintainers` so that the Committee can add the RFC number to the draft, update the title, and fix the filename.

## RFC Template

All RFC documents shall follow the [RFC template](RFCNNNN-New-RFC-Template.md).

## RFC Workflow

RFCs may go through the following stages:

### Draft

This is the initial draft of an RFC posted for comments and considered a work-in-progress.

* New proposed drafts should be submitted as a Pull Request from the Author's fork into the `Draft-Accepted` folder.
* The Author shall ensure that the `Allow edits from maintainers` box is checked so that a Committee Member can assign the next RFC number.
* If the Pull Request has followed the correct template and process, a Committee Member will assign the label `Review - Open for comments` to the Pull Request to indicate that anyone can comment on the content of the submission.
Typically, one or two months is allowed for comments, though this may be extended if a submission is particularly contentious or hasn't received enough feedback for the Committee to feel comfortable making a decision.
* When the Committee closes the comment period, the Author should update the RFC and Pull Request with a new commit to address the comments.
* The Committee shall vote to merge or reject the RFC.
Note: the Committee may be slower to respond to RFCs where the Author has indicated that they do not plan to implement the RFC.

### Draft-Accepted

The Committee has reviewed the RFC and comments, and has voted to accept the RFC as a Draft.

* New comments are not being sought.
* No one has begun implementing the RFC, and there are no current plans to implement the RFC.
In this case, the Committee will create `up-for-grabs` Issues in the [projectwatchman](https://github.com/rdbartram/projectwatchman) repository.

### Experimental

RFCs in the `Experimental` stage have been accepted by the Committee, and code is being written to provide a working example of the proposed design change to get additional feedback.

### Experimental-Accepted

Feedback from the experimental implementation and RFC have been reviewed.

Because this working prototype already exists in preview builds available on GitHub, the community provide feedback on the implementation as issues in the [rdbartram/projectwatchman repository](https://github.com/rdbartram/projectwatchman)

As the engineering team or code contributor works towards a final implementation, they should submit pull requests to projectwatchman in order to keep the RFC in sync with the implementation.
These pull requests shall be reviewed and accepted by the Committee, but a formal vote is not necessary.
The Committee should merely confirm that the changes in the RFC match the working implementation.

### Rejected

RFCs in the `Rejected` state were rejected by the Committee who decided not to proceed further.

### Withdrawn

RFCs in the `Withdrawn` state were rescinded by the RFC Author.

### Final

RFCs in the `Final` state are considered fully complete and implemented in projectwatchman.
Any proposed changes should be made through a new RFC or via an Issue in the [rdbartram/projectwatchman repository](https://github.com/rdbartram/projectwatchman).
New RFCs should reference old RFCs where applicable.

## History

v1.0 - 15-10-2019 - Took initial version from PowerShell-RFC and modified for purpose
