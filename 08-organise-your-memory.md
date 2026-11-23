# 8 - Organise your memory

How to arrange spaces and projects so your agents find what they need and see only what they may.
The reasons are in [decision 0017](https://github.com/knodemd/handbook/blob/main/decisions/0017-organising-spaces.md).

## The rule

**A space is an audience. A project is a topic.**

- A **space** is everything one group of people, and their agents, may read. Access is granted per
  space: an agent that can read a space can read all of it.
- A **project** is a piece of work inside a space: a repository, a client, a product, a course.
  Projects can sit inside other projects.

So: one space per group of people who share the same memory, and as many projects inside it as you
like.

## Start with one space

Put everything you work on in one space, one project per repository or folder of work:

```sh
knode folders add ~/code/site --kind project
knode folders add ~/code/app --kind project
```

Your agents then see your preferences, the people you work with and every decision, whichever
project they are in. Search, the project list and the graph keep a large space easy to move
around: 50 projects in one space are fine.

Keep notes that apply everywhere, such as how you like to work, outside any project.

## When to make another space

Only when someone, or some agent, must not see the rest:

| Reason | Example | What to do |
|---|---|---|
| You share it with other people | A company or a side project with partners | A team space (v0.6), one per group of people |
| No agent should read it | Finances, health | A private space where every agent has `none` |
| An agent must see only part | A client's agent, an agent on a machine you do not trust | A space with only what that agent may read |

Not reasons to split: a project is big, you have many projects, or you want things tidy. Projects
and folders already do that inside one space.

Several projects with the same people go in **one** team space together, however small each
project is.

## Before you share a project

Keep it in your own space, grouped under a parent project named after the people or the
organisation it will go with. Link each project to its parent as `part-of`, in the app or with
`link_nodes`:

```text
Acme (with Ana and Luis)
├── acme-web
└── acme-app
```

The day you share it, that whole branch is what goes to the team space.

## Work across spaces

With a space of your own and team spaces, your agents work like this:

- **They read every space you let them read.** From a personal project you can ask about your
  team's work: the answer comes from the team space, and each result says which space it is from.
- **They write to one space:** the space of the project folder they work in, or the one you tell
  them. Team decisions go to the team space; your own notes stay in yours.
- **Access is one way.** Your agents can read your team spaces; your teammates' agents never read
  your own space.
- **Your view of team work** goes in your space. Link it to the team's note so you can follow it;
  people without access to your space never see that link.

This is how Knode will work once agents read across spaces ([knode#398](https://github.com/knodemd/knode/issues/398))
and notes can refer to other spaces ([knode#403](https://github.com/knodemd/knode/issues/403)).

## Know the current limits

- **An agent reads one space.** `knode mcp` serves your default space, or the one in `--space`. An
  agent connected to a second space does not see your first one, so a second space costs context.
- **Notes follow the agent, not the folder.** Conversations are captured into the space of the
  project folder they were held in, but notes an agent saves go to its own space. With two spaces
  they can end up apart.

Both are tracked in [knode#398](https://github.com/knodemd/knode/issues/398): agents will read every
space they are allowed to and save to the space of the folder they work in. Until then, split only
when access really requires it.

## Examples

**One person.** One space: their apps, their studies, their notes. A private space for finances,
with no agent allowed.

**A freelancer with clients.** One space for their own work and one per client whose material must
stay apart, each with the agent that works on it. Clients that need no separation are projects.

**A small company.** A team space for the company and its products, shared with the partners, and
each partner's own space for everything else. A project two partners run outside the company gets
its own team space with just those two, or stays a project of one partner's space until it is
shared.
