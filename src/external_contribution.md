# Acknowledgements to Contributors

## Purpose of This File

This file aims to **thank and credit external contributors who have participated in the development of Flexible Output View (FOV)**.

It keeps a record of the external contributions received, the work carried out, their review process, and their integration into the project.

An external contribution is considered valid when it corresponds to a code modification, a documentation correction, a translation, the addition of tests, a properly documented bug report, or an external code review.

This document presents the **external contributions received, reviewed, and integrated into the `dev` branch**, as well as the contributors behind them.

---

## External Contributions

### Hugo Payet — @SizzleUnrlsd

**Project:** `web-fov`
**Pull Request:** [#108 — ci: ADD backend and frontend coverage reporting](https://github.com/Flexible-Output-View/web-fov/pull/108)

Hugo Payet, under the GitHub username **@SizzleUnrlsd**, contributed to the `web-fov` project by implementing backend and frontend test coverage tracking.

This contribution followed issue [#61 — Add CI Test Coverage Reporting and README Badge](https://github.com/Flexible-Output-View/web-fov/issues/61), whose goal was to integrate test coverage generation into the CI workflow and display a coverage badge in the `README.md`.

Pull Request #108 notably included the following commits:

- `ci(coverage): ADD backend and frontend coverage reporting`
- `ci(coverage): FIX fork uploads and backend test ordering`
- `fix(frontend): FIX stream availability filtering`

The Pull Request was reviewed by the team, with **@RaphxelS** approving the changes before it was merged into the `dev` branch.

**Thank you to Hugo Payet (@SizzleUnrlsd) for this contribution and for his work on improving the project's quality tracking.**

---

### Maël-Mamadou Diagne — @Mogza

**Project:** `obs-studio-fov`
**Pull Request:** [#51 — UPDATE: About.Info to introduce FOV as a fork in italian language](https://github.com/Flexible-Output-View/obs-studio-fov/pull/51)

Maël-Mamadou Diagne, under the GitHub username **@Mogza**, contributed to the `obs-studio-fov` project by adding the Italian translation of the `About.Info` section.

This contribution followed issue [#28 — FOV about page translation](https://github.com/Flexible-Output-View/obs-studio-fov/issues/28), which requested localizing the content of `About.Info` into the various languages supported by the application.

The contribution added the Italian language file `it-IT.ini` and adapted the FOV presentation text into Italian.

Pull Request #51 modified the localization file, and all **5 automated checks passed**. It was then reviewed, approved, and merged into the `dev` branch.

**Thank you to Maël-Mamadou Diagne (@Mogza) for this contribution and for helping make FOV accessible to Italian users.**

---

### Albéric Flour de Saegher — @albe2

**Project:** `obs-studio-fov`
**Pull Request:** [#52 — DOC: adding the spanish translation of About.info](https://github.com/Flexible-Output-View/obs-studio-fov/pull/52)

Albéric Flour de Saegher, under the GitHub username **@albe2**, contributed to the `obs-studio-fov` project by adding the Spanish translation of the `About.Info` section.

This contribution followed issue [#28 — FOV about page translation](https://github.com/Flexible-Output-View/obs-studio-fov/issues/28), which requested localizing the content of `About.Info` into the various languages supported by the application.

The contribution added the Spanish language file `es-ES.ini` and adapted the FOV presentation text into Spanish. The Pull Request consisted of a single commit, `DOC: adding the spanish translation of About.info`, corresponding to **1 addition and 1 deletion**.

Pull Request #52 was reviewed by **@Slymoz**, who self-assigned the review, approved the changes with the comment `"lgtm"`, and merged commit `af71499` into the `dev` branch. The Pull Request was successfully merged and closed.

**Thank you to Albéric Flour de Saegher (@albe2) for this contribution and for helping make FOV accessible to Spanish users.**

---

### Allan Bandeira — @AllanBandeira91

**Project:** `web-fov`
**Pull Request:** [#196 — TEST: add player component interaction coverage - #196](https://github.com/Flexible-Output-View/web-fov/pull/196)

Allan Bandeira, under the GitHub username **@AllanBandeira91**, contributed to the `web-fov` project by expanding frontend player component test coverage.

This contribution followed issue [#62 — Frontend Component Testing for Multi-Stream Layouts & Player Controls](https://github.com/Flexible-Output-View/web-fov/issues/62), which requested expanding the automated testing suite to cover core frontend components, particularly player controls and layouts.

The contribution added a `fov-player.component.template.spec.ts` file containing new tests for the video player. The Pull Request consisted of a single commit, `TEST: add player component interaction coverage`, corresponding to **253 additions**.

Pull Request #196 was checked by **@LucasLoustalot** and **@RaphxelS**, and then reviewed by **@Slymoz**, who approved the changes with the comment `"lgtm"` and merged commit `a135afa` into the `dev` branch. The Pull Request was successfully merged and closed.

**Thank you to Allan Bandeira (@AllanBandeira91) for this contribution and for helping make FOV more reliable.**

---

## Acknowledgements

We thank **Hugo Payet (@SizzleUnrlsd)**, **Maël-Mamadou Diagne (@Mogza)**, **Albéric Flour de Saegher (@albe2)**, and **Allan Bandeira (@AllanBandeira91)** for their external contributions to the FOV project.

Their contributions were reviewed, validated, and integrated into the `dev` branch, directly supporting the ongoing improvement and evolution of the project.
