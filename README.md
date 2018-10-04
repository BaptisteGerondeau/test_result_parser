# test_result_parser
An Ansible Python Module that parses regexable test results to JUnit/XUnit format

## Requirements
The module requires the python module 'junitparser' (https://pypi.org/project/junitparser/).
The 'test' playbook requires rsync to be installed on the localhost, since 'synchronize' makes use of it. 

## Overview

This module relies on regexes to parse the test output, find its parameters, results and store them in the J/XUnit.
This module also decides if the test has passed or not via the TestImpl class function "failed", which return a boolean denoting if the test has failed or not.

Thus, the TestImpl class holds all the logic necessary to parse new tests not made available in this repository, all that is needed is to copy paste and rewrite one of the existing TestImpl classes, leave it in the 'test_models' folder, and at runtime *export this test_models folder to the remote's /tmp*. This step is *necessary* in all cases to make sure the library of TestImpl is always evaluated at runtime (and because of Ansible's _Ansiballz_ mechanism).

## Design

*This module only acts on one target. You will need to fetch the J/XUnit results back from the remote machine to your Jenkins/SQUAD/Whatever instance*
The TestParser class acts as the main controller of the module. It reads the test output, puts it into a string and gives it to the TestFactory, so it can decide which TestImpl corresponds to this test.
The Factory tries to instantiate each model (via ModelLoader) in the "test_models" folder on the remote, and uses the 'check' function of each TestImpl to know if it corresponds.
Once the model is the right one it returns it to TestParser, which asks the model to parse the test output, and makes a JUnit/Xunit representation of it, and then outputs it via a 'formatter' (only XML at the moment).
