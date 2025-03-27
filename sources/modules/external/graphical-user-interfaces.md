## External libraries: Desktop Graphical User Interfaces {#extern-gui}

_Skeleton descriptions are typeset in italic text,_
_so please don't remove these descriptions when editing the topic._

### Overview

_Provides a short natural language abstract of the module’s contents._
_Specifies the different levels of teaching._

------------------------------------------------------------------------
Level             Objective
----------------- ------------------------------------------------------
Foundational:      Basic use of separate compilation

Main:              Command of supporting mechanisms and tools

Advanced:          Technicalities and tools

------------------------------------------------------------------------

### Motivation

_Why is this important?_
_Why do we want to learn/teach this topic?_

For some applications a graphical user interface is beneficial. However, graphical user interfaces are not part of the C++ standard and external libraries are needed. We provide generic teaching goals and do not focus on a specific external library. Some mentionable libraries are: [Qt](https://www.qt.io/), [Cairo](https://www.cairographics.org/), and [wxWidgets](https://www.wxwidgets.org/). 

### Topic introduction

_Very brief introduction to the topic._

### Foundational: Basic use of separate compilation

#### Background/Required Knowledge

A student: 

1. should know linking libraries
2. should know [header files](../compilation-model/headers.md)
3. should know about [build systems](../compilation-model/buildsystems.md)

#### Student outcomes

_A list of things "a student should be able to" after the curriculum._
_The next word should be an action word and testable in an exam._
_Max 5 items._

A student should be able to:

1. link against the external library
2. write an application with one window
3. add simple widgets like text boxes and buttons to the window
4. catch events of buttons or other elements
5. explain the mechanics of event-driven programming

#### Points to cover

* use basic API functions
* layout of the window
* handle events by callbacks  

#### Caveats

_This section mentions subtle points to understand, like anything resulting in
implementation-defined, unspecified, or undefined behavior._

* programming user interfaces in C++ and in general require plenty of boiler plate code  
* API calls are specific to one library and might not be transferable to other libraries
* Some libraries are written in C 

### Main: Command of supporting mechanisms and tools

#### Background/Required Knowledge

* All of the above.

#### Student outcomes

A student should be able to:

1. add more advanced elements, like tables or images
2. handle multiple windows
3. show error and warning popups
4. use different forms of IO, like keyboard, mouse, or touch pad
5. explain basic of fonts and accessibility 

#### Caveats

*  programming user interfaces is rather complex and might not applicable for most students 

#### Points to cover

* More advanced API calls and design patterns

### Advanced: Technicalities and tools

_These are important topics that are not expected to be covered but provide
guidance where one can continue to investigate this topic in more depth._

* Internationalization
* Font designers 
* Hardware acceleration
* Custom widget generation
