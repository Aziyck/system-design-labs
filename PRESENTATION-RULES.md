# Lab presentation and submission rules

These rules apply to every lab in this course. The Romanian version is
[`PRESENTATION-RULES-ro.md`](./PRESENTATION-RULES-ro.md). Both versions carry
the same requirements.

## 1. Where you publish your work

Fork this repository once, at the start of the course. Forking is a single
button on this page; it is the only new Git operation the course asks of you.

- Your fork must stay **public**.
- Use the same fork for the whole course. Do not create a second fork or a
  second repository.
- You may rename your fork from its Settings page if you prefer another name.
  This is optional.
- Write your solutions in **English or Romanian**. Pick one and keep it for
  every lab.

When new labs are published here, open your fork and press **Sync fork** to
bring them in. It is a button, not a command. A sync never touches your own
files.

## 2. Repository structure

Add one `SD/` folder at the top of your fork and keep every solution inside it:

```text
SD/
  README.md
  .gitignore
  lab-1/
    README.md
    assets/      (optional)
  lab-2/
  lab-3/
  lab-4/
  lab-5/
  lab-6/
  lab-7/
  lab-8/
```

- One folder per lab, named `lab-1` through `lab-8`.
- Each lab folder holds exactly one `README.md`. That file is your solution.
  GitHub renders it as soon as someone opens the folder, so your work is
  visible without a click.
- `SD/README.md` is a different file: it states your name, your group, the
  language you chose, and links every lab folder.
- `lab-N/assets/` is optional. Create it only if you keep screenshot files
  next to a solution (see section 4).

Your fork also contains `labs/`, `lectures/` and the root `README.md`. Those
come from this repository — leave them alone. Everything you write goes inside
`SD/`, so a sync can never conflict with your work.

## 3. Presentation order and pace

- **At most two labs per session.** A session never covers three.
- Labs are presented in order. You cannot present `lab-4` before `lab-3`.

| Stage | What is presented |
| ----- | ----------------- |
| 1     | Labs 1-3          |
| 2     | Evaluation 1      |
| 3     | Labs 4-6          |
| 4     | Evaluation 2      |
| 5     | Labs 7-8          |

A lab is presented only after its `README.md` is committed and pushed. Work
committed during or after the presentation does not count for that session.

## 4. Research must be demonstrated explicitly

Stating that you researched something is not enough. Demonstrate it in one of
two ways:

- **Screenshots**, either embedded directly in `README.md` or committed
  under `SD/lab-N/assets/` and referenced from it, or
- a **live demonstration** during the presentation.

For every source, give the URL. For every lab that asks for research, state
which scope decision the research changed or confirmed.

## 5. A lab counts as presented when

- [ ] Its `README.md` exists in the correct folder and is pushed to your
      public fork.
- [ ] It contains every section required by that lab's `README.md`.
- [ ] Its research is demonstrated by screenshots or live.
- [ ] You can answer questions about the decisions in it.

## 6. Language

You may work in English or Romanian. Neither choice relaxes any rule: both
versions of this document describe the same requirements.
