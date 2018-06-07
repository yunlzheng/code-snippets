# 行为驱动开发

> 通过统一语言，让开发、测试、需求以及可能能够达成一致。典型的框架如Cucumber

Robotframework Example:

> **data-driven-style** and **behaviour-driven-style**

```
*** Settings ***
Resource         ${RESOURCES}/BDD.txt
 
*** Keyword ***
Example
    [Arguments]  ${periodClosed}  ${periodOpenAndModified}  ${importDay}  ${oldManagerValidUntil}  ${newManagerValidFrom}
 
    Given initialized criteria for bonus commercial
    And a branch B with branch manager M_OLD and employee E1
    And evaluation for E1 for period ${periodClosed} which is closed
    And evaluation for E1 for period ${periodOpenAndModified} which is open and modified
 
    When M_NEW becomes new manager of branch B
    And import service is called on ${importDay}
 
    Then the new branch manager of branch B is M_NEW valid from ${newManagerValidFrom}
    And branch manager M_OLD manages employee E until ${oldManagerValidUntil}
    And branch manager M_NEW manages employee E from ${newManagerValidFrom}
    And Evaluations for E1 still have the same content
 
| *Test Case* | | *Closed Period*        | *Open Period*          | *Run Import On* | *Old Manager Stops* | *New Manager Starts* |
| 1 | Example   | 1.11.2009 - 30.11.2009 | 1.12.2009 - 31.12.2009 | 11.11.2009      | 30.11.2009          |  1.12.2009
| 2 | Example   | 1.11.2009 - 30.11.2009 | 1.12.2009 - 31.12.2009 |  1.11.2009      | 31.10.2009          |  1.11.2009
| 3 | Example   | 1.11.2009 - 30.11.2009 | 1.12.2009 - 31.12.2009 |  1.12.2009      | 30.11.2009          |  1.12.2009
```

```
*** Settings ***
Library          de.codecentric.fourtexx.robot.ModelKeyword
 
*** Keywords ***
the new branch manager of branch ${branch} is ${manager} valid from ${newManagerValidFrom}
    assert branch manager valid from  ${branch}  ${manager}  ${newManagerValidFrom}
```