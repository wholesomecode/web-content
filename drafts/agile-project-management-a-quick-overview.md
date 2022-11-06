---

title: Agile Project Management – A Quick Overview
post_date: 2020-06-20T09:21:00.000Z
post_status: publish

---

This is a super quick reference guide to agile project management. In this guide we cover the following two approaches:

*   [Agile Sprint Cycle](#agile-sprint-cycle)
*   [Agile Project Management with Kanban](/#agile-project-management-with-kanban)

<a id="agile-sprint-cycle"/>Agile Sprint Cycle
------------------

Here is a diagram of how the Agile Sprint Cycle works. We will explore this below.

![Agile Sprint Cycle](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990043298/2VKhfnSctp.png)Agile Sprint Cycle

### Sprint Kickoff Meeting

For a more [detailed overview of the sprint kickoff view our guide](https://wholesomecode.ltd/blog/sprint-kickoff/). Its key outcomes are to:

*   Set the sprint focus
*   Prioritise the backlog items
*   Add items to the Sprint Backlog

### Implementation

This is the implementation phase, this happens during the sprint when the work is being delivered.

*   Backlog items self assigned by team
*   Pull Request created
*   Solution developed

During the implementation phase the following also happens:

#### Daily Standup (Daily Scrum)

Read the more indepth [Daily Standup guide](https://wholesomecode.ltd/blog/daily-standup-scrum/) here.

*   What did you do last
*   What will you do next
*   Any blockers
*   Change of priority needed?

#### Sprint Refinement

This happens mid-way through the sprint, its purpose is to prepare for the next sprint, but it can also be used to adjust the current sprint if needed.

*   Does current sprint need adjusting?
*   Assess priorities
*   Refine backlog to prepare it for next sprint

### Sprint Retrospective

This happens at the end of a sprint. In this meeting we gather feedback to adjust processes to improve performance and relationships.

*   What went well
*   What went not so well
*   What did we learn

<a id="agile-project-management-with-kanban"/>Agile Project Management with Kanban
------------------------------------

Here is an example of a Wholesome Code project board.

This kind of board is called a ‘Kanban board’. They kind you might find in the GitHub projects view.

Of course the type of project used in this example is a WordPress plugin development.

![Example of a Kanban Board](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990050729/cCyZdWT6i.png)Example of a Kanban Board

### New Items

New issues are placed in the New Items column, until they have been estimated. This estimate should include:

*   Priority (we can [determine the issue priority using MoSCoW](https://wholesomecode.ltd/blog/moscow-work-prioritisation/))
*   Size

This issue is then placed into the project backlog.

### Backlog

This is reviewed during the [Sprint Kickoff](https://wholesomecode.ltd/blog/sprint-kickoff/). Items are then placed into the Sprint Backlog, ensuring the following:

*   High priority items are moved forward first
*   The items moved forward do not exceed the capacity of the team.

### Sprint Backlog

The priorities of items this column are reviewed daily during the [Daily Standup](https://wholesomecode.ltd/blog/daily-standup-scrum/).

Unplanned high priority work may result in some items being descoped or moved back into the backlog.

Items that are actively being worked on are place in the ‘In Progress’ column.

### In Progress

Solutions for issues in this column are actively being worked on by a team member. Once they are completed they are moved into the QA column for review.

### QA (Quality Assurance)

At this point a PR (Pull Request) will have been created for an issue, this goes through a minimum of two stages of review:

*   Code Review
*   Functional Testing

If the solution passes the review stage it will be made live and the related issue will be moved into the **Done** column.

If however it does not pass it will either:

*   Result in minor feedback placed on the PR
*   Have a new issues created clarifying scope or functionality.