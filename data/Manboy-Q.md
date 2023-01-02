There is a mismatch between the documentation and the implementation in that the documentation specifies that the only permission for Defenders is the ability to call the Pause function (`pauseEverything`).

From the docs (https://multisiglabs.notion.site/Architecture-Protocol-Overview-4b79e351133f4d959a65a15478ec0121):
> The Defenders will be a different set of users from Guardians, and their only permission will be the ability to call the Pause function.

However, the implementation also gives the Defenders the ability to call `resumeEverything` and `disableAllMultisigs`.
