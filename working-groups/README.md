# Working Groups

## What is a Working Group?

A Working Group (WG) is a flash organization of humans and robots devoted to exploring, designing, and synthesizing various aspects of a design space related to blockchain protocol design. The goal of a WG is to produce high-quality, relevant technical and engineering documents that influence the way people design, use, and implement blockchain protocols. These documents include design space overviews, protocol standards, best practices, and various informational documents. This process is loosely inspired by the mission of the IETF and the Ethereum Project Management process.

## Active Working Groups

| Working Group Name | Organizer       | Meeting Cadence    | Milestones                     |
|--------------------|-----------------|--------------------|--------------------------------|
| Short Term Censorship Resistance | [Kydo](https://x.com/0xkydo), [Quintus](https://x.com/0xQuintus), [Dmarz](https://x.com/DistributedMarz)        | Weekly, Thursdays  | Initial setup, Beta release    |
| Encrypted Mempool Standards                | Jane Smith      | Bi-weekly, Mondays | First implementation, Launch   |
| Flashwares                | Alex Johnson    | Monthly, Fridays   | Draft protocol, Public review  |

## Starting Your Own Working Group

### Step 1: Fork and Clone the Repository

1. **Fork the Repository:** Click on [Fork](https://github.com/flashbots/pm/fork) in the Top Right
2. **Clone the Repository:** Clone your fork to your local machine.
   ```bash
   git clone https://github.com/your-username/pm.git
   ```

### Step 2: Copy the 'template' Folder

1. **Navigate to the 'template' Folder:** In your local clone, locate the `template` folder inside of the `working-groups` folder.
2. **Copy the Folder:** Copy the entire `template` folder and rename it to match your working group's focus.

### Step 3: Fill Out the `README.md` File

In your new working group folder, locate the `README.md` file. This file outlines the purpose and structure of your working group. Complete the following fields:

- **Organizer:** Name of the person(s) leading the WG.
- **Meeting Cadence:** Frequency of meetings (e.g., weekly, bi-weekly, monthly).
- **Milestones:** Key milestones and deliverables for the WG.

Example:
```markdown
# Purpose

## Organizer
John Doe (john.doe@example.com)

## Meeting Cadence
Weekly on Thursdays at 3 PM UTC starting January 1st, 1970

## Milestones
1. Initial project setup - MM/DD/YYYY
2. First feature implementation - MM/DD/YYYY
3. Beta release - MM/DD/YYYY
4. Public launch - MM/DD/YYYY
```

### Step 4: Submit Your Working Group Proposal

Once your `Purpose.md` file is complete, submit a pull request (PR) to the main repository for review. The Flashbot's team will review your proposal and, upon approval, merge it!

## Updating Meeting Notes

### Step 1: Create a Meeting Notes Directory

After copying the `template` folder you should have a `meeting-notes` folder as well.

### Step 2: Record Meeting Notes

After each meeting, document the discussion points, decisions made, and action items in a markdown file. Use the following format for the file name: `MM-DD-YYYY-meeting-notes.md`.

Example:
```markdown
# Meeting Notes - MM-DD-YYYY

## Attendees
- John Doe
- Jane Smith
- ...

## Agenda
1. Review action items from last meeting
2. Discuss new feature implementation
3. Plan next steps

## Discussion Points
- Point 1
- Point 2
- ...

## Action Items
- Action Item 1: Responsible Person
- Action Item 2: Responsible Person
```