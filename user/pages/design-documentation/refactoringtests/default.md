---
title: 'Refactoring Tests'
recaptchacontact:
    enabled: false
---

Refactoring Tests is turning out to be complex, so I'm taking notes on all the changes I'm doing to the code base.

First off, I need to edit the test_selection view to be more robust, and to handle all test types.
This also requires editing the getTestSelectionOptions method in the Tests module.

I am going to have to remove all references to "compounds" and replace all references to compounds with tests_all.
I will also need to run checks against test_type_id instead of test_Type.

I will need to seed existing test types ("screen","lcms","reference") into the new data_type_settings table.
So there is quite the to-do list.

Here is the trello task:
https://trello.com/c/Pz0UDTjJ