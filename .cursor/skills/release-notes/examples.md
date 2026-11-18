# Sample release notes

This is the Slack source. Pasting it renders bold section labels, a numbered feature, lettered details, a test bullet, and linked GitHub URLs.

## Single feature (target layout)

:rocket: Release | 17 November 2026 | Environment: Production

:package: *What's in this release*
1. Adding new fields in Project Tracker Export
    a. Project Tracker export now has 38 assignee columns, under an Assignees group in the column picker. These cover TC through Liaising, Design, Survey, I&C, and Project Coordinator, including the manager columns taken from each person's reporting chain.
    b. Few of the previously existing fields now sit in the Assignees group, so that they are not added second time.
    c. The existing columns are unchanged.

:white_check_mark: *Tested & Verified*
- Verified the full exports with the new columns completed: 232 columns for PC and 230 for Sales.

:warning: *Known Issues / Limitations*
None

:link: *GitHub Release Links*
Commons: https://github.com/freyr-tl/freyr-fep-commons/pull/1226
Notifications: https://github.com/freyr-tl/freyr-fep-backend-notifications/pull/1082
Web: https://github.com/freyr-tl/freyr-fep-web-app/pull/2834

:bust_in_silhouette: Released by: Naidu

## Several features

:rocket: Release | 16 November 2026 | Environment: Production

:package: *What's in this release*
1. Stock Adjustment Enhancements
    a. Approval is now two levels: Inventory AGM (L1), then Inventory DGM (L2). The warehouse quantity changes only on the final approval.
    b. A request raised by an Inventory AGM starts at L2. A request raised by an Inventory DGM or Inventory COO is approved immediately and the quantity is updated.
    c. Inventory COO can approve or reject any open request. That decision is final.
    d. If L2 rejects a request the AGM already approved, it leaves the AGM Approved list and shows only under Rejected.
2. Stock adjustment requests list
    a. Pending, Approved, Rejected, and All follow the viewer's role. Pending + Approved + Rejected matches All.
    b. The current approver is shown next to the request status.
    c. Request status, current approver, and approval-date filters work with the Requested On range. The date range stays applied when another filter is added.
    d. Past interactions show the requested quantity on the AGM approval line and the request remarks on the created line.
3. Weekly report
    a. Every Monday at 8:00 AM IST, an email goes out for stock adjustments approved in the previous Monday–Sunday. The requests themselves are not changed.
    b. Inventory AGM and Inventory DGM can also download it from Settings → Exports.
4. Emails
    a. New mails for L1 required, L2 required, final approval, and rejection were added.

:white_check_mark: *Tested & Verified*
- Verified on UAT for Inventory roles: the list buckets, the current approver, the filters, Requested On staying applied with a status filter, and the badge counts adding up to All.
- An L2 rejection no longer stays in the AGM Approved bucket.
- Inventory COO can approve or reject an open request.
- The weekly report covers the previous week's approved requests only.

:warning: *Known Issues / Limitations*
None

:link: *GitHub Release Links*
Commons: https://github.com/freyr-tl/freyr-fep-commons/pull/1262
Backend: https://github.com/freyr-tl/freyr-fep-backend-core/pull/4706
Web: https://github.com/freyr-tl/freyr-fep-web-app/pull/2917
Notifications: https://github.com/freyr-tl/freyr-fep-backend-notifications/pull/1114

:bust_in_silhouette: Released by: Naidu

## Two features, fewer links

:rocket: Release | 10 November 2026 | Environment: Production

:package: *What's in this release*
1. Vendor PO Approval flow issues
    a. Modules prices deviation uses price per watt peak on the no. of watt peak of the module to calculate the base price and deviation.
2. Approvals List page bucket and sections UI enhancement
    a. Display counts on the Approvals section as badges.

:white_check_mark: *Tested & Verified*
- Validated the Vendor PO creation flow.
- Validated the Vendor PO creation with Modules and other sku items.
- Verified the deviation calculation for different sku items.
- Verified the Approvals List page and each section counts from badges.

:warning: *Known Issues / Limitations*
None

:link: *GitHub Release Links*
Backend: https://github.com/freyr-tl/freyr-fep-backend-core/pull/4611
Web: https://github.com/freyr-tl/freyr-fep-web-app/pull/2868

:bust_in_silhouette: Released by: Naidu
