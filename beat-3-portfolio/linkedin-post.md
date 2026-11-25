I built an automated pre-submission checklist that grades pull requests against their plan, test results, and repository rules before a human maintainer ever sees them. Testing it against 20 practice pull requests built on real open-source issues taught me how easy it is to make quality gates too strict.

On the first run, my tool caught every broken pull request in the test set, but it also rejected six out of seven good ones. Digging into the logs showed why: when a developer reorders code cleanly, version-control diffs show lines being removed and re-added, and my first rule treated normal code reorganization as sloppy clutter. Once I updated the rule to tell real cleanup apart from careless churn, accuracy went from 14/20 to 20/20, and I used the calibrated tool to open a fix that passed all six automated checks.

Good developer tools protect maintainers' time without punishing clean refactoring.

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/128 #OpenSource #SoftwareEngineering #DeveloperTools #AI
