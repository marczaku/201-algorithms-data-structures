# Introduction

Imagine, we had a List of 10 Million citizens. And we had to find one specific one from that collection. We know the citizen's name. How long would it take us to find that citizen?

Depends on where in the List the citizen is placed. And from where we start searching. Let's assume that it takes 1ms to look at one entry of the List and compare the entry's name with the name that we're looking for.

If we were to start looking from the front, then either:

1. We're lucky (Best-Case Scenario) and it's the first entry of the list. It would take us 1ms to find him.
2. We're unlucky (Worst-Case Scenario) and it's the last entry of the list. It would take us 10 Million ms to find him.
3. We're averagely lucky (Average-Case Scenario) and it would take us about 5 Million ms to find him.